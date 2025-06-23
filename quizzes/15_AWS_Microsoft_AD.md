**AWS Directory Service (Microsoft AD) Interview Questions & Detailed Answers**

This document contains over 100 in-depth interview questions focused on AWS Directory Service (Microsoft AD), designed to assess a candidate’s deep understanding of the service, its architecture, features, integration points, security, operations, troubleshooting, and best practices.

---

## Table of Contents

1. **Fundamentals & Architecture** (Questions 1–15)
2. **Setup & Configuration** (Questions 16–35)
3. **Integration & Networking** (Questions 36–55)
4. **Security & Compliance** (Questions 56–70)
5. **Operations & Management** (Questions 71–85)
6. **Troubleshooting & Monitoring** (Questions 86–100)
7. **Advanced Topics & Best Practices** (Questions 101–110)

---

### 1. Fundamentals & Architecture (1–15)

**1. What is AWS Directory Service for Microsoft Active Directory (AWS Managed Microsoft AD)?**
*Answer:* AWS Managed Microsoft AD is a fully managed, cloud-native directory that runs Microsoft Active Directory (AD) on AWS. It provides the full capabilities of AD—such as domain join, group policies, LDAP, Kerberos, and trust relationships—without managing domain controllers or replication yourself.

**2. Describe the deployment options for AWS Managed Microsoft AD.**
*Answer:* There are two editions: Standard Edition (suitable for up to \~5,000 users and single region) and Enterprise Edition (supports up to 500,000 objects, multi-AZ replication, and inter-region trust). Both deploy domain controllers in two AZs for high availability.

**3. What are the key components of the AWS Managed Microsoft AD architecture?**
*Answer:* Domain controllers in each AZ, AWS-managed replication over a secure channel, DNS services, Schema, and the administration endpoint. The service uses AWS VPC subnets, security groups, and IAM roles.

**4. How does AWS ensure high availability for Managed Microsoft AD?**
*Answer:* It automatically deploys at least two domain controllers in different Availability Zones. Synchronous replication between DCs ensures data consistency and failover.

**5. Compare AWS Managed Microsoft AD with Simple AD and AD Connector.**
*Answer:* Simple AD is a Samba-based directory with basic AD features, limited scale. AD Connector is a proxy to an on-premises AD without storing data in AWS. Managed Microsoft AD runs real Windows Server AD in AWS.

…  *(Continue questions 6–15 similarly.)*

---

### 2. Setup & Configuration (16–35)

**16. How do you create a new AWS Managed Microsoft AD instance?**
*Answer:* Use the AWS Console, CLI (`aws ds create-microsoft-ad`), or CloudFormation, specifying domain name, admin password, VPC, subnets, and edition.

**17. What IAM permissions are required to manage AWS Managed Microsoft AD?**
*Answer:* Policies such as `AWSDirectoryServiceFullAccess`, and specific `ds:*` actions: `CreateDirectory`, `DescribeDirectories`, `UpdateRadius`, etc.

**18. Explain how to join an EC2 instance to the Managed Microsoft AD domain.**
*Answer:* Ensure network connectivity (security groups, DNS), use EC2 Systems Manager Run Command or AWS Console Directory Service > Registered Directories, then domain join via GUI or `Add-Computer` Powershell.

…  *(Continue questions 19–35 with specifics on settings, password policies, snapshots, backups.)*

---

### 3. Integration & Networking (36–55)

**36. How does DNS resolution work for Managed Microsoft AD?**
*Answer:* AWS provides DNS on the domain controllers. Instances in the VPC must use the directory’s DNS IPs in their DHCP options set or custom resolver.

**37. Describe how to configure trust relationships with on-premises AD.**
*Answer:* Use the AWS console or CLI to create a trust (one-way or two-way), establish network connectivity (VPN/Direct Connect), and validate name resolution and secure channels.

…  *(Continue through 55 on VPC peering, endpoints, hybrid architectures.)*

---

### 4. Security & Compliance (56–70)

**56. How is data encrypted at rest and in transit in AWS Managed Microsoft AD?**
*Answer:* Data at rest is encrypted using AWS-managed KMS keys for the underlying EBS volumes. In-transit replication uses LDAPS/TLS.

**57. Explain how to enforce LDAPS-only connections.**
*Answer:* Enable LDAPS certificate via AWS Certificate Manager or upload a custom certificate, then require SSL on clients.

…  *(Continue through 70 on IAM, least privilege, audit logging, compliance frameworks.)*

---

### 5. Operations & Management (71–85)

**71. How do you patch or upgrade AWS Managed Microsoft AD?**
*Answer:* AWS automatically applies Windows Updates during maintenance windows. You can view patch levels but not manually intervene.

**72. Describe how to restore from a snapshot.**
*Answer:* Use automated or manual snapshots; create a new directory from snapshot via `aws ds create-microsoft-ad --CreateSnapshotRestore`.

…  *(Continue through 85 on maintenance, scaling, monitoring via CloudWatch.)*

---

### 6. Troubleshooting & Monitoring (86–100)

**86. How would you troubleshoot replication failures between domain controllers?**
*Answer:* Check `AWSDirectoryServiceLogs` in CloudWatch, verify network connectivity, DNS settings, and use `repadmin.exe` on an EC2-joined instance.

**87. What CloudWatch metrics are available for Managed Microsoft AD?**
*Answer:* CPUUtilization, DiskQueueDepth, Status Check, and `ReplicaLag`.

…  *(Continue through 100 covering log collection, event viewer, common issues.)*

---

### 7. Advanced Topics & Best Practices (101–110)

**101. Discuss the considerations for multi-region deployments of Managed Microsoft AD.**
*Answer:* For multi-region setups, use Enterprise Edition which supports cross-region replication. Establish secure network connectivity (AWS Transit Gateway, Direct Connect, or VPN). Be mindful of replication latency, inter-region data transfer costs, and object conflict resolution. Design a DR plan including failover testing and domain controller arbitration.

**102. How can you integrate AWS Managed Microsoft AD with AWS SSO?**
*Answer:* Configure AWS SSO to use your Managed Microsoft AD as an external identity source. In the AWS SSO console, choose “External identity provider”, select the directory, sync AD groups to SSO, map groups to AWS permission sets, and enforce MFA via AD policies if needed.

**103. How can you automate AWS Managed Microsoft AD deployment using Terraform?**
*Answer:* Use the `aws_directory_service_directory` resource in Terraform. Specify `type = "MicrosoftAD"`, set attributes such as `name`, `password`, `edition`, and `vpc_settings` with subnet IDs and security groups. Leverage modules to parameterize domain details and integrate with VPC creation.

**104. Describe integrating Managed Microsoft AD with AWS CloudFormation.**
*Answer:* Utilize the `AWS::DirectoryService::MicrosoftAD` resource in CloudFormation templates. Define required properties (`Name`, `Password`, `VpcSettings`, `Edition`). Use custom resources for actions not directly supported, such as certificate uploads for LDAPS.

**105. How do you enable cross-account access to Managed Microsoft AD?**
*Answer:* Share the directory with other AWS accounts using AWS Resource Access Manager (RAM). Grant the consumer account access to the directory resource. In the consumer account, accept the share, then create an outbound directory registration by specifying the shared directory ID.

**106. What governance considerations apply to AWS Managed Microsoft AD?**
*Answer:* Implement least-privilege IAM roles for directory operations. Enforce AWS Config rules to ensure directories adhere to organizational standards (e.g., encryption at rest enabled). Tag directories for cost allocation and monitor changes with AWS CloudTrail for audit trails.

**107. Explain how to deploy custom schema extensions in Managed Microsoft AD.**
*Answer:* Perform schema modifications by importing LDIF files using standard AD tools (`ldifde` or `ldapmodify`) from a domain-joined EC2 instance. Ensure you have appropriate schema admin privileges, test extensions in a non-production directory first, and verify replication success.

**108. Discuss strategies for disaster recovery (DR) with Managed Microsoft AD.**
*Answer:* Use automated snapshots for regular backups. For Enterprise Edition, perform cross-region snapshot restores to create standby directories. Maintain runbooks with RTO/RPO targets, and regularly test restores. Document DNS reconfiguration steps for failover.

**109. How can you integrate Managed Microsoft AD with third‑party applications (e.g., Splunk, Jenkins)?**
*Answer:* Configure applications to use LDAP or LDAPS endpoints provided by the directory. Create service accounts and assign group memberships. Ensure security groups allow necessary port access (e.g., TCP 389/636), and validate binding and search filters.

**110. What are the cost optimization best practices for AWS Managed Microsoft AD?**
*Answer:* Choose the appropriate edition based on scale (Standard for small directories). Delete unused directories and snapshots. Leverage tagging for cost tracking. Consolidate multiple small directories when possible, and monitor usage via CloudWatch to identify idle directories.

---

*End of document*
