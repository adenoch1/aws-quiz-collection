1. VPC Fundamentals
Q: What is a VPC in AWS and why is it used?
A: A Virtual Private Cloud (VPC) is a logically isolated section of the AWS Cloud where you launch AWS resources in a virtual network defined by you. It provides control over IP address ranges, subnets, route tables, network gateways, and security settings, enabling secure and customizable networking for your workloads.

Q: What are the default components created when you launch a default VPC?
A: AWS creates a default VPC in each region with a /16 IPv4 CIDR block, one public subnet per Availability Zone, an Internet Gateway (IGW) attached, default route tables, security groups, and network ACLs. Instances launched without specifying a subnet go into the default subnet with a public IP.

Q: How do you specify the IP address range for a VPC?
A: You choose an IPv4 CIDR block between /16 (65,536 addresses) and /28 (16 addresses). You can optionally add an IPv6 CIDR block. CIDR ranges cannot overlap with existing VPCs you peer with.

Q: Can you change the CIDR block of an existing VPC?
A: You cannot modify an existing IPv4 CIDR block, but you can add secondary IPv4 CIDR blocks (added ranges) and IPv6. To shrink or change the primary block, you must recreate the VPC and migrate resources.

Q: What’s the difference between a VPC and an EC2-Classic network?
A: VPC is the modern, recommended networking model with granular control over routing and security. EC2-Classic (now retired) did not provide network isolation or customer-defined IP ranges. All new AWS accounts use VPC only.

Q: Why would you enable DNS hostnames in a VPC?
A: Enabling DNS hostnames assigns public DNS names to instances with public IPs, making them reachable via hostname. This helps integrate with services (like ELB) and simplifies dynamic IP resolution.

Q: How many VPCs can you have per AWS account per region?
A: By default, you can create up to 5 VPCs per region, but this limit is adjustable via AWS Support. Soft limits apply to related resources as well (subnets, NAT Gateways, etc.).

Q: What’s the significance of AZ isolation within a VPC?
A: Subnets are mapped to a single Availability Zone (AZ). This ensures high availability—if one AZ goes down, resources in other AZs remain unaffected. Designing across AZs enhances fault tolerance.

Q: Explain VPC peering.
A: VPC peering connects two VPCs (same or different AWS account/region) via private IPv4 or IPv6, enabling routing of traffic between them. Peering is non-transitive; you must configure separate peering connections for each VPC pair.

Q: What are some limitations of VPC peering?
A: No transitive peering, no overlapping CIDR blocks, no edge-to-edge routing (cannot route from a peered VPC through a VPN/Direct Connect to another network), and manual route table updates required.

2. Subnets & IP Addressing
Q: What is a subnet in AWS VPC?
A: A subnet is a range of IP addresses in your VPC mapped to a single AZ. Instances launched in that subnet inherit its IP range and AZ location.

Q: How do public and private subnets differ?
A: Public subnets have a route to an Internet Gateway. Instances can have public IPs. Private subnets lack that route; internet-bound traffic must go through a NAT Gateway or proxy in a public subnet.

Q: How do you allocate IP addresses to instances?
A: You can choose to auto-assign a public IPv4 address on launch or manually assign Elastic IPs (EIPs). Private IPv4 addresses are assigned from the subnet’s CIDR.

Q: Explain IPv6 support in VPC.
A: You can assign a single AWS-owned /56 (or BYOIP) IPv6 CIDR block to your VPC. Subnets get /64 sub-blocks. Instances auto-assign IPv6 addresses and can route traffic natively to the internet via an egress-only internet gateway.

Q: What happens if you run out of IPs in a subnet?
A: Launch failures for new ENIs/instances. You must delete unused resources, resize (delete and recreate) the subnet with a larger CIDR, or add another subnet.

Q: Describe how secondary (additional) private IPs work.
A: You can assign multiple private IPv4 addresses per ENI (up to limits) for hosting multiple services or containers behind the same instance. This enhances multi-tenancy and high availability.

Q: What factors influence choosing your CIDR design?
A: Expected number of hosts per AZ, growth, non-overlap with on-premises networks (for VPN/Direct Connect), CIDR block size trade-offs (wasted addresses vs. limit), and future peering.

Q: How do you expand your VPC’s address range?
A: Add a secondary IPv4 CIDR block (up to 5 per VPC) to expand the pool without downtime. Then create new subnets in that range.

Q: Can subnets span multiple AZs?
A: No—each subnet is tied to a single AZ. For multi-AZ resilience, create identical subnets in each AZ.

Q: Explain the concept of “reserved IP addresses” in a subnet.
A: AWS reserves five IP addresses in each subnet: network address (.0), VPC router (.1), DNS (.2), future use (.3), and broadcast (.255). These cannot be assigned to instances.

3. Route Tables & Routing
Q: What is a route table?
A: A set of rules (routes) that determine where network traffic is directed. Each subnet is associated with one route table; a table can be associated with multiple subnets.

Q: How many route tables can you attach per VPC?
A: Up to 200 by default (soft limit adjustable). Each subnet must be explicitly associated; otherwise, it uses the main route table.

Q: What is the main route table?
A: The default table created automatically. All subnets without an explicit association use this. You can modify it but cannot delete it.

Q: How do you route traffic to the Internet?
A: Add a route with destination 0.0.0.0/0 pointing to the Internet Gateway (IGW). For IPv6, route ::/0 to the IGW.

Q: Why might traffic fail even with a correct route?
A: Missing security group or NACL rule, no IGW attachment, wrong association of the subnet to the table, or missing source/destination check disabled for NAT or appliance instances.

Q: What is a blackhole route?
A: A route pointing to a target that no longer exists (e.g., deleted peering, IGW, or NAT). Traffic to its destination is dropped.

Q: How do you route private subnet internet-bound traffic?
A: Private subnets route 0.0.0.0/0 to a NAT Gateway or NAT instance in a public subnet. Responses are sent back via the NAT’s Elastic IP.

Q: What’s the difference between static and dynamic routing in AWS VPC?
A: VPC route tables use only static (manually defined) routes. AWS-managed gateways (Transit Gateway, VPN) support dynamic routing protocols like BGP, but VPC route tables themselves do not.

Q: How do you route traffic between peered VPCs?
A: In each VPC’s route table, add a route whose destination is the peer’s CIDR block and target is the VPC peering attachment.

Q: Can you use transitive peering?
A: No—AWS does not allow transitive routing via VPC peering. Use Transit Gateway or Transit VPC patterns instead.

4. Internet Gateway & NAT
Q: What is an Internet Gateway (IGW)?
A: A horizontally scaled, redundant AWS-managed VPC component that allows bidirectional IPv4 and IPv6 internet traffic between instances in your VPC and the internet.

Q: Can you attach multiple IGWs to a VPC?
A: No—a VPC can have only one IGW. To isolate internet traffic, use multiple VPCs or route tables.

Q: What is a NAT Gateway and how does it differ from a NAT instance?
A: A NAT Gateway is a managed, highly available service that enables outbound IPv4 internet traffic for private subnets. NAT instances are self-managed EC2 instances running NAT software and require patching, scaling, and HA configuration.

Q: When would you use an Egress-Only Internet Gateway?
A: For IPv6-only subnets when you want outbound-only internet access; it prevents incoming traffic initiated from the internet.

Q: How do you configure high availability for NAT Gateways?
A: Create one NAT Gateway per AZ in a public subnet. For each private subnet, point to the NAT Gateway in the same AZ to avoid cross-AZ data charges and provide AZ-level fault tolerance.

Q: What are the cost considerations for NAT Gateways vs. NAT instances?
A: NAT Gateways incur per-hour and per-GB processing fees but require no management. NAT instances incur EC2 instance charges, potentially EIP charges, and require HA architecture (doubling cost) if you need availability.

Q: Can you use a NAT Gateway for IPv6 traffic?
A: No—NAT Gateways only support IPv4. For IPv6, use an egress-only IGW or proxy solutions.

Q: How do you disable source/destination checks?
A: Via the EC2 console or AWS CLI (modify-instance-attribute --no-source-dest-check) on instances acting as appliances (NAT instances, firewalls) to allow forwarding traffic.

Q: What happens if you delete an IGW that’s referenced in a route table?
A: The route becomes a blackhole. Existing connections drop; new connections fail until you update or remove the route.

Q: How would you force all outbound traffic to go through a firewall appliance?
A: Create a private subnet for the appliance, disable source/dest checks on its ENI, route 0.0.0.0/0 in private subnets to the appliance’s ENI, and ensure the firewall routes to the IGW.

5. Security Groups & Network ACLs
Q: What is a security group?
A: A virtual firewall at the instance/ENI level that controls inbound and outbound traffic based on allow rules. Stateful: return traffic is automatically allowed.

Q: What is a network ACL (NACL)?
A: A stateless firewall at the subnet level. You configure inbound and outbound rules; return traffic must be explicitly allowed.

Q: Which has precedence: security group or NACL?
A: Both apply; traffic must pass NACL rules first (subnet level) then security group rules. Both must allow traffic for it to flow.

Q: Why are security groups considered stateful?
A: They automatically allow response traffic for allowed inbound traffic, and vice versa.

Q: Describe how to lock down SSH access to EC2 instances.
A: In the security group, allow inbound TCP port 22 only from known IP CIDR blocks (e.g., your office’s public IP) and deny all others. Use NACLs to further restrict by subnet if needed.

Q: Can security groups reference other security groups?
A: Yes—inbound and outbound rules can specify another SG as the source/destination, enabling micro-segmentation.

Q: How many rules can you have per NACL and per security group?
A: Default limits: NACL—20 rules (10 inbound, 10 outbound) plus the default rule; Security group—60 inbound, 60 outbound rules (soft limits adjustable).

Q: What is the default behavior of an implicit deny?
A: Any traffic not explicitly allowed by a security group rule is denied; NACLs also deny anything not matching an allow rule (except the default NACL allows all).

Q: How do you log denied traffic in VPC?
A: Enable VPC Flow Logs at the VPC, subnet, or ENI level. Choose to capture accept and/or reject traffic to analyze with CloudWatch Logs or S3.

Q: When would you use NACLs over security groups?
A: For subnet-level controls (e.g., cross-account isolation), deny rules (SGs can’t explicitly deny), or to limit ephemeral port ranges for stricter network boundary enforcement.

6. VPC Peering & Transit Gateway
Q: Explain AWS Transit Gateway (TGW).
A: A managed network transit hub that connects hundreds of VPCs and on-prem networks via attachments. Supports transitive routing, simplifies large-scale hub-and-spoke architectures, and can integrate with Direct Connect and VPN.

Q: How does TGW differ from VPC peering?
A: TGW provides transitive routing, centralized route management, higher scale, and integration with on-prem. Peering is point-to-point, requires manual route maintenance, and doesn’t support transitive traffic.

Q: What are the route propagation options with TGW?
A: Attachments can propagate their routes into TGW route tables. You can control which route tables learn which routes, enabling segmentation.

Q: Can you peer two Transit Gateways across regions?
A: Yes—TGW peering supports inter-region attachments, enabling global network topologies.

Q: What security considerations exist with TGW?
A: Route table segmentation, service control policies, IAM permissions for creating attachments/propagations, and flow log capture at TGW level.

Q: How do AWS Resource Access Manager (RAM) and VPC sharing relate?
A: RAM lets you share subnets of a VPC with other AWS accounts in your Organization. Shared subnets allow attached instances in different accounts to communicate in the same VPC without peering.

Q: What limitations apply to Transit Gateway attachments?
A: Limits per TGW: attachments (default 5,000), route tables (default 20), propagated routes per table (default 10,000). Soft limits adjustable.

Q: Can you peer a TGW to a VPC in another account?
A: Yes—VPC attachments support cross-account. You share the subnet via RAM or accept attachment requests.

Q: Describe Transit VPC pattern (legacy).
A: A self-managed approach using EC2 instances (routers), BGP, and VPNs to create a hub-and-spoke network. Superseded by TGW but still used when custom routing or appliances are required.

Q: How would you migrate from VPC peering to Transit Gateway?
A: Establish TGW, attach each VPC, update route tables to point to TGW instead of peering, remove peering connections, and verify connectivity and permissions.

7. VPC Endpoints & PrivateLink
Q: What is a VPC endpoint?
A: A private connection between your VPC and supported AWS services or customer/partner services powered by PrivateLink, without using the internet.

Q: Describe an Interface VPC Endpoint.
A: An ENI with private IPs in your subnets that serves as an entry point to AWS services or PrivateLink-powered SaaS. Charges apply per hour and per GB.

Q: Describe a Gateway VPC Endpoint.
A: Routes added to route tables that direct traffic for supported AWS services (S3, DynamoDB) to the AWS network via a highly available AWS-managed gateway. No hourly charge, only data processing fees.

Q: What services support Gateway Endpoints?
A: Amazon S3 and DynamoDB.

Q: How do you secure Interface Endpoints?
A: Use Security Groups to restrict which resources can access the endpoint. Implement IAM endpoint policies to control service actions and principals.

Q: What is AWS PrivateLink?
A: A technology powering Interface Endpoints that delivers services privately inside your network. Partners can publish services into VPCs via PrivateLink.

Q: How do you connect to a PrivateLink-powered SaaS?
A: Subscribe to the service in the AWS Marketplace (if required), create an Interface Endpoint in your VPC pointing to the service’s endpoint service name, and authorize cross-account access.

Q: Can Interface Endpoints be accessed across AZs?
A: Yes—you create one endpoint per AZ for HA. DNS resolves to the nearest healthy endpoint.

Q: What DNS options exist for endpoints?
A: AWS provides regional DNS names that resolve to endpoint IPs. You can override with private hosted zones for custom domain names.

Q: How do you monitor VPC endpoint usage?
A: Enable VPC Flow Logs on the endpoint’s ENIs, use CloudWatch metrics (bytes in/out), and AWS CloudTrail for endpoint API calls.

8. Connectivity (VPN & Direct Connect)
Q: What are AWS Site-to-Site VPN options?
A: AWS-managed IPsec VPN (static or dynamic BGP), AWS Transit Gateway VPN attachments, and third-party appliances in the VPC.

Q: How do you set up a dynamic VPN?
A: Create a Customer Gateway for your on-prem router, a Virtual Private Gateway (VGW) attached to your VPC, then a VPN connection selecting dynamic routing (BGP). Configure your router’s BGP peering.

Q: What is AWS Direct Connect?
A: A dedicated network connection from your premises to AWS for lower latency, higher bandwidth, and reduced data transfer costs.

Q: How does Direct Connect integrate with a VPC?
A: Through a Direct Connect Gateway or a private Virtual Interface to a Virtual Private Gateway (VGW) or Transit Gateway, enabling access to one or multiple VPCs.

Q: What is a Direct Connect Gateway?
A: A global endpoint to which you can attach VGWs or TGWs across multiple regions, simplifying multi-region connectivity.

Q: Explain AWS Client VPN.
A: A managed OpenVPN-based client-to-site VPN solution allowing remote users to securely connect to your VPC using certificates or Active Directory authentication.

Q: What are the modes of Client VPN?
A: Split-tunnel (only VPC subnets routed) or full-tunnel (all user traffic goes through the VPN), configurable via the client configuration file.

Q: How do you achieve HA for Site-to-Site VPN?
A: AWS automatically provides two tunnels to different AWS endpoints. You configure your on-prem device to use both and fail over via BGP or routing metrics.

Q: What are potential routing conflicts with VPN and Direct Connect?
A: Overlapping CIDR ranges between on-prem and VPC, route priority between VPN, Direct Connect, and TGW, and BGP AS number conflicts.

Q: How do you troubleshoot VPN connectivity issues?
A: Check CloudWatch VPN metrics (TunnelState, DataIn/Out), VPN logs, BGP peering status, security group/NACL rules, and on-prem device logs.

9. Advanced Features & Best Practices
Q: What is AWS IP Address Manager (IPAM)?
A: A service that centrally plans, tracks, and monitors IP addresses across AWS Regions and accounts. Automates CIDR assignment and compliance checks.

Q: How do you implement VPC Flow Logs at scale?
A: Use CloudWatch Logs or S3, apply filters for relevant traffic, consider multiple Flow Log resources per VPC or per ENI for granular control, and aggregate/process with Athena or Elasticsearch.

Q: Explain VPC sharing vs. peering vs. Transit Gateway.
A: Sharing shares subnets within an Organization account; resources launch directly into the same VPC. Peering connects separate VPCs. TGW provides transitive, high-scale, hub-and-spoke.

Q: How do you secure inter-VPC traffic?
A: Use security groups referencing endpoints, enforce encryption (TLS/IPsec), use AWS Network Firewall or third-party appliances, and implement least-privilege IAM policies.

Q: What is AWS Network Firewall?
A: A managed stateful, and stateless firewall service that integrates with VPC to filter traffic in/out between subnets or from the internet, with rule groups for intrusion prevention and domain filtering.

Q: Describe segmentation using multiple VPCs vs. subnets.
A: Multiple VPCs provide strong isolation but increase complexity and peering/Transit Gateway needs. Subnets within a VPC simplify management but rely on NACLs and security groups for isolation.

Q: How do you enforce corporate network policies?
A: Use AWS Firewall Manager with AWS WAF, Shield, Network Firewall across Accounts, apply Service Control Policies, and use AWS Config rules to audit compliance.

Q: What logging and auditing features exist?
A: VPC Flow Logs, AWS CloudTrail, AWS Config, Security Hub checks, CloudWatch Alarms, and third-party SIEM integrations.

Q: What are some performance considerations in VPC design?
A: Cross-AZ data transfer costs, NAT Gateway throughput limits (10 Gbps), ENI and instance IP limits, and route table complexity.

Q: How do you plan for IP exhaustion in multi-account environments?
A: Use IPAM to centrally allocate non-overlapping CIDRs, reserve blocks for future growth, automate subnet creation with service quotas enforcement, and monitor usage.

10. Scenario-Based & Troubleshooting
Q: You can’t reach an EC2 instance’s public IP. What do you check?
A: Subnet’s route to IGW, IGW attachment, security group inbound rule, NACL rules, public IP assignment, instance’s OS firewall, and source/dest check.

Q: Instances in private subnets have no internet. Why?
A: Possible missing NAT Gateway, incorrect route table, no Elastic IP on NAT, subnet using main route instead, or source/dest check on NAT instance still enabled.

Q: Peered VPCs can’t communicate. What’s wrong?
A: Overlapping CIDRs, missing route table entries, missing security group rules, or peering request not accepted.

Q: Your Transit Gateway attachments show “blackhole.” Next steps?
A: Check if the VGW or VPC attachment exists, re-add propagation, verify account permissions, and ensure the attachment is in “available” state.

Q: Flow Logs show rejected traffic—how to filter by instance?
A: Create a flow log at the ENI level for that instance’s network interface and filter by its source or destination IP.

Q: You need to inspect HTTP traffic in a subnet. How?
A: Route traffic through a proxy or inspection appliance by adjusting route tables and source/dest checks on the appliance’s ENI.

Q: How do you migrate a production VPC with minimal downtime?
A: Use VPC peering for synchronization, launch resources in the new VPC alongside old, update DNS/ELB targets, cut over, then decommission.

Q: How can you ensure subnets in each AZ receive equal traffic?
A: Use an Application Load Balancer or Network Load Balancer with cross-zone load balancing enabled to distribute connections evenly.

Q: Your NAT Gateway is a bottleneck—what alternatives exist?
A: Scale out with multiple NAT Gateways in each AZ, or deploy NAT instances on larger EC2 types with auto-scaling groups and health checks.

Q: How would you secure VPC endpoints against misuse?
A: Apply IAM endpoint policies restricting actions and principals, use security groups to limit source IPs, enforce encryption in transit, and audit via CloudTrail.
