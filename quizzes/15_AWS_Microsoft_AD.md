**AWS Directory Service (Microsoft AD) Interview Questions & Detailed Answers**

This document contains 110 in-depth interview questions focused on AWS Directory Service (Microsoft AD), designed to assess a candidate’s deep understanding of the service, its architecture, features, integration points, security, operations, troubleshooting, and best practices.

---

## Table of Contents

1. **Fundamentals & Architecture** (1–15)
2. **Setup & Configuration** (16–35)
3. **Integration & Networking** (36–55)
4. **Security & Compliance** (56–70)
5. **Operations & Management** (71–85)
6. **Troubleshooting & Monitoring** (86–100)
7. **Advanced Topics & Best Practices** (101–110)

---

### 1. Fundamentals & Architecture (1–15)

**1. What is AWS Directory Service for Microsoft Active Directory (AWS Managed Microsoft AD)?**
*Answer:* It is a fully managed Microsoft Active Directory hosted on AWS, providing core AD features—domain join, Group Policy, LDAP, Kerberos—without managing domain controllers or replication yourself.

**2. Describe the deployment options for AWS Managed Microsoft AD.**
*Answer:* Two editions: Standard (up to \~5,000 users, single region, two AZs) and Enterprise (up to 500,000 objects, multi-AZ, inter-region trust). Both deploy domain controllers in two Availability Zones for HA.

**3. What are the key components of the AWS Managed Microsoft AD architecture?**
*Answer:* Includes managed domain controllers in subnets across AZs, AWS-managed DNS, directory schema, global catalog, replication service, and admin endpoints, all within a customer VPC.

**4. How does AWS ensure high availability for Managed Microsoft AD?**
*Answer:* By deploying at least two domain controllers in separate AZs with synchronous replication, ensuring failover if one AZ becomes unreachable.

**5. Compare AWS Managed Microsoft AD with Simple AD and AD Connector.**
*Answer:* Simple AD is a Samba-based directory with limited AD features and scale. AD Connector proxies requests to on-prem AD without storing data. Managed AD runs real Windows AD in AWS.

**6. Explain the difference between Standard and Enterprise editions of Managed Microsoft AD.**
*Answer:* Standard supports fewer objects (\~5k) and single-region. Enterprise supports up to 500k objects, automatic snapshots, multi-region trusts, and higher SLA.

**7. How does AWS handle DNS within Managed Microsoft AD?**
*Answer:* AWS provides DNS services on the domain controllers. VPC DHCP options must point to the directory DNS IPs for domain resolution.

**8. What networking prerequisites are required before deploying Managed Microsoft AD?**
*Answer:* A VPC with at least two private subnets in different AZs, security groups allowing required ports (TCP/UDP 53, 88, 389, 445, 636), and correct route tables.

**9. Discuss the security boundary of AWS Managed Microsoft AD domain controllers.**
*Answer:* Domain controllers run in AWS-managed accounts; you cannot access the OS. Security groups and IAM control network and API access.

**10. How does AWS Managed Microsoft AD integrate with AWS IAM?**
*Answer:* You grant IAM roles permissions to perform directory operations (ds:\* actions). Additionally, you can map AD users/groups to AWS IAM roles via AWS SSO.

**11. What LDAP and Kerberos capabilities does Managed Microsoft AD support?**
*Answer:* Supports standard LDAP v3 binds, searches, LDAPS with certificates, Kerberos authentication, service tickets, and SPNs.

**12. Explain the role of schema and global catalog in Managed Microsoft AD.**
*Answer:* The schema defines object classes and attributes. The global catalog holds a partial, read-only replica of all objects for forest-wide searches and logons.

**13. What is replication topology in AWS Managed Microsoft AD?**
*Answer:* A two-node, multi-AZ topology with synchronous replication between domain controllers. Enterprise can also replicate to read replicas in other regions.

**14. How are snapshots used in Managed Microsoft AD?**
*Answer:* AWS automatically takes daily snapshots. You can also take manual snapshots to restore a directory to a specific point-in-time.

**15. Describe use cases best suited for AWS Managed Microsoft AD.**
*Answer:* Domain join Windows/Linux EC2, corporate user authentication, RDS SQL Server Windows auth, AWS SSO integration, AD-aware apps in AWS.

---

### 2. Setup & Configuration (16–35)

**16. How do you create a new AWS Managed Microsoft AD instance?**
*Answer:* Via Console, CLI (`aws ds create-microsoft-ad`), or CloudFormation, specifying `Name`, `Password`, `Edition`, `VpcSettings` (VPC ID, Subnet IDs).

**17. What IAM permissions are required to manage AWS Managed Microsoft AD?**
*Answer:* Actions such as `ds:CreateDirectory`, `ds:DescribeDirectories`, `ds:UpdateDirectorySetup`, typically bundled in `AWSDirectoryServiceFullAccess`.

**18. Explain how to join an EC2 instance to the Managed Microsoft AD domain.**
*Answer:* Ensure network/DNS configured. On Windows EC2, use System > Domain Join or `Add-Computer` PowerShell. On Linux, install `adcli`/`sssd` and configure `/etc/sssd/sssd.conf`.

**19. How do you configure the DHCP options set to use directory DNS servers?**
*Answer:* Create or modify a DHCP options set in VPC, set `domain-name-servers` to the Managed AD DNS IPs, and associate with the VPC.

**20. Describe steps to configure an LDAPS certificate for secure LDAP.**
*Answer:* Generate a PEM certificate via ACM or import your own. Use `aws ds enable-ldaps` CLI or console to upload certificate. Update clients to use LDAPS port 636.

**21. How do you rotate the admin password for Managed Microsoft AD?**
*Answer:* Use the console or CLI (`aws ds reset-user-password`), specifying directory ID, user name (`Admin`), and new password.

**22. Explain how to take manual snapshots of your directory.**
*Answer:* In Console, Directory details > Snapshots > Create snapshot. Or CLI `aws ds create-snapshot --directory-id <id> --name <snapshotName>`.

**23. How do you restore a directory from a manual snapshot?**
*Answer:* Use CLI `aws ds restore-from-snapshot --snapshot-id <snapshotId> --name <newDirName> --password <AdminPwd>` or via console.

**24. Describe the CloudFormation template properties for `AWS::DirectoryService::MicrosoftAD`.**
*Answer:* Key properties: `Name`, `Password`, `Edition`, `VpcSettings` (subnet IDs), `EnableSso` (optional), `Tags`.

**25. How can you automate deployment using AWS CLI and scripts?**
*Answer:* Write shell or PowerShell scripts invoking `aws ds create-microsoft-ad`, `aws ds enable-ldaps`, `aws ds create-trust`, and AWS CLI commands for EC2 domain join.

**26. How do you tag directories and why are tags important?**
*Answer:* Use `--tags Key=Name,Value=MyDir` CLI or console tags tab. Tags help with cost allocation, automation, and resource organization.

**27. Explain how to enable audit logs for directory operations.**
*Answer:* Enable AWS CloudTrail for DS APIs. For Windows events, configure event forwarding to CloudWatch Logs via Directory Service settings.

**28. What are the default service quotas for Managed Microsoft AD and how can you request increases?**
*Answer:* Quotas include max directories per region, objects per directory. View in Service Quotas console and request increases via console or AWS Support.

**29. How do you configure password policies and account lockout settings?**
*Answer:* Use Group Policy Management on a domain-joined instance to edit Default Domain Policy under `Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies`.

**30. Explain how to create and manage OUs and groups via AWS Directory Service.**
*Answer:* Use standard AD tools: Active Directory Users & Computers on a domain-joined EC2 instance or PowerShell `New-ADOrganizationalUnit`, `New-ADGroup` cmdlets.

**31. How do you integrate AWS Certificate Manager with Managed Microsoft AD for LDAPS?**
*Answer:* Request a certificate in ACM, export it in PEM format including private key, then upload via AWS CLI or console to enable LDAPS.

**32. Describe how CloudWatch Logs integration works for Managed Microsoft AD.**
*Answer:* In Directory details, enable publishing Windows event logs to CloudWatch Logs. Select log streams (Security, System, DirectoryService).

**33. How can you use AWS Backup with Managed Microsoft AD?**
*Answer:* Currently unsupported; leverage native snapshots and cross-region snapshot copy. For file-system backups, integrate LDAPS exports.

**34. Explain multi-AZ placement of domain controllers.**
*Answer:* AWS automatically deploys DCs in two subnets across different AZs specified in VPCSettings for resilience against AZ failures.

**35. What support options exist for troubleshooting setup issues?**
*Answer:* AWS Support plans (Developer, Business, Enterprise), Directory Service logs in CloudWatch, DS API logs in CloudTrail, and community forums.

---

### 3. Integration & Networking (36–55)

**36. How does DNS resolution work for Managed Microsoft AD?**
*Answer:* The domain controllers act as DNS servers. VPC DHCP options point to their IPs; EC2 instances use these DNS for AD domain name resolution.

**37. Describe how to configure trust relationships with on-premises AD.**
*Answer:* Ensure network connectivity (VPN/Direct Connect), configure inbound security rules, then in AWS DS console create a two-way or one-way trust by providing on-prem forest and credential info.

**38. What network connectivity options support hybrid AD architectures (Direct Connect, VPN)?**
*Answer:* AWS Site-to-Site VPN, AWS Direct Connect, or Transit Gateway for private connectivity between on-premises and AWS VPCs.

**39. How do you configure VPC Peering for directory access across VPCs?**
*Answer:* Establish peering, update route tables, ensure security groups allow AD ports, and modify DHCP options in peered VPCs to point to directory DNS.

**40. Explain how to use AWS Transit Gateway with Managed Microsoft AD.**
*Answer:* Attach VPCs hosting DCs and client VPCs to the Transit Gateway, propagate routes, enforce security, and use centralized DNS resolution.

**41. How do you enable cross-account access via Resource Access Manager?**
*Answer:* Share the directory resource with the target account in AWS RAM. In the consumer account, accept share and register the shared directory for use.

**42. Describe how to connect AWS Lambda functions to your directory.**
*Answer:* Place Lambda in same VPC/subnets, attach security groups allowing LDAPS, configure environment variables with directory endpoints, and use LDAP libraries in code.

**43. How do you configure AWS SSO to use Managed Microsoft AD?**
*Answer:* In AWS SSO console, choose External Identity Source, select your directory, sync groups, assign user access based on AD groups.

**44. Explain integrating Amazon RDS SQL Server with Managed Microsoft AD for Windows Authentication.**
*Answer:* Enable Windows Authentication, specify your directory in RDS DB instance settings, and create logins mapped to AD users.

**45. How does AWS Elastic Beanstalk integrate with Managed Microsoft AD?**
*Answer:* Configure Beanstalk environment to run in same VPC/subnets, and use IAM roles or extension scripts to domain-join instances at deployment.

**46. Describe how to use AWS Systems Manager to run commands against domain-joined instances.**
*Answer:* Attach SSM agent and IAM role, then use Run Command targeting instances by tag or AD group membership to execute PowerShell or shell commands.

**47. How can Amazon WorkSpaces leverage Managed Microsoft AD?**
*Answer:* Create a WorkSpaces directory by selecting your Managed AD, then provision WorkSpaces where users authenticate with their AD credentials.

**48. Explain integration of AWS Managed Microsoft AD with Amazon QuickSight.**
*Answer:* QuickSight Enterprise edition can connect to AD for user authentication and group-based access control by specifying the directory ID.

**49. How do you configure AWS Directory Service endpoints using AWS PrivateLink?**
*Answer:* Enable AWS DS PrivateLink endpoints in your VPC; configure interface endpoints for DS APIs to keep traffic within AWS network.

**50. Describe connectivity from on-premises clients to Managed Microsoft AD.**
*Answer:* Use VPN or Direct Connect, update on-prem DNS to forward AD domain queries to AWS DNS IPs, and open firewall for AD ports.

**51. How do you handle overlapping CIDR ranges when extending your AD into AWS?**
*Answer:* Use NAT or proxy-AD with non-overlapping ranges, or deploy separate VPCs with non-overlapping CIDRs dedicated for AD connectivity.

**52. Explain how to configure security groups for domain controllers.**
*Answer:* Allow inbound TCP/UDP ports 53, 88, 389, 445, 464, 636, 3268, 3269 from client subnets and other DCs. Deny all other traffic by default.

**53. How can you integrate AWS Managed Microsoft AD with AWS Directory Service Simple AD?**
*Answer:* Typically not recommended. If needed, establish LDAP trusts at application level or migrate objects. Simple AD lacks trust features.

**54. Describe federation options for single sign-on with AWS Managed Microsoft AD.**
*Answer:* Use SAML federation via ADFS on domain-joined EC2 or AWS SSO integration for SAML-enabled applications.

**55. Explain the use of AWS Directory Service APIs in application code.**
*Answer:* Use AWS SDKs to call DS APIs (e.g., `CreateTrust`, `DescribeDirectories`) for dynamic provisioning, trust management, or metadata retrieval.

---

### 4. Security & Compliance (56–70)

**56. How is data encrypted at rest and in transit in AWS Managed Microsoft AD?**
*Answer:* EBS volumes use AWS KMS-managed encryption. In-transit replication and LDAP traffic can be encrypted with TLS/SSL (LDAPS).

**57. Explain how to enforce LDAPS-only connections.**
*Answer:* Upload an LDAPS certificate, then configure clients to connect over port 636 and disable non-SSL LDAP binds via Group Policy.

**58. Describe how AWS CloudTrail logs directory operations.**
*Answer:* DS API calls (create, update, delete) are recorded in CloudTrail. You can filter events by `eventSource = "ds.amazonaws.com"`.

**59. How do you configure AWS Config rules for Managed Microsoft AD compliance?**
*Answer:* Enable AWS Config, then create custom or managed rules to check directory encryption, snapshot settings, and tag compliance.

**60. What IAM policies should be least-privilege for directory management?**
*Answer:* Grant only required `ds:Describe*`, `ds:ResetUserPassword`, `ds:EnableLDAPS` actions. Avoid full admin unless necessary.

**61. How do you implement Role-Based Access Control (RBAC) in Managed Microsoft AD?**
*Answer:* Use AD groups and OU delegation. Assign permissions at the OU level via Group Policy and AD ACLs for granular control.

**62. Explain multi-factor authentication (MFA) integration.**
*Answer:* Use AD FS or third-party MFA solutions integrated via RADIUS. Configure RADIUS settings in AWS DS for AD Connector or Managed AD.

**63. How do you audit schema changes and group policy modifications?**
*Answer:* Enable AD auditing in Group Policy (`Audit directory service changes`, `Audit policy change`) and forward events to CloudWatch Logs.

**64. What are AWS security best practices for directory controllers?**
*Answer:* Use least-privilege IAM, secure subnet placement (private), strict security group rules, enable LDAPS, and monitor logs.

**65. Describe how to manage service accounts securely.**
*Answer:* Use Managed AD’s service account password rotation via Group Managed Service Accounts (gMSA), store credentials in AWS Secrets Manager.

**66. How do you respond to a suspected credential compromise in the directory?**
*Answer:* Reset compromised account passwords, review audit logs for suspicious activity, disable affected accounts, and investigate via CloudWatch Logs.

**67. What is the Shared Responsibility Model for AWS Managed Microsoft AD?**
*Answer:* AWS manages the infrastructure, hypervisor, and patching of the OS. You manage directory objects, schema, GPOs, and user credentials.

**68. How do you achieve PCI DSS or HIPAA compliance with Managed Microsoft AD?**
*Answer:* Ensure encryption at rest/in-transit, enable logging and monitoring, enforce MFA, maintain audit trails, and implement least-privilege access.

**69. Explain automated certificate renewal for LDAPS.**
*Answer:* Use ACM’s certificate renewal for public CA certs. Automate export and upload to DS via Lambda or scripts triggered on renewal events.

**70. How do you use AWS Secrets Manager with Managed Microsoft AD?**
*Answer:* Store service account or bind DN credentials in Secrets Manager; grant applications permission via IAM to retrieve credentials for LDAP binds.

---

### 5. Operations & Management (71–85)

**71. How do you patch or upgrade AWS Managed Microsoft AD?**
*Answer:* AWS applies Windows Updates during scheduled maintenance windows. You view patch history but cannot patch manually.

**72. Describe how to restore from a snapshot.**
*Answer:* Use Console or CLI (`aws ds restore-from-snapshot`), providing snapshot ID, new directory name, and Admin password to spin up a restored directory.

**73. How do you monitor health of domain controllers?**
*Answer:* Monitor CloudWatch metrics (CPU, Disk, ReplicaLag) and view DS logs in CloudWatch Logs for DC-specific events.

**74. What CloudWatch metrics are critical for directory health?**
*Answer:* `CPUUtilization`, `FreeStorageSpace`, `ReplicaLag`, `HealthCheckStatus`, and `StatusCheckFailed`.

**75. Explain how to set up Amazon CloudWatch Alarms for directory events.**
*Answer:* In CloudWatch, create alarms on DS metrics (e.g., ReplicaLag > threshold) and set SNS notifications or automated actions.

**76. How do you scale the directory for growing object counts?**
*Answer:* Upgrade to Enterprise edition if hitting limits. Clean up unused objects and optimize OU structure to improve AD performance.

**77. Describe how to handle schema version updates.**
*Answer:* Test schema updates in a dev directory, apply LDIF changes, verify replication, then run production updates during maintenance windows.

**78. How do you manage password resets for directory users?**
*Answer:* Use ADUC on a domain-joined instance, PowerShell cmdlets (`Set-ADAccountPassword`), or integrate with AWS SSO for self-service resets.

**79. Explain automated user provisioning using AWS Lambda and Directory APIs.**
*Answer:* Trigger Lambda from events (e.g., HR system webhook), use AWS SDK to call DS APIs or LDAP libraries to create users/groups in AD.

**80. How do you decommission an AWS Managed Microsoft AD directory?**
*Answer:* Remove domain-joined instances, remove trusts, then delete the directory via console or CLI (`aws ds delete-directory`).

**81. Describe best practices for directory backups.**
*Answer:* Rely on daily automated snapshots, take manual snapshots before major changes, and copy snapshots cross-region for DR.

**82. How do you manage cross-region read replica creation?**
*Answer:* Enterprise edition allows read-only replicas in other regions via `CreateMicrosoftAD` with `ReplicaSettings`. Monitor replication health.

**83. Explain how to perform domain upgrades or forest functional level changes.**
*Answer:* AWS Managed AD auto-manages functional levels. For custom schema or forest-level changes, coordinate with AWS Support.

**84. How do you maintain directory health during heavy replication loads?**
*Answer:* Monitor `ReplicaLag`, scale network bandwidth, schedule large changes off-peak, and validate replication topology.

**85. Describe common maintenance window practices for AWS Managed Microsoft AD.**
*Answer:* Define windows during low usage, document changes, notify stakeholders, and monitor logs before/after upgrades.

---

### 6. Troubleshooting & Monitoring (86–100)

**86. How would you troubleshoot replication failures between domain controllers?**
*Answer:* Check CloudWatch Logs for errors, use `repadmin /showrepl` and `dcdiag` on a domain-joined EC2 instance, verify network and DNS.

**87. What CloudWatch metrics are available for Managed Microsoft AD?**
*Answer:* As previously: CPUUtilization, FreeStorageSpace, ReplicaLag, HealthCheckStatus, StatusCheckFailed.

**88. How do you analyze event logs for directory issues?**
*Answer:* Enable Windows Event Forwarding to CloudWatch, then filter Security, System, and DirectoryService event IDs for replication or auth errors.

**89. Explain using `repadmin` and `dcdiag` on domain-joined EC2 instances.**
*Answer:* Install Windows AD tools, run `repadmin /replsummary` to view replication health, `dcdiag` to diagnose DC functionality.

**90. How do you diagnose DNS resolution problems in the directory?**
*Answer:* Use `nslookup` against DC IPs, verify DHCP options, check Route 53 forwarding rules if used, and review security group rules.

**91. Describe troubleshooting steps for failed LDAP binds.**
*Answer:* Verify correct bind DN/password, test with `ldapsearch` or Ldp.exe, check LDAPS certificate validity and port access.

**92. How do you investigate authentication latency issues?**
*Answer:* Correlate Event IDs for logon times, monitor network latency to DCs, check CPU and memory on DCs via CloudWatch.

**93. Explain how to capture network traffic for AD protocols.**
*Answer:* Use packet capture tools (Wireshark) on domain-joined EC2 instances with mirror sessions or VPC Traffic Mirroring.

**94. How do you fix time synchronization issues between DCs?**
*Answer:* Ensure EC2 hosts sync to Amazon Time Sync Service, configure Windows Time Service with `w32tm` commands, and verify with `w32tm /stripchart`.

**95. Describe how to resolve trust relationship errors.**
*Answer:* Reset secure channels using `netdom reset` or `Test-ADServiceAccount`, verify connectivity and credentials used for trust creation.

**96. How do you handle orphaned FSMO roles in recovery scenarios?**
*Answer:* For Managed AD, AWS handles FSMO roles. In custom AD, use `ntdsutil` to seize roles to a healthy DC.

**97. Explain troubleshooting LDAP referral loops.**
*Answer:* Use LDAP tracing, inspect referral URLs, ensure correct forest and domain naming contexts, and fix misconfigured trusts.

**98. How do you recover from accidental schema corruption?**
*Answer:* Restore directory from snapshot prior to corruption in a test VPC, extract LDIF of schema, and reapply correct schema via trusted restore.

**99. Describe steps to diagnose CloudWatch Logs delivery failures.**
*Answer:* Check IAM role for CloudWatch Logs permissions, verify log group exists, and inspect DS console for log publishing errors.

**100. How do you simulate and test disaster recovery plans?**
*Answer:* Use manual snapshots to restore to a separate VPC or region, validate domain join, DNS, and application connectivity as part of drills.

---

### 7. Advanced Topics & Best Practices (101–110)

**101. Discuss the considerations for multi-region deployments of Managed Microsoft AD.**
*Answer:* Use Enterprise edition for cross-region replication. Plan for latency, data transfer costs, and failover. Leverage read replicas for local reads.

**102. How can you integrate AWS Managed Microsoft AD with AWS SSO?**
*Answer:* Configure AWS SSO to use Managed AD as an external identity source. Sync AD groups, map to permission sets, and enable MFA via AD policy.

**103. How can you automate AWS Managed Microsoft AD deployment using Terraform?**
*Answer:* Use `aws_directory_service_directory` with type `MicrosoftAD`, set `name`, `password`, `edition`, and `vpc_settings`. Reference VPC and subnet resources.

**104. Describe integrating Managed Microsoft AD with AWS CloudFormation.**
*Answer:* Use `AWS::DirectoryService::MicrosoftAD` resource with properties for `Name`, `Password`, `Edition`, `VpcSettings`, and `Tags` in your template.

**105. How do you enable cross-account access to Managed Microsoft AD?**
*Answer:* Share directory via AWS RAM, accept the share in the consumer account, then register the shared directory for use in that account.

**106. What governance considerations apply to AWS Managed Microsoft AD?**
*Answer:* Enforce tagging, IAM least-privilege, AWS Config rules for encryption, snapshot retention, and monitor CloudTrail for directory changes.

**107. Explain how to deploy custom schema extensions in Managed Microsoft AD.**
*Answer:* Run `ldifde` or `ldapmodify` from a domain-joined EC2 instance with schema admin rights to import LDIF definitions, then verify replication.

**108. Discuss strategies for disaster recovery (DR) with Managed Microsoft AD.**
*Answer:* Use automated/manual snapshots, cross-region snapshot copy, restore drills, maintain documented RTO/RPO, and test restoration workflows.

**109. How can you integrate Managed Microsoft AD with third-party applications (e.g., Splunk, Jenkins)?**
*Answer:* Configure apps to use LDAP/LDAPS endpoints, create service accounts in AD, assign group memberships, and open necessary ports in security groups.

**110. What are the cost optimization best practices for AWS Managed Microsoft AD?**
*Answer:* Choose the right edition, delete unused directories and snapshots, consolidate directories, use tags for tracking, and monitor usage to identify idle resources.

---

*End of document*
