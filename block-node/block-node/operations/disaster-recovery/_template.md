# DR Playbook: [Scenario name]

> **Summary:** [One sentence - what is broken and what fixes it]

|                      |                                  |
|----------------------|----------------------------------|
| **Playbook ID**      | PB-XX                            |
| **Severity**         | SEV-1 / SEV-2 / SEV-3            |
| **Primary executor** | [Role]                           |
| **Escalate to**      | [Role or channel] if [condition] |

---

## Is this your doc?

Check these without running any commands:

- [Observable symptom 1]
- [Observable symptom 2]

**Not your doc if:**
- [Disqualifying condition] - see PB-XX instead

---

## Before starting

> **Open the incident bridge immediately. Assign:**
> - **Incident commander** - owns resolution, makes go/no-go calls on each phase
> - **Communication lead** - owns public status page and stakeholder updates (not the person executing steps)
>
> Every phase below is executed by the incident commander and designated operators. Communication lead posts updates externally while technical work proceeds in parallel.

---

## Phase 1 - Diagnose

> **Do not skip these before acting:**
> - [Critical action to avoid before diagnosing - e.g., do not restart X before checking Y]
> - [Another dangerous assumption to call out - e.g., do not assume admin actions bypass the failure mode]

| # |                                           Action                                            |                                       Notes                                        | Done |
|---|---------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|------|
| 1 | [First action - check persistence or other critical pre-condition before touching anything] | [Command or config check. **If [condition]: STOP and [consequence].**]             | ☐    |
| 2 | [Next diagnostic step]                                                                      | [Command + what to record]                                                         | ☐    |
| 3 | Classify the failure.                                                                       | **A** - [condition] · **B** - [condition] · **C** - [condition]. Category: _______ | ☐    |

---

## Phase 2 - [Name: Buy Time / Stabilize / etc.]

| # |        Action        |   Notes   | Done |
|---|----------------------|-----------|------|
| 1 | [Stabilization step] | [Command] | ☐    |

---

## Phase 3 - Recover

Identify your failure category from Phase 1 and jump to that section. Complete the first and last steps regardless of category.

### Step 1 - [Pre-condition step] [all categories]

|                  Action                  |   Notes   | Done |
|------------------------------------------|-----------|------|
| [e.g., Preserve logs before any restart] | [Command] | ☐    |

---

### Category A - [Name]

| #  | Action |                      Notes                       | Done |
|----|--------|--------------------------------------------------|------|
| A1 | [Step] | [Command. Failure condition → go to Category X.] | ☐    |

### Category B - [Name]

| #  | Action |   Notes   | Done |
|----|--------|-----------|------|
| B1 | [Step] | [Command] | ☐    |

---

### Final step - [Confirmation step] [all categories]

|                     Action                     |            Notes            | Done |
|------------------------------------------------|-----------------------------|------|
| [e.g., Confirm reconnection / recovery signal] | [Command + expected output] | ☐    |

---

## Phase 4 - Verify and Close

> [One sentence orienting the reader: what should be true at this point, and what this phase confirms]

| # |          Action          |                                  Notes                                  | Done |
|---|--------------------------|-------------------------------------------------------------------------|------|
| 1 | [Verify no data loss]    | [Command + expected output. **Any failure: STOP. Escalate to [role].**] | ☐    |
| 2 | [Verify recovery metric] | [Command + expected value]                                              | ☐    |
| 3 | Update status page.      |                                                                         | ☐    |

---

## Reference

### Key metrics

|     Metric      | Alert condition |
|-----------------|-----------------|
| `[metric_name]` | [When to page]  |

### Pre-incident configuration record

Fill this in before you need it. Every placeholder in this playbook maps to a value below.

- `[CONFIG_PROPERTY]`: _______
- `[SERVICE_UNIT_NAME]`: _______
- `[TOOLS_PATH]`: _______

### Post-incident checklist

After the incident is closed: archive logs, run a post-mortem within 48 hours, and address:

|           Finding            |         Remediation         |
|------------------------------|-----------------------------|
| [What could have gone wrong] | [How to prevent recurrence] |

---

*Playbook version: 1.0 DRAFT*
*Closes: [#ISSUE](https://github.com/hiero-ledger/hiero-block-node/issues/ISSUE)*

[Back to DR Playbook Index](./README.md)
