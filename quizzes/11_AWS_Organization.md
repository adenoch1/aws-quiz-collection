1. What is AWS Organizations and what primary problems does it solve?
Answer:
AWS Organizations lets you centrally manage and govern multiple AWS accounts in a multi-account environment. It simplifies billing with a single consolidated bill, streamlines account creation and grouping via Organizational Units (OUs), and enforces governance through policies (e.g., Service Control Policies). This helps enterprises scale securely and cost-effectively 
docs.aws.amazon.com
.

2. Define the terms “management account” and “member account.”
Answer:
The management account (formerly “master account”) is the root of an organization—able to create OUs, invite accounts, and apply policies. Member accounts are all other accounts in the organization; they inherit policies and billing from the management account 
docs.aws.amazon.com
.

3. What is an Organizational Unit (OU)?
Answer:
An OU is a logical grouping of accounts within the root. OUs help mirror organizational structure (e.g., Development, QA, Production) and enable inheritance of policies (SCPs, tag policies, etc.) 
docs.aws.amazon.com
.

4. Explain Service Control Policies (SCPs).
Answer:
SCPs are JSON-based policies that centrally restrict what services, actions, and regions member accounts can access. They don’t grant permissions themselves but define guardrails—controls are applied across entire OUs/accounts 
docs.aws.amazon.com
docs.aws.amazon.com
.

5. How do SCPs differ from IAM policies?
Answer:
SCPs set the maximum permissions an account can have (account-level guardrails), while IAM policies grant actual permissions to IAM identities within an account. Effective permissions = IAM policies ∩ SCPs 
docs.aws.amazon.com
.

6. What are Backup Policies in AWS Organizations?
Answer:
Backup policies let you centrally define AWS Backup plans (frequency, window, vault, IAM role) as JSON documents and apply them across accounts/OUs. They automate organization-wide resource backups 
docs.aws.amazon.com
.

7. What are Tag Policies?
Answer:
Tag policies standardize resource tagging across the organization—enforcing tag keys, value patterns, required tags, and case sensitivity. They help in cost allocation and resource management at scale 
docs.aws.amazon.com
.

8. Describe AI services opt-out policies.
Answer:
These policies control whether AWS AI services can collect and store customer content for service improvements. You can opt out organization-wide for one or multiple AI services. Historical non-required data is deleted upon opt-out 
docs.aws.amazon.com
.

9. What are Chat applications policies?
Answer:
They control which AWS accounts in an organization can be accessed via chat applications (e.g., Slack, Teams), adding a governance layer over chat-based AWS Console access 
docs.aws.amazon.com
.

10. Explain Resource Control Policies (RCPs).
Answer:
RCPs (formerly “Resource SCPs”) restrict API actions at the resource-level (e.g., specific S3 buckets). They complement SCPs by preventing unintended resource usage across accounts 
docs.aws.amazon.com
.

11. How do declarative policies work?
Answer:
Declarative policies let you define desired configurations for AWS services (e.g., Config rules) at scale; whenever AWS services add new features/APIs, the configuration is automatically maintained 
docs.aws.amazon.com
.

12. What is delegated administration?
Answer:
Delegated admin allows you to designate a member account as the administrator for a supported AWS service, enabling service-specific management without giving full root access 
docs.aws.amazon.com
.

13. How do you enable AWS Organizations in your root account?
Answer:
In the AWS Console’s Organizations page, click Create organization and choose Enable all features (to unlock SCPs, tag/backup policies, etc.) 
devopsschool.com
.

14. Which operations require using the management account?
Answer:
Creating the organization, enabling policy types, inviting/accounts creation, AWS Control Tower setup, and enabling delegated administrators—all require the management account 
docs.aws.amazon.com
.

15. How do you invite an existing account to your organization?
Answer:
Use aws organizations invite-account-to-organization (CLI) or Invite account in Console; then the target account owner accepts via email or console 
docs.aws.amazon.com
.

16. How do you remove a member account?
Answer:
Call aws organizations remove-account-from-organization or use Console. The account becomes standalone with its own billing but loses inherited policies 
docs.aws.amazon.com
.

17. What is the DescribeOrganization API?
Answer:
DescribeOrganization returns metadata about the organization—its ARN, feature set, available policy types, and overall structure 
docs.aws.amazon.com
.

18. Explain EnablePolicyType and DisablePolicyType.
Answer:
These APIs turn on/off specific policy types (e.g., TAG_POLICY, BACKUP_POLICY, AISERVICES_OPT_OUT_POLICY) in a given root. You must disable before removal and only the management account may call them 
docs.aws.amazon.com
.

19. How are policies inherited within OUs?
Answer:
Child OUs/accounts automatically inherit policies attached to their parent OU/root. Inheritance operators (@@, +, -) in JSON let you fine-tune overrides 
docs.aws.amazon.com
.

20. How do you view an account’s effective policies?
Answer:
Use DescribeEffectivePolicy (CLI/API) or Console’s View effective policy on the account’s Policies tab 
docs.aws.amazon.com
.

21. What is consolidated billing?
Answer:
All accounts in an organization share a single payment method. Charges roll up to the management account, simplifying cost tracking and volume discounts 
docs.aws.amazon.com
.

22. How does AWS Organizations integrate with AWS Control Tower?
Answer:
Control Tower uses Organizations to automate OU/account creation with pre-configured guardrails (SCPs, Config rules, CloudTrail), accelerating multi-account best practices deployment 
docs.aws.amazon.com
.

23. How can you automate account creation?
Answer:
Via CreateAccount API (CLI/SDK), CloudFormation StackSets, or Terraform/AWS CDK—scripts can spin up accounts, place them in OUs, and attach SCPs immediately 
docs.aws.amazon.com
.

24. What are the default service limits for AWS Organizations?
Answer:
By default, an organization can have up to 10 OUs per parent, 20 accounts per OU, and 10,000 member accounts. Limits are adjustable via Support tickets 
docs.aws.amazon.com
.

25. Explain the concept of policies as code with Organizations.
Answer:
You can store policies (SCPs, tag policies, backup plans) in source control and deploy them via CI/CD pipelines using AWS CLI/SDK, ensuring versioning and compliance 
docs.aws.amazon.com
.

26. How do you enforce cross-account IAM roles using Organizations?
Answer:
While Organizations itself doesn’t create roles, you can use SCPs to ensure only approved role ARNs can be assumed, and combine with AWS IAM Identity Center for cross-account access 
docs.aws.amazon.com
.

27. Describe how organizational hierarchies affect policy application.
Answer:
Policies attach at root → OUs → accounts. An OU’s policies apply to all nested OUs/accounts unless overridden. Deeper level attachments override higher ones based on inheritance rules 
docs.aws.amazon.com
.

28. What happens if there is a conflict between two SCPs?
Answer:
The most restrictive rule applies. If one SCP denies an action that another allows, the denial wins. Absence of an explicit allow results in deny 
docs.aws.amazon.com
.

29. How do tag policies help with cost allocation?
Answer:
By enforcing mandatory cost-center tags and consistent tag structure, you can reliably drive Cost Explorer and AWS Budgets reporting at scale 
docs.aws.amazon.com
.

30. Explain RCA use-cases for AWS Organizations.
Answer:
Root-cause analysis often uses centralized CloudTrail and Config enabled by Organizations policies—audit logs are enforced and aggregated for forensics 
docs.aws.amazon.com
.

31. How do you integrate AWS RAM with Organizations?
Answer:
Enable AWS RAM in the management account, then share resources (VPC subnets, License Manager, Service Catalog portfolios) across member accounts without cross-account APIs 
docs.aws.amazon.com
.

32. Describe how AWS IAM Identity Center works with Organizations.
Answer:
You set up IAM Identity Center (AWS SSO) in the management account and assign user/group access permissions to accounts/OUs—centrally managing identities across accounts 
docs.aws.amazon.com
.

33. What is a delegated administrator and how do you register one?
Answer:
A delegated admin is a member account authorized to manage a specific AWS service. Use RegisterDelegatedAdministrator API to assign that role—must be done from the management account 
docs.aws.amazon.com
.

34. How do you deregister a delegated administrator?
Answer:
Use DeregisterDelegatedAdministrator from the management account. All resources managed in that account revert to management account control 
docs.aws.amazon.com
.

35. What security considerations apply to the management account?
Answer:
Lock down root user, enable MFA, use strong IAM roles, minimize its everyday use, and audit root activities—compromise of this account jeopardizes the entire org 
docs.aws.amazon.com
.

36. How do you enable multi-region service access?
Answer:
SCPs can whitelist allowed regions. By default, accounts can access all regions; use an SCP to restrict usage to a region list for compliance 
docs.aws.amazon.com
.

37. Explain quota policies in Organizations (if supported).
Answer:
(Some AWS services allow service quota management via Organizations declarative policies—limiting resource counts centrally. Check individual service docs.) 
docs.aws.amazon.com
.

38. What is the maximum number of policy attachments per OU?
Answer:
You can attach up to 5 policies per OU (this includes SCPs, backup, tag, AI opt-out, chat)—quota subject to AWS limits documentation 
docs.aws.amazon.com
.

39. How do you audit policy changes?
Answer:
Enable Organization-wide CloudTrail (cannot be disabled by member accounts). Logs show AttachPolicy, DetachPolicy, and policy content changes for full audit trails 
docs.aws.amazon.com
.

40. Describe how to implement a sandbox OU for experimentation.
Answer:
Create a “Sandbox” OU, restrict regions and sensitive services via SCPs, enforce tagging and backup policies, and place dev/test accounts there to isolate risk 
docs.aws.amazon.com
.

41. What is the difference between “All features” and “Consolidated billing” only?
Answer:
“Consolidated billing only” allows cost sharing but no SCPs or advanced policies. “All features” unlocks full governance controls (SCPs, tag, backup, etc.) 
devopsschool.com
.

42. How can you migrate an existing AWS account into an Organization?
Answer:
Use Invite account or API; account loses existing consolidated billing relationship, accepts membership, then inherits org policies 
docs.aws.amazon.com
.

43. What happens if the management account’s support plan expires?
Answer:
Member accounts lose consolidated billing benefits but policy enforcement remains. You cannot change billing methods until support is renewed 
docs.aws.amazon.com
.

44. Describe best practices for structuring OUs.
Answer:
Use a shallow OU tree (max 2–3 levels), group by lifecycle (Production, Non-Prod), enforce strict SCP naming conventions, and limit policy attachments per OU to simplify inheritance 
docs.aws.amazon.com
.

45. How do you test an SCP before wide rollout?
Answer:
Attach it to a “Test” OU with non-production accounts, monitor API call errors for unintended denies, then attach to prod OUs once validated 
docs.aws.amazon.com
.

46. Explain Tag policy inheritance and overrides.
Answer:
Tag policies attached at root apply org-wide. Tag policies at OU/account can only add more constraints; they can’t loosen a root policy 
docs.aws.amazon.com
.

47. How do you implement custom guardrails using Organizations?
Answer:
Write SCPs that deny undesired APIs (e.g., ec2:TerminateInstances), attach to OUs, and combine with Config rules for runtime checks 
docs.aws.amazon.com
.

48. Describe chargeback versus showback with Organizations.
Answer:
Showback: report cost usage per account without actually billing them. Chargeback: programmatically allocate costs to business units and bill them internally, based on consolidated billing tags 
docs.aws.amazon.com
.

49. How do you restrict an OU to a subset of AWS Regions?
Answer:
Create an SCP that Deny actions when aws:RequestedRegion is not in allowed list, attach to that OU 
docs.aws.amazon.com
.

50. What is the IAM role trust policy required for a delegated administrator?
Answer:
The service being delegated requires you to create a role in the designated account with a trust policy allowing organizations.amazonaws.com to assume it. See service-specific docs 
docs.aws.amazon.com
.

51. Explain how Organizations can enforce CloudTrail and Config.
Answer:
Use a management policy to enforce CloudTrail and Config at org level (via control tower or direct policies). CloudTrail can be org-wide and immutable; Config recorder can be set by backup/declarative policy 
docs.aws.amazon.com
.

52. How do you integrate AWS Security Hub with Organizations?
Answer:
Register Security Hub as a delegated administrator in the management account; it can then centrally aggregate findings across all member accounts 
docs.aws.amazon.com
.

53. What is a resource share in AWS RAM via Organizations?
Answer:
A resource share is a container specifying which resources (VPC subnets, License Manager configurations) are shared and with which OUs or accounts 
docs.aws.amazon.com
.

54. Describe how to automate OUs and policies via CloudFormation.
Answer:
Use the AWS::Organizations::* CloudFormation resources (AWS::Organizations::Organization, AWS::Organizations::OrganizationalUnit, AWS::Organizations::Policy, AWS::Organizations::PolicyAttachment) in templates and StackSets 
docs.aws.amazon.com
.

55. How do you set up drift detection for Organizations resources?
Answer:
Currently Drift Detection is for CloudFormation stacks; you can detect drift on the stacks managing your Org resources via StackSets 
docs.aws.amazon.com
.

56. What are the considerations for using Organizations in GovCloud?
Answer:
You must have a management account in GovCloud (US-West) and enable Organizations there. Some policy types may require GovCloud-specific endpoints 
docs.aws.amazon.com
.

57. How do you handle tag policy exceptions?
Answer:
Tag policies cannot have exceptions—they only tighten constraints. For resource exceptions, place those accounts in separate OUs without the tag policy 
docs.aws.amazon.com
.

58. Explain hierarchical policy evaluation in Organizations.
Answer:
Policies are evaluated top-down: root → parent OUs → child OUs → account. Inheritance operators determine merging/overrides. The resulting union of denies and intersection of allows is applied 
docs.aws.amazon.com
.

59. Describe the cost of AWS Organizations.
Answer:
AWS Organizations is free; you pay only for the AWS resources used by member accounts. Advanced features (SCPs, OUs) incur no extra cost 
docs.aws.amazon.com
.

60. How do you encrypt policies at rest?
Answer:
Policies are managed by AWS and encrypted by AWS KMS under the hood; you don’t need to explicitly encrypt them. Access is controlled via Organizations IAM permissions 
docs.aws.amazon.com
.

61. Explain the CLI vs Console experience for Organizations.
Answer:
Console offers a guided UI for OUs, policies, and accounts. CLI/SDK allows scripting and automation (aws organizations create-account, create-policy, attach-policy, etc.) 
docs.aws.amazon.com
.

62. What OEM tools integrate with AWS Organizations?
Answer:
Terraform (hashicorp/aws provider), AWS CDK, CloudFormation, and third-party governance tools (e.g., CloudCheckr, CloudHealth) integrate with Organizations APIs 
docs.aws.amazon.com
.

63. Describe troubleshooting a failed CreateAccount request.
Answer:
Common issues: unsupported email, reaching account quota, billing limit, or required Root contact info missing. Check DescribeCreateAccountStatus for error details 
docs.aws.amazon.com
.

64. How do you handle email verification for new accounts?
Answer:
AWS Organizations sends a verification code to the new account’s email—this must be completed in the console or via API within the expiry window 
docs.aws.amazon.com
.

65. Explain best practices for SCP naming and versioning.
Answer:
Include OU/context in names (e.g., Deny-NonProd-EC2Terminate-v1), maintain JSON in Git with version tags, and include comments in descriptions for clarity 
docs.aws.amazon.com
.

66. How can Organizations help meet PCI DSS or HIPAA compliance?
Answer:
By enforcing SCPs to disable non-compliant services, centralizing CloudTrail logs, and automating Config rule deployment, you ensure baseline security controls organization-wide 
docs.aws.amazon.com
.

67. What is the Service Access Policies feature?
Answer:
Service Access Policies (delegated admin plus SCPs) let you control which accounts can access certain AWS services organization-wide. E.g., only certain accounts can spin up GuardDuty detectors 
docs.aws.amazon.com
.

68. How do you monitor organization events?
Answer:
Use EventBridge (formerly CloudWatch Events) with the Organizations event source—set rules on events like CreateAccount, InviteAccountToOrganization, etc. 
docs.aws.amazon.com
.

69. Explain how to restrict IAM permissions using Organizations.
Answer:
Attach SCPs denying iam:* except desired actions. Member accounts can’t override these denies; they still define IAM roles/policies within the SCP guardrails 
docs.aws.amazon.com
.

70. What are organization-level CloudWatch metrics?
Answer:
AWS doesn’t provide org-level metrics directly—use CloudWatch cross-account metric streams from member accounts or aggregated logs via EventBridge/CloudTrail 
docs.aws.amazon.com
.

71. How do you use Organizations with Infrastructure as Code?
Answer:
Define Organizations resources in Terraform or CDK constructs, check drift, and deploy via pipelines to ensure org structure and policies remain consistent 
docs.aws.amazon.com
.

72. What happens to billing when an account is removed?
Answer:
Removed accounts get standalone billing; they must add a payment method and can’t see past consolidated usage 
docs.aws.amazon.com
.

73. How do you ensure all accounts have a managed CloudTrail?
Answer:
Use an SCP to deny disabling CloudTrail API calls (cloudtrail:StopLogging), and/or deploy CloudTrail via CloudFormation StackSet across all accounts 
docs.aws.amazon.com
.

74. Describe drift detection for organization policies.
Answer:
While org policies aren’t directly drift-detectable, keep them in IaC and use pipeline checks (e.g., aws organizations describe-policy) to compare against source of truth 
docs.aws.amazon.com
.

75. How do quota increases work for Organizations?
Answer:
Submit a service limit increase request via Support Console for Organizations quotas (e.g., max accounts). Approved limits apply across the org 
docs.aws.amazon.com
.

76. Explain how Organizations supports cross-region disaster recovery.
Answer:
Use SCPs to restrict to DR regions, automate cross-region resource provisioning via StackSets, and share central backup vault policies across accounts 
docs.aws.amazon.com
.

77. What is the pricing model for Organization-wide AWS Config?
Answer:
AWS Config charges per resource recorded per account. Organizations only centralizes setup; cost is per-account resource count 
docs.aws.amazon.com
.

78. How do you handle Org-wide AWS Budgets?
Answer:
Create budgets in the management account with usage monitors across linked accounts. Use cost categories and filters based on tags enforced by tag policies 
docs.aws.amazon.com
.

79. Describe health events visibility across an Organization.
Answer:
Use AWS Health Organizational View (requires Business/Enterprise support) to see service events impacting accounts; can integrate with EventBridge 
docs.aws.amazon.com
.

80. How does Organizations integrate with AWS License Manager?
Answer:
License Manager can act as a delegated admin; you share license configurations across member accounts via AWS RAM, with policies enforced org-wide 
docs.aws.amazon.com
.

81. Explain managing tags via Organizations versus IAM policies.
Answer:
Tag policies enforce tag structure at org level; IAM policies can control tag usage in resource creation. Together they ensure consistent tagging and enforcement 
docs.aws.amazon.com
.

82. What are resource-level deny scenarios and which policy type handles them?
Answer:
Denying deletion of specific S3 buckets, or disallowing creation of public RDS snapshots. These are handled by RCPs (resource control policies) 
docs.aws.amazon.com
.

83. How do you manage organizational units programmatically?
Answer:
Use CreateOrganizationalUnit, ListOrganizationalUnitsForParent, MoveAccount, and DeleteOrganizationalUnit APIs or equivalent CLI commands 
docs.aws.amazon.com
.

84. Describe how to enforce an AWS Backup Vault org-wide.
Answer:
Create a backup policy specifying vault ARNs and attach it at root/OU so all accounts use the designated vault for backups 
docs.aws.amazon.com
.

85. How do you ensure all accounts opt out of AI service data collection?
Answer:
Define an AI services opt-out policy with optOut: ["*"] and attach to root; query effective policy to confirm no account is opted in 
docs.aws.amazon.com
.

86. What is AWS Control Tower best practice to complement Organizations?
Answer:
Use Control Tower blueprints to set up a landing zone with pre-configured OUs, SCPs, Config rules, and guardrails—then maintain with Organizations APIs 
aws.amazon.com
.

87. How do you clean up/decommission an Organization?
Answer:
Remove member accounts, close each account via Console or CLI, then use DeleteOrganization in management account. Ensure no active accounts remain 
docs.aws.amazon.com
.

88. Explain cost allocation tags and how Organizations enforces them.
Answer:
Cost allocation tags track resource usage across accounts. A tag policy can require keys like CostCenter, Project, ensuring accurate cost reporting 
docs.aws.amazon.com
.

89. What are SOA-level quotas and how does Organizations help manage them?
Answer:
Service quotas (e.g., EC2 vCPUs) aren’t managed by Organizations directly, but Organizations can enforce quotas via declarative policies if service supports it 
docs.aws.amazon.com
.

90. How do you handle Service-Linked Roles across an Organization?
Answer:
Use SCPs to ensure only approved service-linked roles exist. Delegate creation to designated accounts via delegated admin, and audit via CloudTrail 
docs.aws.amazon.com
.

91. Describe API throttling considerations for Organizations.
Answer:
Frequent calls to ListAccounts or AttachPolicy can hit API rate limits. Implement retries with exponential backoff and cache responses in automation scripts 
docs.aws.amazon.com
.

92. How can you enforce password policies org-wide?
Answer:
Organizations doesn’t manage IAM password policies. Use IAM Identity Center for centralized identity and password policies across accounts 
docs.aws.amazon.com
.

93. Explain versioning of backup policies.
Answer:
Backup policies are JSON documents; you can update them via UpdatePolicy, but prior versions aren’t automatically retained—you must version in source control 
docs.aws.amazon.com
.

94. How do you implement least privilege across an Organization?
Answer:
Combine SCPs to deny broad privileges, use IAM Identity Center for role-based access, enforce tag policies and Config rules to audit privilege usage 
docs.aws.amazon.com
.

95. What are best practices for managing policy attachments?
Answer:
Limit attachments per OU, prefer OU-level vs account-level for consistency, name policies clearly, and document their purpose in descriptions 
docs.aws.amazon.com
.

96. How does Organizations handle cross-account deployments?
Answer:
Use CloudFormation StackSets or Terraform with IAM roles in each account. SCPs ensure only allowed APIs are invoked during deployment 
docs.aws.amazon.com
.

97. Describe emergency break-glass procedures in Organizations.
Answer:
Create an emergency OU with minimal SCPs, deploy break-glass IAM roles, and have run-books to detach restrictive policies quickly 
docs.aws.amazon.com
.

98. How do you rotate keys for the management account?
Answer:
Regularly rotate root user access keys (if any) and service-linked role keys, use MFA, and store credentials in a secure vault. Prefer IAM roles over long-term keys 
docs.aws.amazon.com
.

99. What logging configurations are mandatory in Organizations?
Answer:
Organization-wide CloudTrail is enforced and cannot be disabled by members. Enforce AWS Config via backup or declarative policies for continuous compliance 
docs.aws.amazon.com
.

100. How do you stay up-to-date on new Organizations features and service integrations?
Answer:
Monitor the AWS Organizations What's New page, AWS re:Invent sessions, and subscribe to the AWS Blog for announcements
