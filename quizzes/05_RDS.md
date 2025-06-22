1–10: Fundamentals & Architecture
What is Amazon RDS and what problem does it solve?
Answer: Amazon RDS (Relational Database Service) is a managed service that simplifies the setup, operation, and scaling of relational databases in the cloud. It automates administrative tasks such as hardware provisioning, database setup, patching, backups, and monitoring, letting you focus on application development rather than database maintenance.

Which database engines does RDS support?
Answer: RDS supports MySQL, PostgreSQL, MariaDB, Oracle, Microsoft SQL Server, and Amazon Aurora (MySQL- and PostgreSQL-compatible editions).

Explain the difference between RDS and Amazon Aurora.
Answer: Aurora is Amazon’s proprietary engine offering higher performance and availability. It decouples storage from compute, replicates six copies across three AZs, and delivers up to 5× MySQL and 3× PostgreSQL performance. Standard RDS engines use attached EBS volumes with two-way synchronous replication for Multi-AZ.

What is a DB instance class?
Answer: A DB instance class defines the compute (vCPUs, memory) and networking capacity of the database instance. Classes range from burstable db.t types to memory-optimized db.r and db.x types for large workloads.

How does RDS Multi-AZ work?
Answer: Multi-AZ creates a synchronous standby replica in a different Availability Zone. Updates are synchronously replicated, and RDS automatically fails over to the standby on instance failure, network issues, or AZ disruption.

What storage types are available in RDS?
Answer:

General Purpose SSD (gp2/gp3): Cost-effective, balanced IOPS.

Provisioned IOPS SSD (io1/io2): High and predictable I/O performance.

Magnetic (legacy): Lower cost, low performance, now deprecated for new instances.

Describe how automated backups work.
Answer: RDS takes daily snapshots and retains transaction logs, enabling point-in-time recovery (PITR) within the backup retention period (1–35 days). Snapshots and logs are stored in S3.

What is an RDS DB snapshot?
Answer: A manual snapshot of the DB instance at a point in time. It persists until explicitly deleted and can be used to restore a new database.

Explain parameter groups and option groups.
Answer:

Parameter Groups: Containers for engine configuration settings (like max_connections). You apply a parameter group to an instance to override defaults.

Option Groups: Allow adding and configuring database features (e.g., SQL Server’s TDE, Oracle’s APEX).

How do you connect to an RDS instance securely?
Answer:

Place the instance inside a VPC with proper security group rules (allowing only required IPs/ports).

Use SSL/TLS certificates for encryption in transit.

Optionally connect via a bastion host or AWS VPN/Direct Connect.

11–20: High Availability & Replication
What’s the difference between read replicas and Multi-AZ?
Answer: Multi-AZ provides synchronous standby for failover (no read scaling). Read replicas are asynchronous copies used for read scaling, cross-region DR, or migrations; they do not provide automated failover.

How do you create a cross-region read replica?
Answer: Enable automated backups, then issue “Create read replica,” selecting a different AWS Region. AWS sets up asynchronous replication over the network.

What are the use-cases for read replicas?
Answer: Read scaling, analytics/reporting offloading, cross-region disaster recovery, zero-downtime upgrades/migrations, global low-latency reads.

How do you promote a read replica to a standalone instance?
Answer: Use the “Promote read replica” operation. This breaks replication, makes it writable, and stops binary log replication.

Can you have read replicas for Multi-AZ instances?
Answer: Yes. Read replicas can be created from either single-AZ or Multi-AZ primary instances.

Describe Aurora’s writer and reader endpoints.
Answer: Aurora provides a single writer endpoint that routes to the primary instance, and a reader endpoint that load-balances connections across all available read replicas.

How many read replicas can you have?
Answer:

MySQL/MariaDB: up to 5 within the same region; up to 5 cross-region.

PostgreSQL: up to 5 within the same region; cross-region currently unsupported for PG on RDS.

Aurora: up to 15 replicas.

What is failover priority in Aurora?
Answer: You can assign replica priorities (1–15) to control the order in which replicas are promoted during failover.

Explain storage auto-scaling in Aurora.
Answer: Aurora automatically grows database volume in 10 GB increments up to 128 TB as data increases, without downtime.

How does Aurora maintain six copies of data?
Answer: Data is distributed in 10 GB segments, each stored in triplicate across three AZs. Additionally, two copies of each segment are maintained in each AZ (total six).

21–30: Security & Compliance
How do you encrypt data at rest in RDS?
Answer: Enable encryption at creation time (EBS volume encryption using AWS KMS). All snapshots and replicas inherit encryption.

Can you enable encryption on an existing unencrypted DB?
Answer: Not directly. Instead, take a snapshot, copy it with encryption enabled, then restore a new encrypted instance from that snapshot.

How is data encrypted in transit?
Answer: Use SSL/TLS certificates provided by AWS. Clients must enable SSL in the connection string.

What is IAM database authentication?
Answer: Allows you to authenticate to MySQL and PostgreSQL using IAM tokens instead of passwords. Tokens are valid for 15 minutes.

How do you rotate the master user password?
Answer: Via the AWS Console, SDK, or CLI (“modify-db-instance” with --master-user-password), followed by applying changes (immediate or during next maintenance window).

Describe RDS integration with AWS Secrets Manager.
Answer: Store database credentials in Secrets Manager, then configure RDS to rotate those credentials automatically on a schedule you define.

How do security groups and NACLs apply to RDS?
Answer:

Security Groups: Virtual firewalls controlling inbound/outbound DB instance traffic.

NACLs: Subnet-level stateless filters that apply to all resources in the subnet.

What are best practices for RDS security?
Answer:

Use least-privilege IAM roles.

Enable encryption at rest and in transit.

Place in private subnets.

Regularly rotate credentials.

Use Secrets Manager.

Enable enhanced monitoring and audit logging.

Explain RDS Enhanced Monitoring.
Answer: Provides OS-level metrics (CPU, memory, file system, disk I/O) at up to 1-second granularity via the CloudWatch Logs Agent running on the instance.

What is RDS Performance Insights?
Answer: A database performance tuning tool that collects query-level performance data and visualizes it in a dashboard, helping identify bottlenecks.

31–40: Backup, Recovery & Maintenance
How do you perform a point-in-time recovery (PITR)?
Answer: Restore a new instance from automated backups specifying the target timestamp (within retention period). RDS applies transaction logs to bring the DB to that exact point.

What happens to automated backups on Multi-AZ?
Answer: Backups are taken from the standby instance to avoid I/O impact on the primary.

Can you backup read replicas?
Answer: Yes. Automated backups can run on read replicas; however, you must enable backups when creating the replica.

How do you schedule maintenance windows?
Answer: Define a weekly 30-minute window during which AWS applies minor engine version upgrades and patches.

What is the difference between minor and major engine version upgrades?
Answer:

Minor Upgrades: Patches and bug fixes, generally backward-compatible.

Major Upgrades: New engine versions with potential feature changes that may require application testing.

Explain snapshot export to S3.
Answer: You can export a snapshot’s data in Parquet format to S3 for analytics using Athena, Redshift Spectrum, or EMR.

How do you restore an encrypted snapshot to a different Region?
Answer: Copy the snapshot to the target Region (encryption is preserved), then restore a DB instance from that copy.

What is RDS lock-in?
Answer: Although schema and data are portable, some engine-specific features (e.g., Oracle’s SQL dialect) and AWS-managed aspects (Aurora storage layer) create partial lock-in.

How do you reduce backup time impact on production?
Answer: Use Multi-AZ (backups use standby), back up during low-traffic windows, and leverage incremental backups.

Describe deletion protection.
Answer: A flag that prevents an instance from being accidentally deleted. You must disable it before you can delete the DB.

41–50: Scaling & Performance
How can you vertically scale an RDS instance?
Answer: Use “modify-db-instance” to change the DB instance class to a larger instance. It incurs downtime unless you scale during a maintenance window with Multi-AZ.

How do you horizontally scale reads?
Answer: Add read replicas and point read-only traffic to them via application logic or endpoint DNS.

What is storage autoscaling?
Answer: For Aurora and for gp3 in standard RDS, you can enable autoscaling so that storage grows automatically when usage approaches allocated capacity.

Discuss connection pooling for RDS.
Answer: Use external pools like PgBouncer (PostgreSQL), ProxySQL, or Amazon RDS Proxy to reduce overhead of establishing DB connections and improve scalability.

What is Amazon RDS Proxy?
Answer: A fully managed, highly available database proxy that aggregates and pools connections to RDS/ Aurora, improving application scalability and resiliency.

How does Proxy differ from a standard load balancer?
Answer: Proxy is application-aware, reuses and multiplexes connections, supports transaction pinning and failover handling. ELBs are network-level and don’t pool DB connections.

Explain query caching and its availability in RDS.
Answer: MySQL supports query cache (disabled by default in newer versions). Aurora has an advanced cache layer automatically. PostgreSQL doesn’t have global query caching.

How do you monitor IOPS usage?
Answer: CloudWatch metrics like ReadIOPS and WriteIOPS. Set alarms to detect when actual IOPS approach provisioned or burst limits.

What is burst balance on gp2 storage?
Answer: gp2 volumes accumulate credits when idle; credits allow bursts above baseline IOPS. If credits deplete, performance drops to baseline.

Optimize performance: what metrics do you watch?
Answer: CPUUtilization, DBConnections, FreeableMemory, ReadLatency, WriteLatency, DiskQueueDepth, Deadlocks, ReplicaLag, SwapUsage, NetworkThroughput.

51–60: Engine-Specific
What unique features does Aurora MySQL offer?
Answer: Global Database for cross-region read-scaling, backtrack for time-travel restore, serverless v1/v2, fast failover (<30 sec), parallel query, and push-down of compute to storage.

Explain PostgreSQL logical replication on RDS.
Answer: Use the rds.logical_replication parameter and the pglogical extension to replicate data between RDS and external PostgreSQL databases.

How do you enable Oracle Data Guard on RDS?
Answer: RDS Oracle Managed Standby (only available in certain editions) provides Data Guard–style replication; configure via option group and console settings.

What licensing models are available for Oracle on RDS?
Answer:

License Included: Pay-as-you-go licensing.

Bring Your Own License (BYOL): Use existing Oracle licenses under AWS’s License Mobility.

How does SQL Server Always On work in RDS?
Answer: For SQL Server Enterprise Edition, RDS uses native Always On Availability Groups across AZs; you configure failover groups within the console.

What are the limitations of RDS Aurora Serverless v1?
Answer:

Cold start latency.

Max capacity 256 ACUs.

No Multi-AZ isolation for serverless.

Limited engine features compared to provisioned Aurora.

Describe how to configure temporal tables in Aurora PostgreSQL.
Answer: Use native PostgreSQL table inheritance and triggers; Aurora doesn’t provide built-in temporal tables, so implement via custom functions and archival tables.

How do you migrate data from on-premises Oracle to RDS MySQL?
Answer: Use AWS DMS for heterogeneous migrations with schema conversion via AWS SCT; handle datatype mapping, function/stored procedure translation manually.

What is Amazon RDS Custom?
Answer: A managed option for installing custom database features or extensions not supported natively by RDS (e.g., specialized Oracle packages).

Can you run PostgreSQL extensions on RDS?
Answer: Yes, RDS supports many popular extensions (e.g., PostGIS, pg_partman). Unsupported extensions require Aurora Custom or self-managed EC2 deployment.

61–70: Networking & Connectivity
How do you place an RDS instance in a VPC?
Answer: All new RDS instances must be in a VPC. You select the VPC, subnet group (specifying at least two subnets in distinct AZs), and security group rules.

What is a DB subnet group?
Answer: A collection of subnets in different AZs that RDS uses to select placement for instances.

How does RDS handle public accessibility?
Answer: You can mark an instance as “publicly accessible,” which attaches a public IP and opens it via an Internet Gateway; otherwise it’s private only.

Explain VPC peering for cross-VPC DB access.
Answer: Establish a VPC peering connection, update route tables, and adjust security groups to allow cross-VPC traffic between application and RDS.

Can you use AWS PrivateLink with RDS?
Answer: Not directly; however, you can front your RDS instance with Network Load Balancer and expose via PrivateLink for secure access.

What is IAM role-based access to RDS?
Answer: Use IAM roles attached to EC2 or Lambda to allow STS-issued tokens for authentication, or to grant rds:Connect.

How do you troubleshoot connectivity issues?
Answer: Check VPC/subnet configuration, security group rules, NACLs, DNS resolution, route tables, and parameter group settings (e.g., publicly_accessible).

Explain the role of a bastion host.
Answer: A hardened EC2 instance in the public subnet that you SSH into, then connect privately to RDS instances in private subnets.

What is AWS Private CA integration for RDS?
Answer: You can import a private TLS certificate from AWS Certificate Manager Private CA to enable SSL/TLS using your own certificate authority.

How do you enable fine-grained auditing?
Answer:

MySQL/MariaDB: Use audit plugins via option groups.

Oracle: Use native audit parameters in an option group.

SQL Server: Enable SQL Server Audit via option group.

71–80: Cost Optimization & Best Practices
How is RDS pricing structured?
Answer: Based on instance class (per-hour), storage allocated (GB-month), I/O (for magnetic), backup storage (beyond free tier), data transfer, and optional features (Multi-AZ, Aurora Serverless ACUs).

When should you use Reserved Instances?
Answer: For predictable, steady-state workloads, you can commit to 1- or 3-year RIs to save up to 60% over on-demand pricing.

How can you reduce storage costs?
Answer: Right-size allocated storage, enable storage autoscaling only when needed, leverage compression at the application level, and clean up unused data.

Discuss the benefits of Aurora Serverless v2 for cost.
Answer: Fine-grained capacity scaling (down to zero) with fast scaling, meaning you pay only for actual usage rather than full instance hours.

What are best practices for Multi-AZ vs. read replicas?
Answer: Use Multi-AZ for high availability/DR; use read replicas for scaling read workloads. Don’t substitute one for the other.

How do you benchmark RDS performance?
Answer: Use tools like SysBench for MySQL, pgbench for PostgreSQL, HammerDB for SQL Server/Oracle. Measure throughput and latency under realistic workloads.

Why is it important to monitor storage usage?
Answer: Sudden growth can exhaust storage (causing instance stalls). Configure CloudWatch alarms on FreeStorageSpace.

How do you clean up unused snapshots?
Answer: Automate snapshot lifecycle using AWS Backup or Lambda scripts to delete snapshots older than a retention threshold.

What is the AWS Well-Architected Framework’s guidance for RDS?
Answer: Follow pillars: Operational Excellence (monitoring, automation), Security (encryption, least privilege), Reliability (Multi-AZ, backups), Performance (right-sizing, caching), Cost Optimization (RIs, autoscaling), Sustainability (efficient resource use).

Which CloudWatch dashboards do you recommend?
Answer: Dashboards showing CPU, connections, memory, storage, IOPS, latency, replica lag, and application-level metrics (e.g., queries per second).

81–90: Migration & Hybrid Architectures
How do you migrate to RDS with minimal downtime?
Answer: Use AWS Database Migration Service (DMS) with continuous data replication, perform cutover during a low-traffic window after syncing CDC.

Explain heterogeneous vs. homogeneous migration.
Answer:

Homogeneous: Same engine (e.g., on-prem MySQL → RDS MySQL), can use native tools or snapshots.

Heterogeneous: Different engines (e.g., Oracle → Aurora), requires AWS SCT for schema conversion and DMS for data replication.

What challenges arise when migrating large databases?
Answer:

Schema incompatibilities.

Network bandwidth constraints.

Large data volumes requiring full load and CDC.

Long replication lag.

How do you connect an on-prem environment to RDS privately?
Answer: Use Direct Connect or VPN to extend your network into the VPC, then access RDS in private subnets.

Describe RDS Proxy in hybrid scenarios.
Answer: Proxy can front RDS endpoints, allowing connection pooling and network routing through on-prem networks with consistent endpoints.

What is AWS SCT and how is it used?
Answer: AWS Schema Conversion Tool analyzes and transforms database schemas and code objects from one engine to another, generating assessment reports and migration scripts.

How do you handle cross-region disaster recovery?
Answer: Use cross-region read replicas or Aurora Global Database. For standard RDS, replicate snapshots or use DMS to replicate into a standby instance in another Region.

Explain blue/green deployments with RDS.
Answer: Provision a parallel green environment (new engine version or config), migrate or sync data continuously, switch application traffic via DNS once validated, then decommission blue.

Can you run RDS on Outposts?
Answer: RDS on Outposts is available for certain engines, allowing you to run managed databases on-premises with AWS Outposts hardware.

How do you monitor network throughput for cross-region replication?
Answer: CloudWatch metrics for network throughput, DMS CloudWatch metrics for replication latency and throughput, and VPC Flow Logs for deeper analysis.

91–100: Troubleshooting & Advanced Topics
How do you diagnose high replica lag?
Answer: Check network latency, instance CPU/memory saturation, disk I/O bottlenecks, long-running queries on primary, and replication configuration mismatches.

What causes “Insufficient storage available” errors?
Answer: Running out of allocated storage (snapshots, tablespaces), log file growth, or uncleaned temporary files. Monitor FreeStorageSpace and enable autoscaling or increase storage.

How do you resolve an RDS instance stuck in “modifying” state?
Answer: Investigate pending maintenance, check CloudTrail for ongoing operations, possibly cancel or wait. If stuck long, contact AWS Support.

Explain how to troubleshoot SSL connection failures.
Answer: Verify certificate installation on client, validate endpoint hostnames, ensure require SSL is enabled in parameter group, confirm port 3306/5432 is open only for encrypted connections.

What is RDS event notification and how is it used?
Answer: You can subscribe to SNS topics for events (failover, backup, low storage, parameter changes) to get alerts and trigger automated workflows.

How do you handle maintenance overruns?
Answer: Monitor AWS Health events, set maintenance windows during off-peak, test in pre-prod, and use Multi-AZ to reduce downtime.

Can you run analytical queries on RDS primary without impacting performance?
Answer: It’s risky—use read replicas or off-load to Redshift, Athena, or Aurora’s parallel query feature for analytics.

Describe RDS support for serverless compute.
Answer: Aurora Serverless v1/v2 allows on-demand scaling of compute capacity. v2 offers finer-grained scaling with virtually no cold starts.

What’s backtrack in Aurora?
Answer: A feature that lets you rewind an Aurora cluster to a prior time (up to the last 72 hours) without restoring from a snapshot, by retaining change history.

How do you secure your RDS auditing and logs?
Answer: Enable audit logs via option groups, publish to CloudWatch Logs or S3 with KMS encryption, enforce retention policies, and use Macie/CloudTrail for compliance monitoring.
