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
