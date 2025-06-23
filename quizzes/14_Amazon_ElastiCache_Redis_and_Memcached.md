Architecture & Core Concepts
Q: What is Amazon ElastiCache and what need does it address?
A: Amazon ElastiCache is a managed in-memory cache service supporting Redis and Memcached. It improves application performance by reducing database load and latency.

Q: Compare Redis and Memcached in ElastiCache.
A:

Redis is single-threaded, durable (optional persistence), supports data structures, replication, clustering, and Pub/Sub.

Memcached is multi-threaded, ephemeral (no persistence), simpler key-value only, client-side scaling, no replication.

Scaling & Clustering (Redis)
Q: How does ElastiCache for Redis clustering work?
A: You shard keys across primary nodes in multiple shards; each shard can have replicas for high availability and failover.

Q: Explain adding or removing shards ("resizing cluster").
A: Modify operation to scale shards using “Online Cluster Resize.” Redis will re-distribute slot ownership and data with minimal downtime.

Q: What happens during a primary node failure in a Redis cluster?
A: A replica is promoted to primary; DNS record updates; cluster-reconfiguration occurs. Failover time ~1–2 minutes.

Replication & Failover
Q: Define ElastiCache replication groups.
A: For Redis, replication groups encapsulate a primary and one or more read replicas for high availability.

Q: Sync vs async replication mechanisms?
A: ElastiCache uses asynchronous replication—primaries replicate updates to replicas with potential replication lag.

Q: What is Multi-AZ with Auto-Failover?
A: Redis configuration with at least one replica in another AZ. If primary AZ fails, automatic failover occurs to replica.

Persistence & Backup
Q: Can ElastiCache for Redis persist data to disk?
A: Yes—by enabling AOF and/or RDB snapshots. Snapshots can be stored in S3 for recovery.

Q: How do backups work in ElastiCache?
A: On-demand or scheduled snapshots of Redis clusters; stored in S3. Use them to restore new clusters.

Q: What are the trade-offs of AOF vs snapshot persistence?
A:

AOF provides higher durability (every write is logged), but can be slower and bigger.

RDB snapshots are compact but risk more data loss since last snapshot.

Security & Networking
Q: Describe encryption options in transit and at rest.
A: At rest encryption via AWS KMS. In-transit using TLS.

Q: How do you restrict access to ElastiCache?
A: Use VPC security groups, Redis AUTH, TLS, IAM (for control-plane), and subnet groups.

Q: Can ElastiCache be accessed over the public Internet?
A: No—must be in VPC; requires VPN/Direct Connect to access from on-prem.

Performance & Monitoring
Q: How to monitor ElastiCache performance?
A: Use CloudWatch metrics: CPUUtilization, CurrConnections, Evictions, EngineCPUUtilization, ReplicationLag.

Q: Explain cache misses and how to reduce them.
A: Misses occur when key not found. Use warm-up scripts or cache pre-population (lazy vs write-through).

Q: What is eviction policy? Why is it important?
A: Defines how Redis handles memory full: LRU, LFU, TTL-based, no-eviction. Critical to avoid errors/performance issues under memory pressure.

Q: How to fine-tune parameter groups for production?
A: Adjust TTL, maxclients, eviction policies, memory fragmentation, save intervals, AOF rewrite thresholds.

Advanced Topics
Q: Describe Redis replication topology in ElastiCache.
A: Primary <--> replica(s), with replica distribution across AZs; optionally nested replication for fan-out.

Q: How to achieve strong consistency in reads?
A: Always read from primary; replicas are eventually consistent and may lag.

Q: Redis support for atomic operations?
A: Yes—single-threaded architecture ensures atomicity of commands; use MULTI/EXEC for transactions.

Q: How does pipelining improve throughput?
A: Batches multiple commands in one request/response, reducing network latency impact.

Q: What is Redis engine version upgrades process?
A: Use “Modify” operation to select new engine version; perform rolling upgrade with auto-failover.

Q: How to back up encrypted data and restore it?
A: Use snapshot with CMK; create new cluster with the same CMK to restore encrypted data.

Q: Describe cross-region replication for Redis.
A: Use Global Datastore—secondary clusters in other regions for disaster recovery and read-scaling.

Q: What is Global Datastore failover behavior?
A: Promote secondary to primary in DR region; may take several minutes. Applications need endpoint swap.

Q: Differences between tiered replication and cluster mode?
A: Cluster mode shards data; tiered replication (multi-node group) duplicates entire data-set without sharding.

Q: How to debug high memory fragmentation?
A: Inspect free memory fragmentation via INFO memory. Use redis-forked-aof-rewrite-percentage, defrag settings and monitor.

Q: Best practices for Redis key expiration?
A: Use TTLs carefully; avoid mass expiration (which can stall eviction); spread expirations over time.

Q: How to conduct capacity planning for Redis?
A: Estimate working set size + overhead (metadata, fragmentation). Add 20–30% buffer.

Q: How to implement rate limiting with Redis?
A: Use Lua scripts or atomic INCR + EXPIRE to enforce per-second thresholds.

Q: How to implement distributed locks?
A: Use SETNX + expire (Redlock pattern) with careful handling of retries and clock skew.

Q: Explain Redis Pub/Sub in ElastiCache.
A: Works within cluster. Use client connections for SUBSCRIBE / PUBLISH. Note no persistence.

Q: Can Redis Streams be used on ElastiCache?
A: Yes, Redis 5+ supports Streams with Caveats—persistence and memory consumption.

Q: How to backup and restore snapshots across regions?
A: Copy snapshot to S3 in target region, then restore.

Q: What are parameter groups?
A: Container for Redis engine settings. Apply to nodes; requires reboot.

Q: What are subnet groups?
A: Defines VPC subnets for node placement.

Q: Data migration strategies to ElastiCache?
A: For Redis: use DUMP/RESTORE, redis-cli MIGRATE, or replication syncing from self-managed to ElastiCache.

Q: Explain the “slot hash” in Redis Cluster.
A: Redis divides key-space into 16,384 hash slots. Shards own subsets of slots.

Q: How to configure maintenance windows?
A: Define weekly window when AWS can apply patches/updates.

Q: My cluster shows high swap usage. What to do?
A: Investigate cache size, eviction policy, memory fragmentation, reduce working set, scale up.

Q: Redis vs DynamoDB Accelerator (DAX) when to use each?
A: Redis for general-purpose caching, clustering, pub/sub, multi-data-structure. DAX is fully managed in-memory cache tightly integrated with DynamoDB, only for DynamoDB workloads.

Q: How to identify hot keys?
A: Analyze CloudWatch metrics like “OldestCachedItemAge” or monitor key access patterns outside.

Q: How do shards rebalance on scaling?
A: AWS adds or removes shards and migrates hash slots in background with slot ownership transfers.

Q: Explain parameter maxmemory-policy.
A: Defines eviction behavior (volatile‑lru, allkeys‑lru, no‑eviction, etc.).

Q: How to configure snapshots for encrypted clusters?
A: Same schedule; snapshot is encrypted using the KMS key.

Q: How to test failover processes?
A: Use RebootCacheCluster on primary with Failover option or simulate AZ failure; monitor promotion.

Q: What is client-side sharding for Memcached?
A: Application (e.g. Elasticache SDK) hashes key to specific node; scales by adding/removing nodes.

Q: How to scale Memcached cluster?
A: Modify cluster to change node count; it’s client-driven key distribution.

Q: Compare costs for Redis vs Memcached.
A: Redis is typically costlier per node due to single-threaded deployment and HA options; Memcached scales horizontally with smaller instance sizes, often more cost-effective for simple workloads.
