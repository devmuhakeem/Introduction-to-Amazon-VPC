# Introduction to Amazon VPC

A hands-on AWS lab where I built a VPC with both a public and private subnet using the VPC Wizard, then explored every networking component inside it to understand exactly what makes a subnet "public" or "private."

## Scenario
Rather than manually wiring up every networking component by hand, this lab used the AWS VPC Wizard to generate a complete VPC — then walked through each piece it created to understand the mechanics behind it.

## What I did

### 1. Created a VPC with the wizard
Built a VPC spanning 1 Availability Zone, with one public subnet (`10.0.25.0/24`) and one private subnet (`10.0.50.0/24`), a zonal NAT gateway, and no VPC endpoints.

### 2. Explored the Internet Gateway
Confirmed the VPC had an Internet Gateway attached — the component that makes any internet connectivity possible at all, and which imposes no bandwidth constraints since it's horizontally scaled and highly available by design.

### 3. Investigated what actually makes a subnet "public"
Checked the public subnet's route table and found two routes: a local route for traffic within the VPC's CIDR range, and a default route (`0.0.0.0/0`) pointing to the Internet Gateway. That second route is the entire reason it's considered public — it's reachable from the internet because its route table says so, not because of any inherent property of the subnet itself.

### 4. Reviewed the Network ACL
Looked at the subnet's Network ACL — a stateless firewall operating at the subnet level — and saw the default allow-all rule combined with an implicit deny-all catch-all rule for anything that doesn't match.

### 5. Investigated the private subnet by contrast
Checked the private subnet's route table and found no route to the Internet Gateway at all — instead, its default route pointed to the NAT gateway. That's the defining difference: no direct internet route means no inbound reachability from the internet.

### 6. Understood the NAT gateway's role
Confirmed the NAT gateway lets resources in the private subnet initiate outbound connections to the internet, while blocking anything on the internet from initiating a connection inward — outbound-only by design.

### 7. Reviewed the default security group
Looked at the VPC's default security group and noted its self-referencing rule: resources in the default security group can talk to each other, but all other traffic is denied by default — a conservative, safe starting point.

## Key takeaways
- "Public" and "private" subnet is entirely a routing table decision, not a fixed property — the exact same subnet becomes private the moment you remove its route to the Internet Gateway
- NAT gateways solve a specific, narrow problem: letting private resources reach out to the internet without ever being reachable from it
- Network ACLs and security groups operate at different layers (subnet vs. instance) and both default to a safe, restrictive posture — defense in depth starts with sensible defaults

## Tools
Amazon VPC, Internet Gateway, NAT Gateway, Route Tables, Network ACLs, Security Groups

---
*Completed as an AWS hands-on lab, including a knowledge check assessment.*
