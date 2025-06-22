1–15: IAM Fundamentals
What is AWS IAM and why is it important?
Answer: IAM (Identity and Access Management) is AWS’s service for managing users, groups, roles, and permissions. It enables fine-grained access control to AWS resources, implementing the principle of least privilege, centralizing credentials management, and improving security posture.

Explain the difference between an IAM user and an IAM role.
Answer: An IAM user is an identity permanently associated with credentials (password, access keys). An IAM role is a temporary identity you assume, granting permissions without long-term credentials; ideal for cross-account access, EC2 instances, or AWS services.

What are IAM policies?
Answer: Policies are JSON documents that define permissions. They include statements that allow or deny actions on resources under certain conditions. Policies attach to users, groups, or roles to grant permissions.

Differentiate between managed policies and inline policies.
Answer: Managed policies are standalone, reusable JSON policy documents managed by AWS or the customer. Inline policies are embedded directly into a single user, group, or role; they maintain a strict one-to-one relationship.

What is the AWS-managed policy AdministratorAccess?
Answer: It’s an AWS-managed policy that provides full access to all AWS services and resources ("Action": "*", "Resource": "*"). It’s useful for admin accounts but should be used sparingly.

How does IAM authenticate API requests?
Answer: IAM uses AWS Signature Version 4: the client signs requests with their secret access key, and AWS verifies the signature matches the request parameters, timestamp, and credentials.

What is the root account in AWS?
Answer: The root user is the account owner with unlimited privileges. It is tied to the email address used to create the AWS account. Best practice: lock away root credentials and use IAM users/roles for day-to-day tasks.

Why should you avoid using root user credentials?
Answer: The root user has full unrestricted access; compromise or accidental misuse can lead to catastrophic results. Instead, create least-privilege IAM users and roles.

What is the principle of least privilege?
Answer: Granting identities (users/roles) only the permissions they need to perform their tasks—and no more—to reduce security risk.

How do groups simplify permission management?
Answer: IAM groups let you assign a set of permissions once and apply them to multiple users. If you need to change permissions, you modify the group’s policy rather than each user.

What is an IAM policy simulator?
Answer: A tool within the AWS console and API that simulates and tests which permissions a set of policies grants or denies for specific actions on specific resources.

Explain a policy statement structure.
Answer: A statement has "Effect" (Allow/Deny), "Action" (list of API operations), "Resource" (ARNs), and optional "Condition" (key-value pairs for conditional logic).

What are IAM conditions and how are they used?
Answer: Conditions refine policy logic using keys (e.g., aws:SourceIp, aws:RequestTag/${TagKey}) with operators (StringEquals, IpAddress). They allow context-aware permissions like restricting access by IP range or MFA status.

When do you need an explicit Deny in policies?
Answer: To override an Allow from another policy or to enforce compliance – for example, to block deletion of critical resources, even if other policies allow it.

What is policy evaluation logic in IAM?
Answer: AWS evaluates policies by: 1) gathering all relevant policies (identity-based, resource-based, SCPs, session policies, ACLs), 2) denies take precedence, 3) explicit allows if no deny, 4) implicit denies everything else.

16–30: Authentication & Credentials
Describe access keys and how you should manage them.
Answer: Access keys consist of an access key ID and secret access key for programmatic access. They should be rotated regularly, never embedded in code, and stored securely (e.g., AWS Secrets Manager).

What is AWS STS and how does it integrate with IAM?
Answer: AWS Security Token Service issues temporary security credentials for roles or federated users. IAM roles leverage STS to provide short-lived credentials.

Explain IAM identity federation.
Answer: Federation lets users log in to AWS using external identities (e.g., SAML 2.0, OIDC, Active Directory) without creating IAM users. AWS trusts the external IdP to authenticate the user.

What is AWS Single Sign-On (SSO)?
Answer: A managed service that centrally manages SSO access to multiple AWS accounts and cloud applications. It integrates with corporate directories and simplifies user provisioning.

How do instance profiles work?
Answer: An instance profile is a container for an IAM role that you attach to an EC2 instance. The instance can then retrieve the role’s temporary credentials via the instance metadata service.

What is the AWS CLI credential process?
Answer: The CLI checks (in order): ENV vars, shared credentials file (~/.aws/credentials), shared config file for SSO or profiles, EC2/ECS metadata credentials.

What is AWS Access Advisor?
Answer: A console feature showing service-level last accessed data for users, roles, and policies. It helps identify unused permissions for cleanup.

Describe how MFA works in IAM.
Answer: Multi-Factor Authentication adds a second authentication factor (time-based one-time passwords or U2F). You can enforce MFA via policies (aws:MultiFactorAuthPresent).

How can you enforce password policies?
Answer: In IAM account settings, you set rules for password length, complexity, expiration, reuse prevention, and require MFA for console sign-in.

What is credential report?
Answer: A downloadable CSV report of all IAM users and status of their credentials (password last used, access keys status, MFA enabled, etc.) for audit.

When would you use an IAM access key versus an IAM role?
Answer: Access keys for programmatic, long-lived usage (e.g., CI pipelines). Roles for temporary, on-the-fly permissions (EC2 instances, cross-account, federated access).

Explain cross-account access with roles.
Answer: You create a role in the target account trusting the source account’s principal (account ID or role). The source principal calls sts:AssumeRole to obtain temporary credentials in the target.

How do you revoke active sessions?
Answer: Use aws iam delete-virtual-mfa-device or aws sts revoke-session (for session-based IAM Identity Center). For console sessions, rotate critical credentials or tighten policies.

What are service-linked roles?
Answer: Predefined roles linked to AWS services, created automatically, with required permissions for that service to call AWS on your behalf.

Explain IAM session policies.
Answer: Session policies are passed in AssumeRole or federation calls to further restrict the permissions the assumed role inherits during that session. They cannot broaden permissions.

31–50: Policies Deep Dive
How do you restrict a policy to specific S3 buckets and objects?
Answer: In the policy’s "Resource" field, specify ARNs of desired buckets/objects, e.g., "arn:aws:s3:::example-bucket/*", and include "Action": ["s3:GetObject"].

What is the difference between "Resource": "*" and omitting the resource element?
Answer: "Resource": "*" explicitly applies to all resources. Omitting "Resource" is invalid—every policy statement must have a resource, except IAM permissions on principal-based policies.

How do policy variables work?
Answer: You can use placeholders like ${aws:username} in policy ARNs or conditions to dynamically reference the calling principal’s name.

Explain policy size limits and best practices to manage them.
Answer: Managed policies have a 6,144-character limit; inline policies 2,048 characters per identity. Use multiple smaller policies, leverage wildcards wisely, and reuse managed policies.

What are resource-based policies?
Answer: Policies attached directly to resources (S3 buckets, SQS queues, KMS keys, Lambda functions) that specify which principals can access that resource, independent of their identity-based policies.

How do you allow cross-origin S3 access?
Answer: Configure a CORS policy on the bucket specifying allowed origins, methods, headers, and max age in XML.

What is AWS Organizations SCP and how does it relate to IAM?
Answer: Service Control Policies are organization-level policies that define the maximum allowed permissions for accounts in an OU. SCPs can only restrict (deny); they cannot grant.

Can you attach both a managed policy and inline policy to the same role?
Answer: Yes; AWS evaluates all policies attached to the principal, and any Deny supersedes.

Explain how you would write a policy to deny all S3 delete actions except for a specific user.
Answer:

json
Copy
Edit
{
  "Effect": "Deny",
  "Action": "s3:Delete*",
  "Resource": "*",
  "Condition": {
    "StringNotEquals": {"aws:userId": "<allowed-user-id>"}
  }
}
What is IAM Access Analyzer?
Answer: A service that analyzes resource-based policies to identify unintended public or cross-account access, generating findings for remediation.

How can you lock down access to only requests from a corporate IP range?
Answer: Use a condition in policy:

json
Copy
Edit
"Condition": {"IpAddress": {"aws:SourceIp": ["203.0.113.0/24"]}}
What are tag-based permissions?
Answer: You can tag IAM principals and AWS resources, and write policies that grant or deny based on matching tags (e.g., ResourceTag/${TagKey}).

How do you prevent privilege escalation in IAM?
Answer: Deny actions like iam:CreatePolicyVersion, iam:PutRolePolicy, or iam:AttachUserPolicy unless explicitly required, and use SCPs to block those actions at the OU level.

Explain how to allow an EC2 instance to read from a specific DynamoDB table.
Answer: Create an IAM role with a policy granting dynamodb:GetItem on the table ARN, attach the role via instance profile to the EC2 instance.

What is a permissions boundary?
Answer: A policy that sets the maximum permissions an IAM principal (user or role) can have. When attached as a boundary, it prevents that principal from obtaining permissions beyond the boundary, even if their identity-based policies allow more.

When would you use a permissions boundary?
Answer: In large teams or delegated admin scenarios to ensure newly created users/roles by developers cannot escalate to admin privileges.

How do you enforce encryption for certain S3 buckets in IAM policies?
Answer: Use a condition requiring the s3:x-amz-server-side-encryption header in PutObject:

json
Copy
Edit
"Condition": {"StringEquals": {"s3:x-amz-server-side-encryption": "AES256"}}
What is the effect of wildcarding actions/resources in policies?
Answer: Wildcards simplify policies but can inadvertently grant excessive permissions. Use them very judiciously and with conditions.

Can you attach a resource-based policy to an IAM user?
Answer: No. Resource-based policies are attached to supported AWS resources, not principals.

How do you test whether a user can perform an action?
Answer: Use the IAM Policy Simulator or call Simulator API to simulate the action with the user’s attached policies.

51–70: Roles & Delegation
How do you create a cross-account role for AWS Lambda?
Answer: In Account B (target), define a role with AssumeRolePolicy trusting Account A’s Lambda execution role. In the Lambda function’s execution config in Account A, specify that role’s ARN via STS.

What is the trust policy in a role?
Answer: The role’s assume-role policy document specifying which principals (users, roles, services) are allowed to assume it.

Explain Web Identity Federation with Cognito.
Answer: Cognito Identity Pools let users sign in via social IdPs (Google, Facebook) or OIDC providers. Cognito exchanges the IdP token for temporary AWS credentials mapped to IAM roles.

What is the difference between Cognito User Pools and Identity Pools?
Answer: User Pools handle user registration/authentication (OAuth2), while Identity Pools provide AWS credentials to users federated via User Pools or external IdPs.

How do you restrict an IAM role to be assumed only from a specific source IP or VPC?
Answer: In the assume-role policy’s Condition, use aws:SourceIp or aws:SourceVpc context keys.

What is the use of sts:TagSession?
Answer: When calling AssumeRole, you can pass session tags; these tags propagate into the session and can be referenced in downstream policies.

How do you allow S3 access only when a role is assumed via MFA?
Answer: Write a condition in the bucket policy or role policy:

json
Copy
Edit
"Condition": {"Bool": {"aws:MultiFactorAuthPresent": true}}
Explain how to grant on-premises AD users access to AWS.
Answer: Establish federation via SAML 2.0 with AWS IAM Identity Provider for Microsoft Active Directory and define IAM roles mapped to AD groups.

What are ephemeral credentials?
Answer: Short-lived credentials issued by STS when assuming a role or federating, automatically expiring after their duration.

How do you expire tokens faster than default?
Answer: When calling AssumeRole, set the DurationSeconds parameter to a lower value (minimum 900 seconds).

How do you let an IAM role call STS to assume another role?
Answer: In the target role’s trust policy, include the first role’s ARN. Then in code, the first role’s credentials call AssumeRole.

What is resource tagging on IAM roles for cost allocation?
Answer: You can tag roles and later filter CloudTrail or AWS Billing reports by tags to allocate costs to teams/projects.

Describe how to rotate credentials for a role used by ECS tasks.
Answer: ECS tasks assume an IAM role dynamically each task invocation; no long-lived keys exist. Rotation is automatic via STS.

What is the default maximum session duration for a role?
Answer: 1 hour (3,600 seconds). You can extend up to 12 hours (43,200 seconds) via the role’s settings.

How do you delegate AWS account billing access to a user?
Answer: Grant them the AWS-billing group policy by activating the “AWSAccountActivityAccess” or “AWSBillingReadOnlyAccess” managed policies and enable IAM users to view billing.

Explain how AWS services assume roles (e.g., Lambda, EC2).
Answer: Each service has a distinct trust relationship in its default service-linked role allowing the service principal (e.g., lambda.amazonaws.com) to assume it.

What is the Amazon Resource Name (ARN) format for roles?
Answer: arn:aws:iam::<account-id>:role/<role-name>

How do you audit role assumptions?
Answer: Enable CloudTrail and filter for AssumeRole events. You can view which principal assumed which role and when.

What happens if you delete an instance profile attached to a running EC2 instance?
Answer: The EC2 instance loses its associated role immediately and can no longer retrieve temporary credentials from metadata.

Can you pass an IAM role from one AWS region to another?
Answer: IAM is global; roles exist globally, so they are usable in any region.

71–85: Best Practices & Security
List five IAM best practices.
Answer:

Use MFA everywhere.

Apply least privilege.

Rotate credentials regularly.

Monitor and audit with CloudTrail and Access Advisor.

Use roles instead of long-lived access keys.

How do you detect unused IAM entities?
Answer: Review IAM Access Advisor data (last accessed) and generate credential reports to find stale users, keys, and roles.

What is AWS Security Hub’s IAM Integration?
Answer: Security Hub ingests IAM best practice findings (e.g., root account MFA not enabled) and aggregates them alongside other security insights.

Explain how to mitigate the risk of long-lived credentials in S3.
Answer: Use pre-signed URLs, temporary STS credentials, and IAM roles attached to EC2 or Lambda instead of embedding access keys.

How can you centralize IAM management across multiple AWS accounts?
Answer: Use AWS Organizations with Service Control Policies, AWS SSO, and cross-account roles for centralized identity and permission management.

What is the recommended way to grant CI/CD pipelines AWS access?
Answer: Use dedicated IAM roles with narrowly scoped policies and assume those roles in the pipeline (via OIDC trust from GitHub Actions, CodeBuild, etc.).

Describe how you’d secure AWS root user.
Answer: Enable MFA, delete or rotate root access keys, apply a strong unique password, store credentials offline, and enable billing alerts.

How do you restrict console access to corporate network only?
Answer: Create an IAM group with a policy that denies iam:ChangePassword and other console actions unless aws:SourceIp matches your corporate IP range.

What monitoring checks should you implement for IAM?
Answer: Alerts for root login attempts, new IAM user creation, permission changes, policy attachment/detachment, and unusual AssumeRole events.

How do you implement Just-In-Time (JIT) IAM roles?
Answer: Use temporary roles via AWS SSO or custom STS workflows where roles live short sessions and require approval (e.g., via Lambda/Step Functions).

Explain how to secure access keys in an application.
Answer: Don’t store them in code or environment variables; use IAM roles, AWS Secrets Manager, or AWS Systems Manager Parameter Store with encryption.

What is AWS IAM Access Analyzer zone of trust?
Answer: The set of accounts and organizations you configure Access Analyzer to monitor. Anything outside is flagged as external access.

Describe an approach for rotating IAM user access keys automatically.
Answer: Use AWS Lambda triggered by CloudWatch Events to create a new key pair, store it securely, update the application, then deactivate and delete the old key after verification.

What’s the risk of giving broad administrator access to IAM itself?
Answer: A user with iam:* can create, modify, or delete any IAM entity, potentially impersonating others, escalating privileges, or locking out admins.

How do you decommission an employee’s AWS access?
Answer: Immediately disable or delete their IAM user, revoke active sessions, remove group memberships, and rotate any shared resources they accessed.

86–100: Advanced & Scenario Questions
Scenario: A developer needs temporary write access to S3 only between 9 AM–5 PM. How do you implement it?
Answer: Use a permissions boundary or session policy with a condition on aws:CurrentTime (e.g., DateGreaterThanEquals/DateLessThanEquals), combine with aws:SourceAccount and MFA condition.

How would you allow an external AWS account to assume a role in your account only if they include specific session tags?
Answer: In the trust policy add a condition:

json
Copy
Edit
"Condition": {"StringEquals": {"sts:RequestedSessionTags/Project": "X"}}
What troubleshooting steps would you take if an IAM role assumption fails with “AccessDenied”?
Answer:

Check trust policy for correct principal.

Verify calling principal’s permissions on sts:AssumeRole.

Ensure session policy or permissions boundary isn’t blocking.

Inspect CloudTrail for Deny events.

Explain how AWS Config rules can help enforce IAM best practices.
Answer: AWS Config provides managed rules (e.g., iam-password-policy, iam-root-access-key-check) that continuously evaluate compliance and alert on violations.

How do you ensure that a role’s permissions can never exceed a certain scope, even if policies change?
Answer: Attach a permissions boundary policy to that role; any additional permissions outside the boundary are automatically denied.

Describe the lifecycle of an SAML federation request.
Answer:

User authenticates to IdP.

IdP returns SAML assertion.

User posts assertion to AWS STS.

STS issues temporary credentials.

User uses credentials to call AWS APIs.

What are the limitations of IAM policies regarding JSON policy size, number of policies, etc.?
Answer:

Managed policy max size: 6,144 characters.

Inline policy per identity: 2,048 characters.

Max 10 managed policies per principal.

Max 5 inline policies per identity.

Max 1500 IAM roles per account (soft limit).

Explain how to restrict AWS CLI usage to only specified regions.
Answer: Use a condition:

json
Copy
Edit
"Condition": {"StringEquals": {"aws:RequestedRegion": ["us-east-1","us-west-2"]}}
How do you implement attribute-based access control (ABAC) in IAM?
Answer: Tag IAM principals and resources with matching key/value pairs, then write policies with aws:PrincipalTag/${TagKey} and ResourceTag/${TagKey} conditions.

Scenario: You need to allow a Lambda function in Account A to read a DynamoDB table in Account B securely. Outline steps.
Answer:

In B, create a role trusting the lambda’s service principal and Account A’s lambda execution role.

Attach a policy granting dynamodb:GetItem on the table.

In A, update Lambda’s execution role policy to allow sts:AssumeRole on that role’s ARN.

In code, call AssumeRole then DynamoDB.

What audit controls can you implement around IAM policy changes?
Answer: Use CloudTrail to log PutRolePolicy, AttachUserPolicy, CreatePolicy, and set up CloudWatch Events or AWS Config rule to detect changes and trigger notifications.

How would you recover if an IAM misconfiguration locked out all admins?
Answer: Use the root user (who cannot be locked out). Sign in as root with MFA to correct IAM policies or re-enable users.

Explain how you would structure IAM for a large organization with hundreds of teams.
Answer: Use AWS Organizations with OUs per business unit, deploy SCPs for broad guardrails, centralize identity via AWS SSO or federated IdP, create permission sets (roles) per job function, enforce ABAC for granular access.

What is the difference between iam:PassRole and sts:TagSession?
Answer: iam:PassRole permits passing an IAM role to an AWS service (e.g., EC2, Lambda). sts:TagSession allows attaching tags at assume-role time to sessions for ABAC.

How do you ensure that sensitive IAM actions (e.g., policy deletion) require approval?
Answer: Implement a change-management pipeline: require all IAM changes via Infrastructure as Code (CloudFormation/Terraform) through a central repo with pull-request reviews, use SCPs to prevent direct console/API changes, and alert on direct API calls.
