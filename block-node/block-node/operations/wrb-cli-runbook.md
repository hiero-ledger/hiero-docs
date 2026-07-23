# WRB CLI Operations Runbook

## Overview

This runbook documents the setup and operational workflows for the [Wrapped Record Block](../glossary.md#wrb-wrapped-record-block) (WRB) CLI tools. These tools are used to download, wrap, validate, and maintain block streams for Hiero networks (mainnet and testnet).

**Audience**: Operators running Block Stream validation, migration, or archival processes.

---

## Prerequisites

### Required Tools

```bash
# Install Java 25+
sudo apt-get update
sudo apt-get install openjdk-25-jdk

# Install zstd (required for decompression)
sudo apt-get install zstd

# Verify installations
java -version
zstd --version
```

### Building the Shadow JAR

```bash
# Clone the repository
git clone https://github.com/hiero-ledger/hiero-block-node.git
cd hiero-block-node

# Build the tools shadow jar
./gradlew :tools:shadowJar

# Locate the jar
ls tools-and-tests/tools/build/libs/tools-*-all.jar
```

The shadow jar is self-contained and can be copied to your deployment server.

### Optional: Command Aliases

For convenience, you can set up aliases to simplify command invocations:

```bash
# Add to ~/.bashrc or ~/.bash_profile
alias bntools='java -jar /mnt/wrb-operations/tools-0.35.0-SNAPSHOT-all.jar '

# Common operations with JVM args
alias bntools-large='java -Xmx32g -Xms16g -jar /mnt/wrb-operations/tools-0.35.0-SNAPSHOT-all.jar'

# Then use as:
bntools --network testnet metadata update
bntools-large --network mainnet blocks wrap --input-dir compressedDays
```

**Note**: Commands in this runbook show the full `java -jar` syntax for clarity, but aliases can reduce typing for frequent operations.

---

## Directory Structure

Recommended layout for operations:

```text
/mnt/wrb-operations/
├── tools-<version>-all.jar          # WRB CLI jar
├── metadata/                         # Metadata files
│   ├── block_times.bin               # Block number → timestamp mapping
│   ├── day_blocks.json               # Day → block range mapping
│   └── listingsByDay/                # GCS file listings per day
│       ├── 2024-01-01.json
│       └── ...
├── compressedDays/                   # Downloaded record files (compressed)
│   ├── 2024-01-01/
│   └── ...
└── wrappedBlocks/                    # Wrapped Block Stream output
    ├── addressBookHistory.json       # Address book changes over time
    ├── tss-enablement.bin            # TSS parameters protobuf (if TSS enabled)
    ├── tss-bootstrap-roster.json     # TSS parameters JSON (if TSS enabled)
    ├── 0/                            # Block 0
    ├── 1/                            # Block 1
    └── ...
```

---

## Network Configuration

### GCP Authentication (for GCS access)

Export GCP credentials before running any commands:

```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/gcp-key.json"
export GOOGLE_CLOUD_PROJECT="your-project-id"
export GCP_PROJECT_ID="your-project-id"
```

### Network Selection

All commands accept `--network <mainnet|testnet>`:

- **Mainnet**: `--network mainnet` (default if omitted)
- **Testnet**: `--network testnet`

---

## Core Workflows

### 1. Generate Address Book History

**Purpose**: Fetch address book changes from the Mirror Node API to enable [block proof](../glossary.md#block-proof) verification.

**Command**:

```bash
# Optional: Use nohup for long-running commands to continue after logout
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  mirror generateAddressBook \
  -o wrappedBlocks/addressBookHistory.json \
  --show-changes \
  > addressbook.log 2>&1 &

# Or run directly (foreground):
java -jar tools-<version>-all.jar \
  --network testnet \
  mirror generateAddressBook \
  -o wrappedBlocks/addressBookHistory.json \
  --show-changes
```

**Output**: `wrappedBlocks/addressBookHistory.json` contains all address book updates.

**Notes**:
- Run this before starting wrapping or validation
- Re-run periodically to capture new address book changes

#### Fallback: Generate from Binary File (For Networks Without Mirror Node Backups)

For networks like previewnet where Mirror Node CSV backups are not available, you can generate the address book history from a raw protobuf binary file (file 0.0.102 from the database).

**Command**:

```bash
java -jar tools-<version>-all.jar \
  mirror generateAddressBookFromBin \
  address_book.bin \
  -o wrappedBlocks/addressBookHistory.json
```

**Options**:

```bash
# Use current system time as consensus timestamp
java -jar tools-<version>-all.jar \
  mirror generateAddressBookFromBin \
  address_book.bin \
  -o wrappedBlocks/addressBookHistory.json \
  --use-current-time

# Specify custom consensus timestamp (format: seconds.nanos)
java -jar tools-<version>-all.jar \
  mirror generateAddressBookFromBin \
  address_book.bin \
  -o wrappedBlocks/addressBookHistory.json \
  --timestamp 1234567890.123456789
```

**How to obtain the bin file**:
- Export file 0.0.102 from the network's database
- For previewnet: Contact the network operator for the latest address book export
- The file contains the protobuf-serialized `NodeAddressBook` message

**Output**: Same format as `generateAddressBook` - a JSON file with address book history that can be used for block proof verification.

**When to use**:
- Previewnet or custom networks without Mirror Node CSV exports
- Emergency recovery when Mirror Node is unavailable
- Testing with historical address book snapshots

---

### 2. Update Metadata Files

Metadata files are essential for mapping blocks to days and timestamps.

#### Generate Block Times and Day Blocks

```bash
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  metadata update \
  --listing-dir metadata/listingsByDay \
  > metadata-update.log 2>&1 &
```

**Outputs**:
- `metadata/block_times.bin` - binary map of block numbers to consensus timestamps
- `metadata/day_blocks.json` - JSON map of dates to block ranges

**For bounded updates** (e.g., stopping at a specific date):

```bash
java -jar tools-<version>-all.jar \
  --network testnet \
  metadata update \
  --block-times metadata/block_times.bin \
  --day-blocks metadata/day_blocks.json \
  --end-date 2026-03-13
```

#### Update Day Listings

```bash
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  days updateDayListings \
  --listing-dir metadata/listingsByDay \
  > update-day-listings.log 2>&1 &
```

**What it does**: Queries GCS for all record files per day and caches the listings locally.

**Fix corrupt/missing day**:

```bash
java -jar tools-<version>-all.jar \
  --network testnet \
  days updateDayListings \
  --day 2026-04-03
```

---

### 3. Download Block Data

#### Bulk Historical Download (`download-days-v3`)

**Purpose**: Download compressed record files for a specific date range.

**Command**:

```bash
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  days download-days-v3 \
  --listing-dir metadata/listingsByDay \
  --downloaded-days-dir compressedDays \
  --threads 600 \
  2024 02 01 \
  2026 03 13 \
  > download-days.log 2>&1 &
```

**Arguments**:
- `--listing-dir`: Path to day listings metadata
- `--downloaded-days-dir` (or `-d`): Output directory
- `--threads` (or `-t`): Parallelism (recommend 100-600 based on server capacity)
- Date range: `YYYY MM DD YYYY MM DD` (start and end, inclusive)

**Example - Single Day**:

```bash
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  days download-days-v3 \
  --listing-dir metadata/listingsByDay \
  -d compressedDays \
  -t 100 \
  2026 04 03 \
  2026 04 03 \
  > download-single-day.log 2>&1 &
```

**Monitoring Progress**:

```bash
tail -f download-days.log
```

**Resuming**: The command automatically resumes from where it left off if interrupted.

---

#### Live Streaming Download (`live-sequential`)

**Purpose**: Continuously download, wrap, and validate blocks in near real-time.

**Command**:

```bash
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  days live-sequential \
  -l metadata/listingsByDay \
  -o compressedDays \
  --wrap-output-dir wrappedBlocks \
  --address-book wrappedBlocks/addressBookHistory.json \
  > live-sequential.log 2>&1 &
```

**Arguments**:
- `-l`: Listing directory
- `-o`: Downloaded days output directory
- `--wrap-output-dir`: Wrapped blocks output directory
- `--address-book`: Path to address book history JSON
- `--start-date` (optional): Start from specific date (format: `YYYY-MM-DD`)

**With Start Date**:

```bash
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  days live-sequential \
  -l metadata/listingsByDay \
  -o compressedDays \
  --wrap-output-dir wrappedBlocks \
  --address-book wrappedBlocks/addressBookHistory.json \
  --start-date 2026-03-27 \
  > live-sequential.log 2>&1 &
```

**What it does**:
1. Polls GCS for new record files
2. Downloads them to `compressedDays/`
3. Wraps them into Block Stream format
4. Writes to `wrappedBlocks/`
5. Validates hashes inline

**Monitoring**:

```bash
tail -f live-sequential.log
```

**Graceful Shutdown**:

```bash
# Find the process
ps aux | grep live-sequential

# Send SIGTERM (allows checkpoint save)
kill <PID>

# Avoid SIGKILL unless necessary (loses checkpoint)
```

---

#### Legacy Live Download (`download-live2`)

**Note**: `download-live2` is deprecated in favor of `live-sequential`. Use `live-sequential` for new deployments.

**Command** (for reference):

```bash
nohup java -jar tools-<version>-all.jar \
  --network testnet \
  days download-live2 \
  --listing-dir metadata/listingsByDay \
  --output-dir compressedDays \
  --wrap-output-dir wrappedBlocks \
  --address-book wrappedBlocks/addressBookHistory.json \
  --start-date 2026-03-26 \
  > download-live2.log 2>&1 &
```

---

### 4. Wrap Record Files

**Purpose**: Convert compressed record files into the wrapped Block Stream format.

**Command**:

```bash
nohup java \
  -Xms20g \
  -Xmx20g \
  -XX:+UseZGC \
  -XX:+AlwaysPreTouch \
  -XX:+UseNUMA \
  -XX:ConcGCThreads=12 \
  -jar tools-<version>-all.jar \
  blocks wrap \
  -i compressedDays \
  -o wrappedBlocks \
  -b metadata/block_times.bin \
  -d metadata/day_blocks.json \
  > wrap.log 2>&1 &
```

**Arguments**:
- `-i`: Input directory (compressed days)
- `-o`: Output directory (wrapped blocks)
- `-b`: Block times binary file
- `-d`: Day blocks JSON file

**JVM Flags Explained**:
- `-Xms20g -Xmx20g`: Heap size (adjust based on available RAM)
- `-XX:+UseZGC`: Low-latency GC (recommended for large heaps)
- `-XX:+AlwaysPreTouch`: Pre-allocate heap pages (reduces runtime delays)
- `-XX:+UseNUMA`: NUMA-aware allocation (if hardware supports it)
- `-XX:ConcGCThreads=12`: GC thread count (tune to CPU cores)

**Monitoring**:

```bash
tail -f wrap.log
```

**Resuming**: Wrapping automatically resumes from the last completed block.

**Output Structure**:

```text
wrappedBlocks/
├── 0/
│   ├── 0.blk.gz                 # Block header
│   ├── 0_01.record.gz           # Record file 1
│   ├── 0.rsh                    # Record Stream hash
│   └── 0.blk.footer.gz          # Block footer
├── 1/
├── jumpstart.bin                # Jumpstart integrity data
├── tss-enablement.bin           # TSS parameters (if TSS enabled)
├── tssPublicationHistory.json   # TSS publication checkpoint
└── ...
```

#### Understanding Wrap Output Files

The wrapping process produces several auxiliary files in addition to the wrapped blocks:

##### jumpstart.bin

**Purpose**: Contains integrity verification data for Consensus Nodes performing WRB catch-up.

**Contents**:
- Current block number
- Block hash (SHA-384)
- Consensus timestamp hash (SHA-384)
- Output items tree root hash (SHA-384)
- Streaming hasher state (for incremental hash verification)

**When created**: Generated during `blocks wrap` and `days live-sequential` operations.

**Consumed by**: Consensus Nodes during fast catch-up to verify wrapped block integrity.

**Management**:

Delete `jumpstart.bin` when:
- Regenerating wrapped blocks for a date range (to avoid stale state)
- `live-sequential` reports validation mismatches
- Switching networks or starting fresh wrapping operations
- The file is older than the wrapped blocks it references

**Example - Regenerating after corruption**:

```bash
# Remove stale jumpstart file
rm wrappedBlocks/jumpstart.bin

# Re-run wrapping to generate fresh jumpstart data
java -jar tools.jar blocks wrap -i compressedDays -o wrappedBlocks \
  -b metadata/block_times.bin -d metadata/day_blocks.json
```

**Note**: The jumpstart file is automatically regenerated during wrapping. Deleting it is safe and often necessary when re-wrapping existing block ranges.

---

##### tss-enablement.bin

**Purpose**: Binary [TSS](../glossary.md#tss-hintsts) (Threshold Signature Scheme) parameters file for Block Node verification plugins.

**Contents**:
- Raw protobuf binary of the most recent `LedgerIdPublication` transaction
- TSS public key material
- Ledger ID
- Signature share thresholds

**When created**: Automatically written when the first `LedgerIdPublication` transaction is encountered during wrapping or validation.

**Consumed by**: Block Node's `VerificationServicePlugin` for TSS-based block proof verification.

**Management**:

The file is updated automatically whenever a new TSS publication is detected. You typically do **not** need to manually manage this file unless:
- Switching networks (TSS parameters differ per network)
- Regenerating blocks from scratch (delete to allow fresh detection)

**Example output during wrapping**:

```text
TSS ENABLED: First LedgerIdPublication transaction found at block 12345678
TSS publication at block 12345678: Ledger ID abc123, threshold 1/2, start block 12500000
```

---

##### tssPublicationHistory.json

**Purpose**: JSON checkpoint file tracking all TSS publications discovered during wrapping.

**Contents**:
- Block number and timestamp of each TSS publication
- Ledger ID changes over time
- TSS public key transitions
- Validity ranges (start/end blocks)

**When created**: Saved periodically during wrapping operations as part of validation checkpoints.

**Consumed by**: Resume logic for `blocks wrap` and `live-sequential` to maintain TSS state across restarts.

**Management**:

This file is managed automatically by the validation checkpoint system. It is safe to delete when:
- Starting fresh wrapping operations
- Switching networks
- The corresponding `tss-enablement.bin` has been deleted

**Example structure**:

```json
{
  "publications": [
    {
      "blockNumber": 12345678,
      "timestamp": "2024-01-15T10:30:00Z",
      "ledgerId": "abc123...",
      "threshold": "1/2",
      "startBlock": 12500000
    }
  ]
}
```

---

### 5. Validate Wrapped Blocks

**Purpose**: Verify the integrity of wrapped blocks (hash chain, merkle trees, HBAR supply, balances).

**Full Validation (Mainnet)**:

```bash
nohup java \
  -Xms20g \
  -Xmx20g \
  -XX:+UseZGC \
  -XX:+AlwaysPreTouch \
  -jar tools-<version>-all.jar \
  blocks validate \
  wrappedBlocks/ \
  > validate.log 2>&1 &
```

**Skip Supply Validation** (for testnet or known supply issues):

```bash
nohup java \
  -Xms20g \
  -Xmx20g \
  -XX:+UseZGC \
  -XX:+AlwaysPreTouch \
  -jar tools-<version>-all.jar \
  blocks validate \
  --skip-supply \
  wrappedBlocks/ \
  > validate.log 2>&1 &
```

**Verbose Mode** (detailed output per block):

```bash
nohup java \
  -Xms20g \
  -Xmx20g \
  -XX:+UseZGC \
  -XX:+AlwaysPreTouch \
  -jar tools-<version>-all.jar \
  blocks validate \
  --skip-supply \
  --verbose \
  wrappedBlocks/ \
  > validate-verbose.log 2>&1 &
```

**Available Flags**:

|            Flag             |                       Purpose                        |
|-----------------------------|------------------------------------------------------|
| `--skip-supply`             | Skip HBAR supply validation                          |
| `--skip-signatures`         | Skip block proof signature validation                |
| `--validate-balances=false` | Disable balance tracking validation                  |
| `--verbose`                 | Print detailed per-block output                      |
| `--threads <N>`             | Set parallel validation threads (default: CPU cores) |
| `--prefetch <N>`            | Number of blocks to prefetch (default: 10)           |
| `--no-resume`               | Start from block 0 (ignore saved checkpoint)         |

**Checkpoint Behavior**:
- Validation saves checkpoints periodically to `wrappedBlocks/.validation_checkpoint`
- Resume automatically continues from the last checkpoint
- Graceful shutdown (`kill`) saves checkpoint; `kill -9` does not

**Interpreting Output**:

```text
[INFO] Validated block 1,000,000 in 45.2ms (speed: 2.1x realtime)
[INFO] Hash chain valid up to block 1,000,000
[INFO] Supply: 50,000,000,000 HBAR (expected: 50,000,000,000)
```

- **Speed multiplier**: `2.1x` means validation is 2.1x faster than block production
- **Timing breakdown**: Shows time spent on each validation phase (hashing, signatures, balances)

**Known Issues**:

**Testnet Supply Validation Failure** (Block 7,557,270):
- A SCHEDULESIGN transaction with unbalanced transfer list causes supply mismatch
- **Workaround**: Use `--skip-supply` for testnet validation
- **Root cause**: Testnet Consensus Node bug from HAPI v0.52.0 (August 2024)
- **Reference**: [Testnet Mirror API](https://testnet.mirrornode.hedera.com/api/v1/blocks/7557270)

**TSS Enablement Detection**:

During validation, the CLI automatically detects and extracts TSS (Threshold Signature Scheme) enablement data from `LedgerIdPublication` transactions. When detected, two files are written to the wrapped blocks directory:

**Output Files**:

|            File             |     Format      |                               Purpose                               |
|-----------------------------|-----------------|---------------------------------------------------------------------|
| `tss-enablement.bin`        | Protobuf binary | TssData in protobuf format for archival                             |
| `tss-bootstrap-roster.json` | JSON            | TssData in JSON format matching Block Node's ApplicationStateConfig |

**Example JSON Output** (`tss-bootstrap-roster.json`):

```json
{
  "ledger_id": "0102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1f20",
  "wraps_verification_key": "a1b2c3d4e5f6a7b8c9d0...",
  "current_roster": {
    "roster_entries": [
      {
        "node_id": 0,
        "weight": 1000,
        "schnorr_public_key": "0a1b2c3d4e5f6a7b8c9d0e1f..."
      }
    ]
  },
  "valid_from_block": 42
}
```

**Usage**:
- The JSON file can be directly consumed by the Block Node's `ApplicationStateConfig`
- Both files are updated automatically on each TSS publication detection
- Validation output shows: `TSS publication at block <N>: <description>`

---

### 6. Repair Corrupt or Missing Files

**Purpose**: Re-download individual corrupt record files.

**Command**:

```bash
java -jar tools-<version>-all.jar \
  --network testnet \
  blocks repair-zips \
  --input compressedDays \
  --listing-dir metadata/listingsByDay
```

**What it does**:
- Scans `compressedDays/` for corrupt or incomplete `.zip` files
- Re-downloads them from GCS using the day listings
- Verifies file integrity after download

**Use case**: When `download-days-v3` or `wrap` reports file corruption errors.

---

## Operational Notes

### Monitoring Progress

**Active Processes**:

```bash
ps aux | grep java | grep tools
```

**Log Tailing**:

```bash
tail -f <operation>.log
```

**Disk Usage**:

```bash
du -sh compressedDays/ wrappedBlocks/ metadata/
```

**Block Count**:

```bash
ls -d wrappedBlocks/*/ | wc -l
```

### Disk Space Requirements

**Recommendations**:
- Use ZFS with compression (ratio ~1.5x for wrapped blocks)
- Separate volumes for compressed days and wrapped blocks
- Monitor disk usage during bulk downloads

### Graceful Shutdown

**SIGINT (recommended)**:

```bash
kill -INT <PID>
```

- Allows checkpoint save
- Cleanly closes file handles
- Safe for resume

**SIGKILL (avoid)**:

```bash
kill -9 <PID>
```

- Loses checkpoint state
- May corrupt in-progress writes
- Use only if process is unresponsive

### Running with `nohup`

**Pattern**:

```bash
nohup <command> > output.log 2>&1 &
```

- `nohup`: Prevents termination on terminal disconnect
- `> output.log`: Redirect stdout to log file
- `2>&1`: Redirect stderr to stdout (same log)
- `&`: Run in background

**Check Job**:

```bash
jobs -l
```

**Bring to Foreground** (if needed):

```bash
fg %1
```

---

## Troubleshooting

### "Unknown file type in GCS bucket"

**Symptom**: Download fails with unknown file extension error.

**Cause**: GCS contains non-record files (e.g., `.tmp`, `.lock`).

**Solution**: Re-run `days updateDayListings` for the affected day to refresh the file list.

### "HBAR supply mismatch"

**Symptom**: Validation fails at specific block with supply error.

**Cause**: Consensus Node bug or testnet-specific issue.

**Solution**: Use `--skip-supply` flag. Report issue if on mainnet.

### "Block proof signature verification failed"

**Symptom**: Signature validation fails.

**Cause**: Missing or outdated address book history.

**Solution**:
1. Re-generate address book: `mirror generateAddressBook`
2. Use `--skip-signatures` as temporary workaround

### "Out of memory" during validation

**Symptom**: JVM crashes with `OutOfMemoryError`.

**Solution**:
1. Increase heap: `-Xms32g -Xmx32g`
2. Reduce prefetch: `--prefetch 5`
3. Reduce threads: `--threads 8`

### Download stalls or times out

**Symptom**: `download-days-v3` hangs or reports timeout.

**Solution**:
1. Reduce threads: `--threads 100` (instead of 600)
2. Check network connectivity to GCS
3. Verify GCP credentials are valid

### Validation mismatches with stale jumpstart.bin

**Symptom**: `live-sequential` or validation reports hash mismatches or integrity errors.

**Cause**: Stale `jumpstart.bin` file from previous wrapping operations.

**Solution**:

```bash
# Delete stale jumpstart file
rm wrappedBlocks/jumpstart.bin

# Optionally delete TSS files if switching networks
rm wrappedBlocks/tss-enablement.bin
rm wrappedBlocks/tssPublicationHistory.json

# Re-run wrapping or validation
java -jar tools.jar blocks wrap -i compressedDays -o wrappedBlocks \
  -b metadata/block_times.bin -d metadata/day_blocks.json
```

**When this happens**:
- After regenerating blocks for a date range that overlaps with existing wrapped blocks
- When switching between networks (mainnet/testnet)
- If wrapping was interrupted and restarted from a different starting block

---

## Quick Reference

### Common Command Patterns

**Mainnet Full Pipeline**:

```bash
# 1. Setup metadata
java -jar tools.jar metadata update --listing-dir metadata/listingsByDay

# 2. Generate address book
java -jar tools.jar mirror generateAddressBook -o wrappedBlocks/addressBookHistory.json

# 3. Download bulk history
java -jar tools.jar days download-days-v3 -l metadata/listingsByDay -d compressedDays -t 600 2019 09 01 2026 04 30

# 4. Wrap blocks
java -Xms20g -Xmx20g -XX:+UseZGC -jar tools.jar blocks wrap -i compressedDays -o wrappedBlocks -b metadata/block_times.bin -d metadata/day_blocks.json

# 5. Validate
java -Xms20g -Xmx20g -XX:+UseZGC -jar tools.jar blocks validate wrappedBlocks/

# 6. Live streaming
java -jar tools.jar days live-sequential -l metadata/listingsByDay -o compressedDays --wrap-output-dir wrappedBlocks --address-book wrappedBlocks/addressBookHistory.json
```

**Testnet Full Pipeline** (with `--skip-supply`):

```bash
# Same as mainnet, but add --network testnet and --skip-supply to validate:
java -Xms20g -Xmx20g -XX:+UseZGC -jar tools.jar --network testnet blocks validate --skip-supply wrappedBlocks/
```

---

## Appendix: Real-World Examples

### Mainnet Production Setup

**Hardware**:
- 32-core server
- 128GB RAM
- 20TB ZFS RAID10 SSD array

**Commands**:

```bash
# Update metadata (daily cron)
0 2 * * * cd /mnt/wrb && java -jar tools.jar days updateDayListings --listing-dir metadata/listingsByDay > update.log 2>&1

# Live streaming (systemd service)
nohup java -jar tools.jar days live-sequential -l metadata/listingsByDay -o compressedDays --wrap-output-dir wrappedBlocks --address-book wrappedBlocks/addressBookHistory.json > live.log 2>&1 &

# Weekly full validation (Sunday 00:00)
0 0 * * 0 cd /mnt/wrb && java -Xms64g -Xmx64g -XX:+UseZGC -jar tools.jar blocks validate wrappedBlocks/ > validate-$(date +\%Y\%m\%d).log 2>&1
```

### Testnet CI/CD Validation

**Purpose**: Nightly validation of testnet wrapped blocks.

```bash
#!/bin/bash
# testnet-validate.sh

set -euo pipefail

JAR="/opt/wrb/tools.jar"
BLOCKS="/data/testnet/wrappedBlocks"

java -Xms20g -Xmx20g -XX:+UseZGC \
  -jar "$JAR" \
  --network testnet \
  blocks validate \
  --skip-supply \
  "$BLOCKS" > "/var/log/wrb/validate-$(date +%Y%m%d).log" 2>&1

if [ $? -eq 0 ]; then
  echo "Validation PASSED" | tee -a /var/log/wrb/status.log
else
  echo "Validation FAILED" | tee -a /var/log/wrb/status.log
  exit 1
fi
```

---
