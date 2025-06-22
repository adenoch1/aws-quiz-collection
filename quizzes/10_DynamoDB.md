## AWS DynamoDB Interview Questions & Detailed Answers

### Table of Contents

1. **Fundamentals** (Questions 1–15)
2. **Data Modeling & Design** (Questions 16–40)
3. **Partitioning & Performance** (Questions 41–60)
4. **Indexes** (Questions 61–75)
5. **Transactions & ACID** (Questions 76–85)
6. **Streams & Lambda Integration** (Questions 86–95)
7. **Security & Encryption** (Questions 96–105)
8. **Monitoring, Troubleshooting & Best Practices** (Questions 106–120)

---

### 1. Fundamentals

1. **What is Amazon DynamoDB?**
   **Answer:** DynamoDB is a fully managed NoSQL key-value and document database service providing single-digit millisecond performance at any scale. It automatically scales throughput capacity and storage, manages replication across multiple AZs, and offers built-in security, backup, and restore.

2. **Explain the difference between key-value and document models in DynamoDB.**
   **Answer:** The key-value model uses primary keys to store items as opaque blobs. The document model allows items to have complex nested attributes (JSON), enabling richer querying and indexing.

3. **What are primary keys in DynamoDB?**
   **Answer:** A primary key uniquely identifies each item in a table and can be:

   * **Partition key only (simple primary key):** A single attribute whose value is hashed for partition placement.
   * **Partition key + sort key (composite primary key):** Two attributes; the partition key for distribution and sort key for item ordering within the partition.

4. **How does DynamoDB ensure durability and availability?**
   **Answer:** DynamoDB synchronously replicates item data across three AZs in a region. This multi-AZ replication ensures high availability and data durability.

5. **What is eventual consistency vs. strong consistency?**
   **Answer:**

   * **Eventual consistency (default):** Read might return stale data, but updates propagate within seconds.
   * **Strong consistency:** Read reflects all writes up to the moment of the read, ensuring the latest data at the cost of higher latency and slightly reduced availability.

6. **Describe DynamoDB storage billing.**
   **Answer:** Billing includes charges for read/write capacity units (provisioned mode), on-demand read/write requests (on-demand mode), storage per GB, DynamoDB Streams read costs, backups, and optional features like global tables or Accelerator.

7. **What are DynamoDB Streams?**
   **Answer:** Streams capture a time-ordered sequence of item-level modifications (insert, update, delete), retaining data for 24 hours, enabling triggers via AWS Lambda or replication between tables.

8. **How do you create a table in DynamoDB?**
   **Answer:** Use AWS Console, AWS CLI (`aws dynamodb create-table`), AWS SDKs, or CloudFormation/Terraform templates, specifying table name, primary key schema, provisioned throughput or on-demand mode, and optional secondary indexes.

9. **What are the differences between provisioned and on-demand capacity modes?**
   **Answer:**

   * **Provisioned:** Pre-allocate read/write capacity units; cost-effective for predictable workloads; requires capacity planning.
   * **On-demand:** Pay per request; automatic scaling for unpredictable or spiky workloads; higher per-request cost.

10. **Can you change the primary key of an existing table?**
    **Answer:** No. Changing a primary key requires creating a new table, migrating data (using AWS Data Pipeline, DMS, or custom scripts), and switching application endpoints.

11. **What is Time to Live (TTL) in DynamoDB?**
    **Answer:** TTL allows automatic deletion of items after a specified timestamp attribute. Expired items are deleted asynchronously, reducing storage costs and enabling data lifecycle management.

12. **How does DynamoDB handle large items?**
    **Answer:** Maximum item size is 400 KB. For larger payloads, use Amazon S3 for the bulk data and store pointers or metadata in DynamoDB.

13. **What is DynamoDB Accelerator (DAX)?**
    **Answer:** DAX is an in-memory caching layer for DynamoDB offering microsecond response times. It is API-compatible with DynamoDB and requires minimal application changes.

14. **Explain Global Tables.**
    **Answer:** Global Tables provide multi-region, fully replicated tables offering low-latency reads and writes across AWS Regions. It leverages DynamoDB Streams to replicate changes.

15. **What are conditional writes in DynamoDB?**
    **Answer:** Conditional writes use expressions to ensure writes (put, update, delete) only succeed if specified conditions are met, preventing race conditions and enabling optimistic concurrency control.

---

### 2. Data Modeling & Design

16. **What is a hot partition and how can you avoid it?**
    **Answer:** A hot partition occurs when a disproportionate number of read/write operations target a single partition key, exceeding its throughput. Avoid by designing keys with high cardinality and even access distribution (e.g., use random suffixes or timestamps).

17. **How would you model a one-to-many relationship in DynamoDB?**
    **Answer:** Use a composite key: partition key for the `parent ID`, sort key for `child ID` or `timestamp`. Query by partition key to fetch all children.

18. **Explain adjacency list vs. materialized path for hierarchical data.**
    **Answer:**

* **Adjacency list:** Each item stores a pointer/ID to its parent. Requires multiple queries to traverse.
* **Materialized path:** Each item stores the full path (e.g., `/root/parent/child`). Enables single-query retrieval using begins\_with on the sort key.

19. **How do you implement many-to-many relationships?**
    **Answer:** Create a join table where each item’s PK is a composite of the two entity IDs. Use GSIs to query by either entity.

20. **How would you store time-series data in DynamoDB?**
    **Answer:** Use a partition key combining a fixed entity ID and time bucket (e.g., hourly/day); sort key for exact timestamp. This ensures write distribution and efficient range queries.

21. **What is single-table design and what are its benefits?**
    **Answer:** Single-table design stores multiple entity types in one table, leveraging composite keys and GSIs to support different access patterns. Benefits include fewer tables to manage, reduced cross-table transactions, improved efficiency.

22. **When is multi-table design preferable?**
    **Answer:** For very distinct, unrelated data with no common access patterns or low-scale use where simplicity outweighs performance complexity.

23. **Describe how to use GSIs and LSIs in data modeling.**
    **Answer:**

* **GSI:** Global index with alternate partition and optional sort key; allows different query patterns; throughput is separate.
* **LSI:** Local index using same partition key and alternate sort key; queries within partitions; must be defined at table creation; shares throughput.

24. **How do you manage attribute projection for indexes?**
    **Answer:** Choose `ALL`, `KEYS_ONLY`, or `INCLUDE` with specific non-key attributes to optimize storage and read efficiency.

25. **Explain design patterns like item collection vs. sparse index.**
    **Answer:**

* **Item collection:** Group related items by PK, sort key to filter by SK prefix.
* **Sparse index:** Only items with certain attributes are projected, enabling efficient querying of subsets.

26. **How do you model fan-out notifications (e.g., push to many subscribers)?**
    **Answer:** Prestore subscription records in a table. On publish, batch fetch subscriber list, then parallel write notifications. Alternatively, fan-out via Streams to Lambda.

27. **What’s the significance of attribute name and value types?**
    **Answer:** DynamoDB uses type definitions (S, N, B, BOOL, L, M) to validate and store attributes. Correct typing ensures proper indexing and efficient storage.

28. **How would you store binary data?**
    **Answer:** Use the Binary (B) data type for small blobs (<=400 KB) or store in S3 and reference via URL for larger data.

29. **Explain the use of map and list data types.**
    **Answer:**

* **List:** Ordered collection; accessed by index.
* **Map:** Unordered key-value pairs; enables nested JSON-like structures.

30. **How to avoid large item size hotspots?**
    **Answer:** Use pointer attributes to external storage, divide large arrays/maps into smaller items, or store deltas rather than full payloads.

31. **Describe how you would version items.**
    **Answer:** Include a version attribute or use conditional writes with `Version` increment on each update. Or maintain a separate history table.

32. **How would you handle read-heavy workloads differently than write-heavy?**
    **Answer:** For read-heavy, consider DAX caching, on-demand mode, or storing pre-aggregated data. For write-heavy, optimize for batch writes, provision adequate write capacity, and design HOT key avoidance.

33. **What is denormalization in DynamoDB, and why is it used?**
    **Answer:** Denormalization duplicates data across items to support query patterns without joins. It reduces read complexity at the cost of additional write maintenance logic.

34. **How do you model geospatial queries?**
    **Answer:** Use geohashing libraries (e.g., AWS Geo Library for DynamoDB) to encode lat/long into zone-based partition keys and range queries.

35. **Explain the significance of attribute size on capacity.**
    **Answer:** Read/write capacity units are based on item size. Larger items consume more RCUs/WCUs: e.g., one strongly consistent read of up to 4 KB costs one RCU.

36. **How would you store hierarchical permissions per user?**
    **Answer:** Use a map attribute for entitlement structure or a separate permissions table keyed by user ID and resource ID with assignment entries.

37. **Describe the anti-patterns to avoid in data modeling.**
    **Answer:**

* Scatter-gather queries requiring table scans.
* Hot keys.
* Excessive GSIs.
* Large item sizes.
* Deep nesting causing complex updates.

38. **How do you plan capacity for unpredictable workloads?**
    **Answer:** Use on-demand mode or implement auto-scaling policies on provisioned tables to react to usage metrics.

39. **What are adaptive capacity features?**
    **Answer:** Automatic reallocation of RCUs/WCUs to partitions with increased activity, smoothing over hot keys without manual intervention.

40. **How would you perform data migrations between tables or regions?**
    **Answer:** Use AWS Data Pipeline, AWS DMS, DynamoDB Streams with Lambda replication, or custom scripts with parallel scanning and bulk writes.

---

### 3. Partitioning & Performance (Questions 41–60)

41. **How do partition keys affect data distribution?**
    **Answer:** The partition (hash) key is hashed to determine the physical partition. Uniform key distribution ensures balanced shard load and throughput utilization.

42. **What is the maximum read/write throughput per partition?**
    **Answer:** Each partition can support up to 3,000 RCU or 1,000 WCU (strongly consistent reads/write) per second; exceeding these causes throttling.

43. **How does DynamoDB auto-scaling work under the hood?**
    **Answer:** Auto-scaling uses CloudWatch metrics to adjust provisioned capacity within defined min/max thresholds, reacting to utilization percentages to avoid throttling.

44. **What is adaptive capacity and how does it mitigate hot keys?**
    **Answer:** Adaptive capacity reallocates unused throughput from cold partitions to hot ones in real time, reducing throttling without manual repartitioning.

45. **Explain read/write capacity units (RCUs/WCUs) calculations.**
    **Answer:**

* One RCU = one strongly consistent read of up to 4 KB per second, or two eventually consistent reads.
* One WCU = one write of up to 1 KB per second.

46. **How do you calculate item size for capacity planning?**
    **Answer:** Sum byte-size of attribute names and values (S as length, N as string length, etc.) including overhead, then round up to nearest KB for capacity unit calculation.

47. **What is read/write throttling and how do you handle it?**
    **Answer:** Throttling occurs when requests exceed provisioned capacity. Handle by implementing exponential backoff/retries, auto-scaling, or migrating to on-demand mode.

48. **How can you optimize batch operations?**
    **Answer:** Use BatchGetItem/BatchWriteItem, splitting into batches of 25 items or less, parallelize requests, and implement retry logic for unprocessed items.

49. **What are best practices for large scan operations?**
    **Answer:** Prefer Query over Scan; if Scan is necessary, use pagination, filter expressions, parallel scans with segmented reads, and limit projections.

50. **How does DynamoDB handle network latency?**
    **Answer:** Keep data close to your consumers using Global Tables or AWS Local Zones; use DAX for in-memory caching to reduce round-trip times.

51. **What is DynamoDB Accelerator (DAX), and when should you use it?**
    **Answer:** DAX is an in-memory cache that delivers microsecond read performance; use for read-heavy, latency-sensitive workloads where dataset fits in cache.

52. **How do you monitor DynamoDB performance?**
    **Answer:** Use CloudWatch metrics (ConsumedCapacity, ThrottledRequests, Latency, ReadThrottleEvents), enable Contributor Insights, and set alarms.

53. **What effect do large attribute projections have on performance?**
    **Answer:** Larger projected attributes increase item size, consuming more RCUs and network bandwidth, leading to higher latency and costs.

54. **Explain DynamoDB’s soft vs. hard limits.**
    **Answer:** Soft limits (e.g., number of tables per account) can be increased via support; hard limits (e.g., item size 400 KB, partition limits) are fixed.

55. **What is a parallel scan, and what are the caveats?**
    **Answer:** Divides a Scan into segments processed concurrently. Caveats: uneven data distribution leads to skewed load and potential throttling.

56. **How do you choose between on-demand and provisioned modes?**
    **Answer:** Use on-demand for unpredictable/spiky workloads; provisioned for stable, predictable patterns with cost efficiencies and capacity control.

57. **How can you pre-warm a table to avoid cold start throttling?**
    **Answer:** Use a script to perform dummy reads/writes to all partitions before production traffic peaks, especially after a large capacity increase.

58. **What is the impact of large numbers of GSIs on performance?**
    **Answer:** Each GSI consumes write capacity for every base table write and increases storage; excessive GSIs can throttle writes and increase costs.

59. **How do you measure the efficiency of your data access patterns?**
    **Answer:** Track RequestUnitsConsumed per operation, monitor latency percentiles, and review CloudWatch metrics to identify hotspots or inefficiencies.

60. **What are DynamoDB On-Demand backup and Restore performance considerations?**
    **Answer:** Backups operate online without impacting performance; restoring large tables can take significant time and may need capacity adjustments.

---

### 4. Indexes (Questions 61–75)

61. **What is a Global Secondary Index (GSI)?**
    **Answer:** A GSI is an index with an alternate partition (and optional sort) key. It enables querying by non-primary key attributes and has its own throughput and storage.

62. **What is a Local Secondary Index (LSI)?**
    **Answer:** An LSI uses the same partition key as the base table but a different sort key. It must be defined at table creation and shares provisioned throughput with the base table.

63. **When would you choose an LSI over a GSI?**
    **Answer:** When you need to query alternative sort orders on the same partition key and require strong consistency on the index.

64. **How many GSIs/LSIs can you create per table?**
    **Answer:** Up to 20 GSIs and 5 LSIs per table (soft limits for GSIs can be increased via support).

65. **What are index projection types?**
    **Answer:**

* **ALL:** Project all table attributes.
* **KEYS\_ONLY:** Only keys.
* **INCLUDE:** Specified non-key attributes.

66. **How do you manage GSI throughput separately?**
    **Answer:** GSIs provision independent RCUs/WCUs. Monitor and adjust their capacity via auto-scaling or manual updates.

67. **What is eventual consistency behavior on GSIs?**
    **Answer:** GSIs are always eventually consistent; writes are propagated asynchronously to the index.

68. **How do you backfill a new GSI without impacting performance?**
    **Answer:** Use on-demand mode or temporarily increase base table capacity to accelerate backfill and reduce throttling.

69. **What are write amplification and its effect on GSIs?**
    **Answer:** Each table write also writes to GSIs, multiplying WCU consumption. More GSIs mean higher write amplification and cost.

70. **Can you delete an index? What are the implications?**
    **Answer:** Yes. Deleting a GSI removes its data and frees capacity charges. It may take time and impact performance during deletion.

71. **Explain sparse indexes and use cases.**
    **Answer:** Sparse indexes project only items with the indexed attribute, enabling efficient queries for subsets without scanning entire table.

72. **How do you query a GSI?**
    **Answer:** Use `Query` API specifying `IndexName` with key condition expressions on the GSI’s keys.

73. **What are composite GSIs?**
    **Answer:** GSIs with both partition and sort keys, allowing range queries and ordered results on the index.

74. **How do you monitor index usage?**
    **Answer:** CloudWatch provides metrics for GSI consumed capacity and throttles. Use DynamoDB Console to view hot partitions by index.

75. **What are the trade-offs of using many small GSIs vs. fewer larger ones?**
    **Answer:** Many small GSIs target specific patterns with minimal projected attributes (lower cost), while fewer broad GSIs increase flexibility at higher write cost.

---

### 5. Transactions & ACID (Questions 76–85)

76. **What transactional operations does DynamoDB support?**
    **Answer:** `TransactWriteItems` and `TransactGetItems` enabling atomic, consistent, isolated operations across multiple items and tables.

77. **How do you ensure ACID properties in DynamoDB?**
    **Answer:** Transactions provide all-or-nothing operations with conditional checks, ensuring atomicity, consistency, isolation, and durability.

78. **What are the limitations of transactions?**
    **Answer:** Up to 25 items or 4 MB total payload per transaction. Transactions consume double WCUs because they perform conditional checks.

79. **How do you handle transaction conflicts?**
    **Answer:** Detect `TransactionCanceledException` with reasons (e.g., conditional check failure). Implement retries with backoff and idempotency.

80. **What is idempotency in DynamoDB transactions?**
    **Answer:** Use client tokens for idempotent operations or design your logic to handle retries without side effects (e.g., check for existing state).

81. **Can you mix transactional and non-transactional operations?**
    **Answer:** Yes, but they are separate API calls. Mixing within the same application flow requires careful capacity planning and error handling.

82. **How do you model cross-table transactions?**
    **Answer:** Place related items in different tables and reference them in a single `TransactWriteItems` call, with conditional checks to maintain consistency.

83. **What performance impact do transactions have?**
    **Answer:** Higher latency and double WCU consumption per item due to locking and conditional checks. Use only when strict consistency is needed.

84. **How does DynamoDB ensure isolation within transactions?**
    **Answer:** Transactions apply locks on items being written until the transaction completes, preventing concurrent conflicting updates.

85. **Describe a use case where transactions are essential.**
    **Answer:** Financial transfers between accounts where debiting one item and crediting another must succeed or fail together.

---

### 6. Streams & Lambda Integration (Questions 86–95)

86. **What is DynamoDB Streams and how is data captured?**
    **Answer:** Streams capture item-level changes (INSERT, MODIFY, REMOVE) in order and deliver them as an ordered log for 24 hours.

87. **How do you enable and configure Streams?**
    **Answer:** Enable on table creation or update, choosing view type: KEYS\_ONLY, NEW\_IMAGE, OLD\_IMAGE, or NEW\_AND\_OLD\_IMAGES.

88. **How do you process Streams with AWS Lambda?**
    **Answer:** Create a Lambda trigger pointing to the stream ARN. Lambda receives batched event records, processes, and can checkpoint via DynamoDB iterator.

89. **What is the difference between Kinesis Streams and DynamoDB Streams?**
    **Answer:** DynamoDB Streams is table-specific and auto-managed, capturing table changes. Kinesis Streams is a general-purpose streaming platform with broader retention and scaling options.

90. **How do you scale Lambda consumers for high throughput streams?**
    **Answer:** Increase parallelism by adjusting batch size and reserved concurrency for Lambda; consider using enhanced fan-out.

91. **What are common use cases for Streams?**
    **Answer:** Cross-region replication, maintaining materialized views, triggering downstream workflows, auditing changes.

92. **How do you guarantee at-least-once processing with Streams?**
    **Answer:** Implement idempotent Lambda handlers and checkpointing logic to handle retries without duplicating effects.

93. **What are iterator age and its significance?**
    **Answer:** Iterator age measures lag between record generation and processing; high age indicates slow consumers or throttling.

94. **How do you handle schema evolution when using Streams?**
    **Answer:** Use flexible parsing logic that checks for attribute existence, versioning in items, and backward compatibility in Lambda code.

95. **Describe a near-real-time analytics pipeline using DynamoDB Streams.**
    **Answer:** Stream changes to Lambda, transform records, and put into Kinesis Data Firehose for loading into Redshift or S3 for analytics.

---

### 7. Security & Encryption (Questions 96–105)

96. **How is data encrypted at rest in DynamoDB?**
    **Answer:** DynamoDB uses AWS KMS-managed keys (SSE-S3 by default, or SSE-KMS customer-managed) to encrypt table data at rest.

97. **What are encryption in transit options?**
    **Answer:** All API calls use HTTPS/TLS for encrypting data in transit between clients and DynamoDB endpoints.

98. **How do you implement fine-grained access control?**
    **Answer:** Use IAM policies with `Condition` on `dynamodb:LeadingKeys` or `dynamodb:Attributes` to restrict access to specific items or attributes.

99. **What is AWS IAM policy condition for attribute-level permissions?**
    **Answer:** Use `dynamodb:Attributes` in policy `Condition` block to allow or deny actions based on attribute names.

100. **How do you audit DynamoDB access?**
     **Answer:** Enable AWS CloudTrail data events for DynamoDB to log table-level API calls and use CloudWatch Logs for analysis.

101. **What are VPC endpoints for DynamoDB?**
     **Answer:** DynamoDB supports Gateway VPC Endpoints via AWS PrivateLink, enabling private network access without internet.

102. **How do you rotate encryption keys?**
     **Answer:** For SSE-KMS, enable automatic key rotation in KMS or manually create new CMK and update table settings.

103. **How do you secure Streams data?**
     **Answer:** Streams inherit table encryption settings. Use IAM policies to restrict `dynamodb:DescribeStream` and `dynamodb:GetRecords` actions.

104. **What compliance standards does DynamoDB meet?**
     **Answer:** DynamoDB is compliant with PCI DSS, HIPAA, SOC, ISO, FedRAMP, and other regional standards.

105. **How do you protect against DDoS attacks on DynamoDB?**
     **Answer:** Use AWS Shield, WAF on application layer, caching at edge (CloudFront/DAX), and ensure capacity buffers to absorb spikes.

---

### 8. Monitoring, Troubleshooting & Best Practices (Questions 106–120)

106. **What CloudWatch metrics are critical for DynamoDB health?**
     **Answer:** `ConsumedReadCapacityUnits`, `ConsumedWriteCapacityUnits`, `ReadThrottleEvents`, `WriteThrottleEvents`, `SuccessfulRequestLatency`, and `SystemErrors`.

107. **How do you enable detailed monitoring?**
     **Answer:** Detailed monitoring is automatic; enable Contributor Insights for granular patterns and hot-key analysis.

108. **What is AWS X-Ray integration with DynamoDB?**
     **Answer:** X-Ray traces DynamoDB API calls from instrumented SDK calls to visualize latency, downstream dependencies, and performance bottlenecks.

109. **How do you troubleshoot high latency?**
     **Answer:** Correlate CloudWatch metrics, check for throttling, review partition key access patterns, enable X-Ray, and examine network metrics.

110. **What are DynamoDB best practices for cost optimization?**
     **Answer:** Use on-demand for variable workloads, right-size provisioned capacity with auto-scaling, minimize item size, use sparse indexes, and enable TTL.

111. **How do you conduct a load test for DynamoDB?**
     **Answer:** Use tools like AWS SDK-based scripts, Amazon QLDB, or NoSQL Workbench’s generate traffic feature; monitor CloudWatch live.

112. **What is Contributor Insights for DynamoDB?**
     **Answer:** Contributor Insights analyzes log data to identify top contributors (e.g., hot keys) over time, helping optimize partition and access patterns.

113. **How do you detect and resolve hot partitions?**
     **Answer:** Use CloudWatch and Contributor Insights to find skew; redesign key schema, increase cardinality, or introduce sharding prefixes.

114. **How do you handle item-level errors in batch operations?**
     **Answer:** Inspect `UnprocessedItems` in the response and retry with exponential backoff; log failures for manual remediation.

115. **What is the significance of latency percentiles (p50, p90, p99)?**
     **Answer:** Percentiles show distribution: p50 is median latency; p90/p99 highlight tail latencies that impact user experience and SLAs.

116. **When should you use DynamoDB TTL?**
     **Answer:** For data with natural expiration (sessions, logs), reducing storage and cost by automatically deleting stale items.

117. **How do you maintain data integrity over time?**
     **Answer:** Use conditional writes, transactions, and periodic consistency checks via full scans or Streams-driven reconciliation.

118. **What is proactive vs. reactive scaling?**
     **Answer:** Proactive uses scheduled capacity changes based on known patterns; reactive leverages auto-scaling responding to real-time metrics.

119. **How do you implement disaster recovery for DynamoDB?**
     **Answer:** Use on-demand backups, point-in-time recovery, Global Tables for multi-region redundancy, and cross-region backups to S3.

120. **Summarize key DynamoDB design tenets.**
     **Answer:** Design for access patterns, embrace denormalization, optimize partition keys, leverage adaptive capacity, and automate scaling and backups.
