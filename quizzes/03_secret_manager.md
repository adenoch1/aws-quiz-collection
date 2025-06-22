What is AWS Secrets Manager and why use it over storing secrets in plaintext?
Answer: AWS Secrets Manager is a fully managed service to securely store, rotate, and retrieve credentials (passwords, API keys, tokens). Unlike plaintext storage (e.g., in code or environment variables), Secrets Manager encrypts secrets at rest with AWS KMS, provides fine-grained IAM policies, audit logging via CloudTrail, and automatic rotation to reduce the risk of credential compromise.

How does AWS Secrets Manager encrypt your secrets?
Answer: Secrets are encrypted using a customer-managed or AWS-managed KMS key. When you create or update a secret, Secrets Manager calls KMS’s Encrypt API to encrypt the secret value, and Decrypt whenever you retrieve it. This leverages envelope encryption for scalability and security.

Explain how secret rotation works in Secrets Manager.
Answer: Rotation can be configured to run on a schedule (e.g., every 30 days). You provide a Lambda function that implements the four rotation steps (createSecret, setSecret, testSecret, finishSecret). Secrets Manager invokes the function, which updates the credential on the target service and then synchronizes the secret value stored.

What are the four rotation steps and their responsibilities?
Answer:

createSecret: Generate a new secret value (e.g., new password) and store it as a pending version.

setSecret: Apply the pending version to the target resource (e.g., update the database with the new password).

testSecret: Verify that the pending secret works against the target (e.g., connect to DB).

finishSecret: Mark the pending version as the current version and optionally clean up old ones.

How do you configure secret rotation without writing custom code?
Answer: AWS provides built-in templates for common engines (RDS, DocumentDB, Redshift). You choose the template and Secrets Manager auto-generates the rotation Lambda function, which you then customize minimally (just supply ARNs, user, etc.).

Can you rotate secrets for on-premises databases?
Answer: Yes: you deploy a Lambda in a VPC with connectivity (VPN/direct connect) to your on-premises DB. The rotation Lambda uses that connection to rotate credentials. You need to configure the VPC/subnets/security groups for Lambda.

What is the difference between a secret and a parameter in AWS Parameter Store?
Answer: Parameter Store (part of SSM) offers simple key-value pairs (Standard/Advanced tiers), but Secrets Manager is designed specifically for secrets—it supports automatic rotation, versioning, and built-in audit. Parameter Store Standard is free up to limits, whereas Secrets Manager incurs per-secret charges plus API calls.

How are secret versions handled in Secrets Manager?
Answer: Each secret can have multiple versions identified by staging labels (e.g., AWSCURRENT, AWSPENDING, AWSPREVIOUS). When rotation runs, it stages the new version as AWSPENDING, tests it, then promotes to AWSCURRENT, and optionally labels the old as AWSPREVIOUS.

How would you restrict which IAM principals can retrieve or manage specific secrets?
Answer: Use IAM policies that specify secretsmanager:GetSecretValue, secretsmanager:CreateSecret, etc., scoped by resource ARNs. You can also apply resource-based policies on the secret itself, allowing cross-account access.

Explain resource-based policies in Secrets Manager.
Answer: Unlike IAM user policies, resource-based policies are attached to the secret. They allow you to grant access to principals (even in other accounts) directly on that secret. You can specify conditions (e.g., source VPC endpoint).

How can you ensure that secrets are only accessed from within your VPC?
Answer: Combine VPC endpoint policies (Interface VPC Endpoint for Secrets Manager) with resource policies on the secret. The endpoint policy denies requests not originating from the VPC, and the secret’s policy similarly restricts access.

What AWS CloudTrail events does Secrets Manager emit?
Answer: Secrets Manager logs API calls such as GetSecretValue, CreateSecret, UpdateSecret, RotateSecret, etc. You can monitor and alert on sensitive operations (e.g., unexpected retrievals).

How do you retrieve a secret value from an EC2 instance at runtime?
Answer: The application uses the AWS SDK to call GetSecretValue, either directly or via the AWS CLI. Best practice is to run EC2 with an IAM role granting only secretsmanager:GetSecretValue on the specific secret.

What is the Secrets Manager caching client and why use it?
Answer: The AWS Secrets Manager caching client (available for Java, Python, .NET) caches retrieved secrets in-memory for a configurable TTL, reducing API calls and latency. It auto-refreshes before expiration.

Can you store non-text binary secrets?
Answer: Yes. Secrets Manager accepts binary data (Base64-encoded). The secret value parameter can take either SecretString or SecretBinary.

How does Secrets Manager integrate with Amazon RDS IAM authentication?
Answer: For RDS databases that support IAM DB authentication, you can store the IAM authentication token in Secrets Manager and configure rotation to call the RDS API for token generation. More commonly, you store a static user/password and rotate via Lambda.

Explain cross-account secret sharing.
Answer: Attach a resource policy to the secret granting a principal in another AWS account the necessary actions (e.g., secretsmanager:GetSecretValue). The principal must assume an IAM role in their account with permissions to call through.

How do you monitor for unauthorised attempts to access secrets?
Answer: Enable CloudTrail logging, ship logs to CloudWatch Logs or an SIEM, and set metric filters/alarms on AccessDenied or suspicious GetSecretValue calls, especially from unusual IPs or accounts.

What quotas and service limits should you be aware of?
Answer: By default, up to 5000 secrets per account, 10 versions per secret, API request rate of ~100 per second. You can request limit increases for some quotas.

How do you recover a deleted secret?
Answer: You enable a recovery window (7–30 days) when deleting. During this window, you can call RestoreSecret to cancel the deletion. After the window, the secret and all versions are permanently deleted.

What are the best practices for naming secrets?
Answer: Use a consistent path-like hierarchy (e.g., /prod/db/mysql/admin), include environment and service, avoid embedding credentials in the name, and maintain clarity.

How can you tag secrets and why is tagging useful?
Answer: You can tag secrets with key-value tags at creation or update. Tags help organize, control access (via IAM conditions), and drive cost allocation.

Describe how secrets replication works across regions.
Answer: You can configure replicas in other regions. Secrets Manager asynchronously replicates current and pending versions, KMS key usage must be enabled or an external key replicated as well. Consumers in other regions use low-latency local API calls.

What happens if your rotation Lambda fails?
Answer: The secret’s rotation status will be marked as Failed. AWS will send events to CloudWatch Events (EventBridge) for failed rotations. You need to troubleshoot the Lambda logs, fix the code or permissions, and manually retry the rotation if needed.

How do you grant a Lambda permission to manage a secret?
Answer: Create an IAM role for the Lambda with a policy allowing secretsmanager:PutSecretValue, GetSecretValue, DescribeSecret, etc., scoped to the secret’s ARN.

Can you use Secrets Manager with Kubernetes?
Answer: Yes. Use tools like AWS Secrets & Configuration Provider (ASCP) for Kubernetes, which can mount secrets directly into pods or update Kubernetes Secrets from Secrets Manager.

Explain encryption at rest vs encryption in transit for Secrets Manager.
Answer: At rest, KMS-managed encryption secures stored secret values. In transit, API calls to Secrets Manager are sent over TLS. You can enforce TLS 1.2+ via service-side settings and endpoint policies.

How do you handle secret version cleanup?
Answer: By default, Secrets Manager retains up to 10 versions. You can configure the DeleteOldVersions automatic cleanup in rotation Lambda (template supports this), or manually purge older staging labels.

What is the pricing model for Secrets Manager?
Answer: You pay per secret per month (currently $0.50/secret) plus per 10,000 API calls (~$0.05). Replicated secrets and rotation Lambdas incur additional Lambda execution fees.

How do you migrate secrets from Parameter Store to Secrets Manager?
Answer: Write a script (e.g., using AWS CLI or SDK) to list parameters, fetch their values, then create new secrets with those values. Update applications to point to Secrets Manager ARNs. Clean up old parameters after verification.

Can you store JSON documents as secrets?
