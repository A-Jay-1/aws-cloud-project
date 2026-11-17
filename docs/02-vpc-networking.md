# 02: VPC Networking

## Goal

Build a segmented network with a public jumpbox subnet, a public web subnet, and a private database subnet, so each tier only has the exposure it needs.

## Why

A flat network means one compromised host can reach everything. Splitting the VPC into tiers limits the blast radius. Only the web tier faces the internet, administration goes through a single entry point, and the database has no route to or from the internet at all, so it stays unreachable even if a security group is misconfigured.

## Design

| Component         | Value              | Purpose                                                                                                         |
| ----------------- | ------------------ | --------------------------------------------------------------------------------------------------------------- |
| VPC               | `10.0.0.0/16`      | Isolated network for the project                                                                                |
| Public Subnet A   | `10.0.0.0/24`      | Jumpbox (bastion)                                                                                               |
| Public Web Subnet | `10.0.1.0/24`      | Web server                                                                                                      |
| Private DB Subnet | `10.0.2.0/24`      | Database tier, no internet route                                                                                |
| Internet Gateway  | attached to VPC    | Inbound/outbound for public subnets                                                                             |
| NAT Gateway       | in a public subnet | Outbound-only internet for private resources(deleted after required packages were installed to save on credits) |

## Routing

| Route table | Destination | Target | Used by |
|-------------|-------------|--------|---------|
| Public | `10.0.0.0/16` | local | Jumpbox and web subnets |
| Public | `0.0.0.0/0` | IGW | Jumpbox and web subnets |
| Private | `10.0.0.0/16` | local | DB subnet |
| Private | `0.0.0.0/0` | NAT | DB subnet <!-- only if configured in this phase --> |

**Key decision:** 
1. A subnet is "public" because its route table sends `0.0.0.0/0` to the IGW, not because of its name. The DB subnet's route table has no IGW route, which is what keeps it private. <!-- add anything you decided differently -->

## Verification

- Confirmed each subnet is associated with the intended route table

## Production hardening

- Spread subnets across multiple Availability Zones for resilience
- Add network ACLs as a second layer beside security groups
- Enable VPC Flow Logs for traffic visibility (planned for the monitoring phase)
- Use one NAT Gateway per AZ in production; here a single one keeps cost down
