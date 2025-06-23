Q: What is DAX and why use it?
A: DAX is a fully managed, in-memory caching layer for DynamoDB that offloads read traffic and reduces latency from tens of ms to single-digit ms.

Q: How does DAX integrate with DynamoDB SDK?
A: You point your SDK at the DAX cluster endpoint; it transparently handles cache lookup, invalidation, and DynamoDB fetch on miss.

Q: What types of operations does DAX accelerate?
A: GetItem, BatchGetItem, Query (read-heavy); does not accelerate writes or scan operations.

Architecture & Consistency
Q: Deep dive: DAX cluster architecture?
A: Composed of one or more nodes across AZs. One acts as coordinator; others serve reads; auto-replicated. Consistent hashing used.

Q: How does DAX ensure cache consistency?
A: Uses write-through: writes and updates invalidate cached items. TTL on cached entries.

Q: Is DAX strongly or eventually consistent?
A: Always eventually consistent with default. Can make strongly consistent reads, but latency increases.

Q: What about write-through vs write-around caching in DAX?
A: DAX uses write-through for atomic writes/invalidation; no write-around.

Scaling & Failover
Q: How to scale a DAX cluster?
A: Modify cluster and increase/decrease node count. DAX handles replication and synchronization.

Q: How does DAX failover work?
A: Coordinator failure triggers failover to another node; client reconnects.

Q: Is there multi-region support?
A: Not built-in. Use app-level logic or DynamoDB Global Tables.

Caching Behavior
Q: What is default TTL for DAX?
A: 5 minutes. Configurable between 1 second and 365 days.

Q: How to perform cache warming for DAX?
A: Pre-populate keys via DAX client after cluster setup or use preload mechanisms.

Q: Handling of batch reads?
A: DAX caches as individual items; BatchGet triggers cache lookup per item.

Security
Q: What encryption options does DAX provide?
A: In-transit TLS. At-rest encryption is through underlying DynamoDB.

Q: How to control access to DAX?
A: VPC-based with security groups; IAM controls creation/modification only, no attach-level IAM.

Q: Can DAX be accessed from on-prem?
A: Connect via VPN or Direct Connect into VPC.

Monitoring & Performance
Q: What CloudWatch metrics does DAX emit?
A: CacheHits, CacheMisses, HitRate, SuccessfulRequestLatency, Errors, ThrottledRequests.

Q: How to interpret DAX effectiveness?
A: High hit rate (>90%) and low latency indicate good usage.

Q: Monitoring cache invalidation patterns?
A: Track TTL expiry, track keys with writes to identify hot invalidations.

Q: How to troubleshoot high miss rates?
A: Look at request patterns, TTLs, application access; possibly pre-warm.

Q: DAX vs ElastiCache for caching DynamoDB?
A: DAX is specialized, turnkey for DynamoDB. ElastiCache offers more features but requires custom integration.

Advanced Use Cases
Q: How to ensure atomicity in DAX operations?
A: Writes are atomic; reading stale data possible until invalidation completes.

Q: How to handle cache stampede?
A: Use jittered retry, back-off logic, pre-warm; DAX lacks locking primitives.

Q: DAX cluster deletion process?
A: Delete cluster via API/Console. Purges cache; DynamoDB tables remain unaffected.

Q: Upgrading DAX node types?
A: Modify node type; replacement involves creating new nodes and seamlessly switching.

Q: When to use DAX over ElastiCache?
A: When primarily caching reads against DynamoDB and wanting minimal integration work.

Q: Does DAX support transacted writes?
A: No; it supports standard DynamoDB transactions (TransactWriteItems) with cache invalidation after success.

Q: DAX limits on cluster size?
A: Minimum 3 nodes; max: current soft limit ~10 per cluster (subject to AWS account limits).

Q: DAX pricing model?
A: Charged per node-hour and data transfer; no per-request cost.

Q: How to back up DAX cache?
A: No snapshots. Cache is ephemeral and rebuilds on restart.

Q: What failure scenarios should be tested?
A: Node failover, network partition, cluster restart, TTL expiring mid-load.

Q: DAX vs DynamoDB On-demand backups?
A: DAX doesn’t participate in backups; it’s a volatile cache. DynamoDB backups are separate.

Q: Effect of DynamoDB table schema changes?
A: DAX continues; but cache contents may no longer correspond. Best to flush cache items that rely on schema.

Q: How do you scale-down DAX nodes safely?
A: Modify cluster to reduce nodes, ideally during low traffic; it drains replicas.

Q: Does DAX support VPC endpoint policies?
A: No. Use security groups; IAM is only for control-plane.

Q: DAX and DynamoDB encryption at rest: how aligned?
A: DAX defers to table’s encryption; cache does not require separate encryption.

Q: Cache entry expiry behavior under load?
A: TTLs enforced lazily; expired entries removed on access.

Q: How to do cache invalidation on global table updates?
A: DAX cluster uses coordinator invalidation; cross-region writes don’t sync—app needs to handle.

Q: How to evaluate DAX ROI?
A: Compare reduced RCU/WCU cost and latency improvements against node-hour charges.

Q: Common pitfalls for DAX?
A: Misconfigured SDK endpoints, insufficient warm-up, no metrics monitoring.

Q: DAX and Application Auto Scaling?
A: Not supported. Nodes are modified manually via API/CLI.

Q: What AWS regions support DAX?
A: Most, but only in specific AWS commercial regions; check console for availability.

Q: How to estimate DAX cluster capacity?
A: Use peak request rates and dataset working set size; memory available per node type ~85%.

Q: Impact of TTL misconfiguration?
A: Too short → cache churn; too long → stale data.

Q: How to flush DAX cache?
A: Delete and recreate cluster or call FlushCluster, which invalidates all entries.

Q: DAX cluster naming conventions?
A: Must be unique per AZ; use DNS‑compatible names.

Q: Threading model?
A: DAX nodes are multi-threaded, optimized for network and request handling.

Q: How are DAX nodes maintained?
A: AWS pushes patches during maintenance windows; rolling updates.

Q: Data lease duration and refresh?
A: TTL determines lease; no automatic refresh; client can re-read.

Q: DAX’s behavior under DynamoDB throttle events?
A: DAX bypasses cache, forwards to DynamoDB; throttles propagate and may cause misses.
