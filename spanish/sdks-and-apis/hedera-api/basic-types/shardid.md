# ShardID

Each shard has a nonnegative shard number. Each realm within a given shard has a nonnegative realm number (that number might be reused in other shards). And each account, file, and smart contract instance within a given realm has a nonnegative number (which might be reused in other realms). Every account, file, and smart contract instance is within exactly one realm. So a FileID is a triplet of numbers, like 0.1.2 for entity number 2 within realm 1 within shard 0. Each realm maintains a single counter for assigning numbers, so if there is a file with ID 0.1.2, then there won't be an account or smart contract instance with ID 0.1.2.

| Field      | Description                    |
| ---------- | ------------------------------ |
| `shardNum` | the shard number (nonnegative) |
