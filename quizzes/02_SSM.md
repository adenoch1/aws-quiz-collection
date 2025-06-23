1. Fundamentals & Architecture
What is AWS Systems Manager (SSM)?
Answer: AWS Systems Manager is a unified interface for viewing and managing your AWS and on-premises resources. It provides operational data, automates common tasks, and lets you manage patches, configurations, and run commands across fleets of instances without needing SSH or RDP access.

How does SSM differ from AWS Config?
Answer: AWS Config tracks resource configuration changes over time and provides compliance auditing. SSM focuses on operational tasks—running commands, patching, gathering inventory, and configuration management. Config is for auditing and drift detection; SSM is for active management.

What components make up the SSM architecture?
Answer:

SSM Agent: Installed on each managed instance to communicate with the SSM service.

Systems Manager service endpoints: APIs that process requests (Run Command, Automation, etc.).

Parameter Store backend: Secure storage for parameters.

Document repository: JSON/YAML “runbooks” that define actions.

Instance profile roles: IAM roles granting instances permission to call SSM APIs.

What platforms does SSM support?
Answer: SSM Agent supports:

Amazon Linux / Amazon Linux 2

RHEL, CentOS, Ubuntu

Windows Server versions

macOS (via Session Manager only)

On-premises servers (Windows/Linux) when registered as managed instances.

How do managed instances register with SSM?
Answer: Instances assume an IAM instance profile with a policy granting ssm:UpdateInstanceInformation and related permissions. The SSM Agent then uses its role-linked credentials to call the Systems Manager APIs and become a “managed instance.”

Explain the SSM Agent’s communication model.
Answer: The agent polls SSM service endpoints over HTTPS. It periodically sends inventory data and polls for new commands or automation steps. Commands are retrieved, executed locally, and results posted back to the service.

How is SSM secured?
Answer:

IAM policies/roles restrict which APIs and resources users or instances can access.

KMS encryption for Parameter Store SecureString and Automation documents.

VPC endpoints (PrivateLink) allow agent traffic via private network.

CloudTrail logs all SSM API calls.

1–15: Core Concepts & Architecture
What is AWS Systems Manager and what problems does it solve?
Answer: AWS Systems Manager (SSM) is a unified interface for operational data, automating tasks across AWS resources. It solves configuration drift, secrets management, inventory collection, remote command execution, patch management, and more—across EC2, on-premises servers, and hybrid environments.

Explain the role of the SSM Agent.
Answer: The SSM Agent is a lightweight piece of software installed on managed instances (EC2 or servers). It polls the Systems Manager service for commands and configuration, executes them (Run Command, Patch, Automation workflows), and reports results back.

How does Systems Manager authenticate and authorize agent calls?
Answer: The agent uses the instance’s IAM role. When the agent makes API calls to SSM, they’re signed with temporary credentials from the EC2 instance metadata service (IMDS). IAM policies attached to the role control which SSM APIs and resources the agent can use.

Describe the difference between managed instances and hybrid activations.
Answer: Managed instances are EC2 instances with SSM Agent and proper IAM role. Hybrid activations let you register on-premises servers or VMs as managed instances by creating an activation (with code and ID) and configuring the agent to use it.

What regional endpoints does SSM use, and why might you use a private SSM endpoint?
Answer: SSM has regional public endpoints (e.g., ssm.us-east-1.amazonaws.com). Private endpoints via AWS PrivateLink let you keep agent traffic within your VPC, improving security and reducing internet exposure.

How does Systems Manager integrate with AWS Organizations?
Answer: You can enable delegated administration and service-managed associations across member accounts. You define permissions centrally in the management account to apply SSM documents, patch baselines, and Automation workflows organization-wide.

What is Systems Manager Inventory and what data does it collect?
Answer: Inventory gathers metadata from instances: installed applications, OS patches, network configurations, file inventory, custom metadata. Data is stored in SSM Inventory database and can be queried via AWS CLI, console, or AWS Config.

Explain Parameter Store. How does it differ from AWS Secrets Manager?
Answer: Parameter Store holds configuration data (strings, SecureStrings, StringLists). SecureStrings are encrypted using KMS. Secrets Manager is specialized for credentials, offers automatic rotation, built-in integrations, and higher throughput SLA. Parameter Store is free-tier up to a point and better for static config.

What are the performance and throughput limits of Parameter Store?
Answer: By default, you get ~40 TPS for Standard parameters and ~30 TPS for SecureString parameters. Advanced parameters support higher throughput (~100 TPS), larger size (up to 8 KB), but incur charges.

How can you grant cross-account access to Parameter Store parameters?
Answer: Use a resource-based KMS key policy and parameter resource policy. Attach a policy to the parameter that grants ssm:GetParameter* to IAM principals in another account, and ensure KMS key policy allows decrypt.

Describe the architecture of Session Manager.
Answer: Session Manager uses a WebSocket over HTTPS to tunnel a shell (or SSH) session through the SSM service. No inbound ports or SSH keys needed. Session data can be logged to CloudWatch Logs or S3 via session policies.

What are Session Manager preferences, and how do you enforce secure logging?
Answer: Preferences let you configure logging destinations (CloudWatch, S3), encryption settings (KMS keys), idle time-outs, approved regions, and logging encryption. Enforce via Organization Service Control Policies or IAM permissions on SSM Session preferences.

How do you perform patch management with SSM?
Answer: Use Patch Manager: define patch baselines (Microsoft, Ubuntu, custom), assign them to instance groups via tags or Resource Groups, and schedule maintenance windows. The SSM Agent downloads and installs patches according to baseline rules.

Explain how Maintenance Windows work and how you schedule tasks.
Answer: Maintenance Windows let you register a schedule (cron/frequency), target instances, and tasks (Run Command, Automation, Patch, or Lambda). You define concurrency and error thresholds to control execution flow.

What is SSM Automation and how does it differ from Run Command?
Answer: Automation uses pre-built or custom Automation documents (playbooks) to orchestrate complex workflows across multiple services, with steps, approvals, and branching. Run Command executes single or ad-hoc commands on instances.

16–30: Parameter Store & Secrets
Q: How do you rotate a SecureString parameter automatically?
A: Use AWS Lambda and EventBridge. Schedule a rule to trigger a Lambda which retrieves the old secret, generates a new one, stores it as a SecureString, and updates dependent resources.

Q: What encryption options exist for Parameter Store?
A: Standard parameters use AWS-managed key. SecureString uses either AWS-managed KMS key (alias/aws/ssm) or a customer-managed KMS key (recommended for cross-account or audit logging).

Q: How can you reference a Parameter Store value in a CloudFormation template?
A: Use the intrinsic function:

yaml
Copy
Edit
MyParam: !Ref /myapp/config/db_password
or dynamic references:

yaml
Copy
Edit
DBPassword: '{{resolve:ssm-secure:/myapp/config/db_password:1}}'
Q: How do you audit access to Parameter Store values?
A: Enable CloudTrail data events for SSM API calls (GetParameter*, PutParameter, etc.). Use AWS Config managed rules to detect unencrypted parameters or unauthorized access.

Q: Describe how you handle large configuration files in Parameter Store.
A: For files > 4 KB, split into multiple StringList parameters or store in S3 (with encryption), and reference the S3 URL in a parameter.

Q: Can Parameter Store notify on changes?
A: Yes—use CloudWatch Events (EventBridge) on AWS API calls (PutParameter) to trigger SNS or Lambda notifications.

Q: How do you import multiple parameters in bulk?
A: Use the AWS CLI aws ssm put-parameter in scripts, AWS SDK loops, or AWS CloudFormation with AWS::SSM::Parameter resources in a template.

Q: What is an advanced parameter?
A: Advanced parameters support up to 8 KB, higher throughput, parameter policies (expiration, no-echo), but incur per-parameter monthly charges.

Q: Explain parameter policies.
A: You can attach policies to parameters: expiration (auto-delete after date), no-echo (mask in CloudWatch), and tiering (move to advanced). Useful for lifecycle management.

Q: How do you secure Parameter Store access in CI/CD pipelines?
A: Give the CI/CD role least-privilege via IAM policies scoped to specific parameters. Use dynamic references in buildspecs rather than exposing parameters in pipelines.

Q: Compare Parameter Store vs. S3 for storing application configurations.
A: Parameter Store provides encryption-at-rest integrated with KMS, versioning, and fine-grained access control via IAM, whereas S3 requires bucket policies and SSE. Parameter Store has size limits; use S3 for large blobs.

Q: How do you manage parameter versions and rollbacks?
A: Each PutParameter creates a new version. Use GetParameter --version to retrieve specific versions and DeleteParameter to remove unwanted ones. For rollbacks, update application to reference older version.

Q: What is the SecureString type and how is it different from String?
A: SecureString is encrypted at rest with KMS. When retrieved, it’s decrypted. String is plaintext. SecureStrings incur API throttling differences and require decryption permissions.

Q: How do you handle cross-region Parameter Store replication?
A: Parameter Store doesn’t replicate automatically; build a Lambda triggered on EventBridge to copy parameters to target regions via PutParameter.

Q: What are common gotchas with retrieving parameters in ECS tasks?
A: ECS tasks need execution role permissions for SSM. Use the awslogs-stream-prefix dynamic reference or inject via ssm secrets integration. Beware of container startup timeouts if SSM API calls lag.

31–45: Run Command & Session Manager
Q: How does Run Command maintain idempotency?
A: Many prebuilt documents (AWS-RunShellScript) detect previously applied changes, but custom documents must handle idempotency logic. You can include checksums or use OS package managers’ native idempotency.

Q: Explain how to target instances in Run Command.
A: Targets by instance IDs, tags, resource groups, or specify --document-name with a parameter targets=[{Key=tag:Env,Values=[prod]}].

Q: How do you capture Run Command output?
A: Outputs are stored in S3 or CloudWatch Logs if configured in the Document’s output S3/CloudWatch settings or via --output-s3-bucket-name flags.

Q: What security considerations apply to Run Command documents?
A: Documents can contain scripts; use least-privilege for the service role, restrict who can create/execute documents, and validate content before sharing.

Q: How do you create a custom Run Command document?
A: Define a JSON or YAML SSM document, specify schemaVersion, description, mainSteps with action types (e.g., aws:runShellScript), and register via aws ssm create-document.

Q: Describe how Session Manager tunnels SSH sessions.
A: You configure an SSH plugin to use Session Manager as a proxy. The plugin intercepts SSH and opens a port via SSM’s StartSession API, forwarding TCP traffic to the agent.

Q: How do you restrict Session Manager commands to approved documents?
A: Use the IAM condition key ssm:DocumentName in the policy for ssm:StartSession or ssm:SendCommand to restrict which documents users can run.

Q: Can you share Run Command output with multiple teams?
A: Yes—configure output to an S3 bucket with appropriate bucket policies granting cross-account or team access.

Q: Explain the difference between interactive and non-interactive sessions in Session Manager.
A: Interactive sessions provide a shell/PowerShell. Non-interactive sessions (Port forwarding, Exec) allow running specific commands or port forwarding without a shell.

Q: How do you audit Session Manager sessions?
A: Enable session logging to CloudWatch Logs or S3. Use CloudTrail for StartSession events. Integrate with CloudWatch Logs Insights to query session activity.

Q: What IAM permissions are required to start a Session Manager session?
A: ssm:StartSession, ssm:DescribeInstanceInformation, ssm:OpenDataChannel, ssm:OpenControlChannel, and KMS decrypt if logging encrypted.

Q: How do you terminate idle Session Manager sessions automatically?
A: Configure Session Manager preferences IdleSessionTimeoutSeconds to auto-close sessions after a specified period.

Q: Describe port forwarding with Session Manager.
A: Use aws ssm start-session --target i-0123 --document-name AWS-StartPortForwardingSession --parameters '{"portNumber":["22"],"localPortNumber":["2222"]}' to forward local port 2222 to instance port 22.

Q: What is the AWS-RunPowerShellScript document?
A: A built-in Run Command document for Windows that runs PowerShell commands. Supports parameters for commands array, workingDirectory, and executionTimeout.

Q: How do you debug a failed Run Command invocation?
A: Check the invocation status via GetCommandInvocation, inspect the CloudWatch Logs or S3 output, validate agent health (amazon-ssm-agent service), and review AWS CloudTrail for permission errors.

46–60: Patch Manager & Maintenance Windows
Q: What is a patch baseline, and what patch approval rules can you define?
A: A patch baseline is a collection of rules and exceptions controlling which patches to approve. You can filter by severity, classification, product, and schedule auto-approval after a compliance level or auto-approval delay.

Q: How do you exclude specific patches from a baseline?
A: List their patch IDs in the baseline’s “RejectedPatches” field, optionally with RejectedPatchesAction=ALLOW_AS_DEPENDENCY if needed for dependencies.

Q: Can you apply different patch baselines to different instance types?
A: Yes—use patch groups defined by instance tags and associate each tag (e.g., PatchGroup:WindowsServers) with the appropriate baseline.

Q: Explain how to schedule patching for Linux instances.
A: Create a maintenance window, register the AWS-RunPatchBaseline task specifying Operation=Install, targets (via tag), schedule cron expression, and define concurrency/error thresholds.

Q: How do you handle reboot behavior after patching?
A: Configure the RebootOption parameter in the patch task (RebootOption=RebootIfNeeded or NoReboot), and optionally add post-patch Automation steps to verify system health.

Q: What maintenance window roles and service roles are required?
A: Maintenance windows require a service role (AWSServiceRoleForMaintenanceWindows) for SSM to execute tasks, plus a target instance IAM role with SSM permissions.

Q: How do you monitor patch compliance?
A: Use the Inventory collected “PatchCompliance” data; query via AWS Console, CLI (aws ssm describe-instance-patch-states), or build dashboards in CloudWatch.

Q: Describe the difference between Scan and Install operations.
A: Scan detects missing patches without applying them; Install downloads and installs patches that meet baseline rules.

Q: How can you audit failed patch installations?
A: Check the maintenance window task invocation details, CloudWatch Logs for patch-manager logs in /var/log/amazon/ssm, and the instance’s logged patching errors.

Q: Explain how you would test patch baselines before production.
A: Create a test patch group tag on a small set of dev instances, associate with a “test” baseline, run maintenance window, verify stability, then roll to prod.

Q: What are common patching gotchas for Windows instances?
A: Windows may require reboots; patch KB IDs sometimes conflict; WSUS configurations can block SSM; permissions on the SYSTEM account may block patching.

Q: How does SSM handle patch dependencies?
A: The OS package manager resolves dependencies during Install. You can control behavior via AllowReboot, and baseline allows dependencies if marked.

Q: Can you use Automation in place of maintenance windows for patching?
A: Yes—Automation documents like AWS-ApplyPatchBaseline can be invoked directly or chained in a workflow, but maintenance windows provide scheduling and concurrency controls.

Q: How do you ensure patch windows don’t overlap across multiple groups?
A: Stagger cron expressions, use separate maintenance windows, or leverage SSM Automation workflows with built-in synchronization steps.

Q: What logging does Patch Manager provide?
A: Detailed patching logs in S3/CloudWatch (if configured), and instance logs at /var/log/amazon/ssm/ (Linux) or %ProgramData%\Amazon\SSM\Logs\ (Windows).

61–75: Automation & Advanced Workflows
Q: What is an Automation document schema version?
A: schemaVersion (e.g., "0.3") defines the document’s JSON/YAML schema. Newer versions support more step types, error handling constructs, and branching.

Q: How do you pass parameters between steps in Automation?
A: Use the Outputs section in a step and reference via {{ Steps.StepName.Outputs.OutputKey }} in subsequent steps.

Q: Explain error handling in Automation.
A: Use onFailure in a step to specify next steps on error, and maxAttempts plus timeoutSeconds to control retry behavior.

Q: How do you include approval steps in Automation?
A: Use the aws:approve action type. Define approvers (IAM ARNs), timeout periods, and rejection behavior.

Q: Describe how you would automate AMI patching and deprecation.
A: An Automation document that: (1) launches a test instance from AMI, (2) applies patches, (3) creates a new AMI, (4) tags old AMI as “Deprecated”, (5) notifies teams. Use branching and tagging steps.

Q: Can Automation invoke Lambda functions?
A: Yes—use aws:lambda:invoke action, passing payload and handling results via outputs.

Q: How do you share custom Automation documents across accounts?
A: Use resource-based policies on the document (UpdateDocumentDefaultVersion). Grant ssm:GetDocument, ssm:DescribeDocument, and ssm:StartAutomationExecution to other accounts.

Q: Explain how to debug a failing Automation execution.
A: Review execution history in console, inspect step-level logs and outputs, check CloudWatch Logs if enabled, and validate IAM roles and service-linked roles.

Q: What Service-Linked Role does Automation use?
A: AWSServiceRoleForSSM or AWSServiceRoleForMaintenanceWindows. Automation needs AWSServiceRoleForSSM to make calls on your behalf.

Q: How do you parameterize branch logic in Automation?
A: Use the aws:branch action, defining conditions (StringEquals, BoolEquals) on outputs or input parameters to choose subsequent steps.

Q: Describe how to trigger Automation on resource events.
A: Use EventBridge rules—e.g., on EC2 instance state-change events, schedule or trigger workflows via StartAutomationExecution.

Q: How does Automation handle long-running tasks?
A: Steps can specify timeoutSeconds. Overall execution defaults to 7 days. Long tasks can be broken into sub-documents or invoked via Lambda with asynchronous handling.

Q: Explain how to integrate Automation with Change Manager.
A: Use AWS Systems Manager Change Calendar and Change Manager workflows, embedding approval steps and blackout windows in Automation documents.

Q: What is the difference between ExecuteAutomation and StartAutomationExecution?
A: There is no ExecuteAutomation API; StartAutomationExecution begins a workflow. Execution history and details are retrieved via GetAutomationExecution.

Q: How can Automation help with incident response?
A: Pre-built incident response documents (e.g., AWS-RestartEC2Instance), custom workflows can isolate network, collect logs, or notify teams—triggered manually or via CloudWatch alarms.

76–90: Inventory, Distributor, Maintenance & Security
Q: How do you extend Inventory with custom data?
A: Create Inventory schema types with the aws:registerInventory Run Command plugin, collecting files, registry keys, or application data specified in JSON inventory configuration.

Q: Explain SSM Distributor.
A: Distributor packages and distributes software (RPM, MSI) to managed instances. You create packages in S3, register with SSM, then run InstallDocument to deploy.

Q: What is Session Manager Fleet Manager?
A: A console feature under Systems Manager Fleet Manager to view and manage instance details (OS, performance metrics), file systems, users, processes—without SSH.

Q: Describe how to enforce SSM Agent updates.
A: Use Distributor to publish the latest agent package to S3, then run a Distributor install document on your fleet via Run Command or Automation.

Q: How do you secure cross-region invocation of SSM documents?
A: Use IAM conditions on aws:RequestedRegion, service-linked VPC endpoints, and restrict via SCPs. Use regional KMS keys for each region.

Q: What encryption is in transit for SSM data?
A: TLS v1.2 for agent-to-service communications. When using PrivateLink, it’s still encrypted inside AWS network.

Q: How can you configure instance patch compliance drift alerts?
A: Use CloudWatch Events on PatchComplianceChangeNotification events to trigger SNS or Lambda notifications when compliance thresholds are breached.

Q: Explain parameter compliance scanning.
A: AWS Config managed rule ssm-parameter-compliance-check detects unencrypted or expired parameters. It evaluates Parameter Store parameters against policies.

Q: How do you manage SSM across thousands of instances?
A: Use tags/Resource Groups for targeting, CriticalPath parameter policies, use SSM Automation for bulk operations, monitor via dashboards in CloudWatch, and use private endpoints to reduce networking overhead.

Q: Can you integrate SSM with AWS Control Tower?
A: Yes—automate SSM document associations as part of landing zone guardrails, use Service Catalog portfolios containing SSM scripts for bootstrap in accounts.

Q: How do you troubleshoot ssm-agent connectivity issues?
A: Verify IAM role, IMDS accessibility, VPC endpoints or internet access, check agent logs, SSM endpoint DNS resolution, and security group/network ACL rules.

Q: What are service-linked roles in SSM?
A: AWS-created roles with predefined permissions. Key SL roles: AWSServiceRoleForSSM, AWSServiceRoleForEC2InstanceManagedBySSM, AWSServiceRoleForMaintenanceWindows.

Q: How do you upgrade SSM documents when newer schema versions are available?
A: Use aws ssm update-document-default-version or aws ssm create-document with --document-version. Test in staging before shifting default.

Q: Describe the impact of SSM API throttling.
A: Exceeding API quotas (e.g., DescribeInstanceInformation) causes ThrottlingException; implement exponential backoff, pagination, and caching to mitigate.

Q: How do you maintain configuration drift remediation?
A: Use State Manager associations: define desired state (Chef, Puppet, Ansible, SSM documents) and allow SSM to enforce at regular intervals or on compliance events.

91–100: Best Practices, Troubleshooting & Exam-Level Scenarios
Q: What are best practices for tagging to maximize SSM effectiveness?
A: Standardize tags (Environment, App, PatchGroup), use automation to enforce tag presence, and leverage Resource Groups for Run Command, patching, and State Manager targeting.

Q: How do you secure SSM Agent in immutable infrastructure?
A: Bake agent into golden AMIs via Distributor/Automation, set up PrivateLink, disable IMDS v1, enforce IMDSv2-only, and restrict IAM role permissions narrowly.

Q: You need to run commands on instances in a private subnet with no internet access. How?
A: Configure VPC endpoints for SSM (com.amazonaws.region.ssm, ssmmessages, ec2messages) and ensure proper route tables and security groups.

Q: How would you integrate SSM with third-party CMDB tools?
A: Export Inventory data via Data sync to S3/Glue/Lambda, transform to CMDB schema, and ingest via APIs or direct database connections.

Q: A Run Command fails with “AccessDenied”. What steps do you take?
A: Check the user’s IAM policy for ssm:SendCommand, the instance role for ssm:ListCommands/ssm:ListCommandInvocations, and KMS decrypt if SecureString is used.

Q: How do you ensure least-privilege in SSM document creation?
A: Separate roles for document authors vs executors; use KMS grants for SecureStrings; restrict ssm:CreateDocument to approved directories or naming conventions via conditions.

Q: How do you automate SSM document testing and CI/CD?
A: Store documents in Git, use CodeBuild to validate JSON/YAML schema with aws ssm validate-document CLI, then deploy via CloudFormation or SDK.

Q: What metrics would you track for SSM health and performance?
A: API latency and error rates (CloudWatch SSM metrics), agent heartbeats (LastPingDateTime), command success/failure counts, session launch times, patch compliance percentages.

Q: How do you decommission an on-prem managed instance?
A: Deregister the instance via aws ssm delete-managed-instance or uninstall the agent, remove the hybrid activation, and revoke any credentials or IAM role associations.

Q: Propose an architecture for zero-trust remote administration using SSM.
Answer:

Enforce IMDSv2-only for instance metadata requests

Use Session Manager (no SSH keys or open ports) over PrivateLink

Centralized logging of sessions to encrypted CloudWatch Logs/S3

IAM policies granting session-only privileges

Periodic inventory and patch compliance scans via State Manager/Automation

Multi-factor approval steps in Automation for privileged actions

Integrated with AWS Security Hub and GuardDuty for anomaly detection
