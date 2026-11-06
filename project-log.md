# Project Log

Running journal of the build. Newest entries at the bottom. Keep it honest: include what broke.

---

## Phase 1: IAM setup

**Date:** <!-- YYYY-MM-DD -->

**Goal:** Stop using the root account and set up secure CLI access.

**Why:** Root has unrestricted power and can't be limited by policy. A scoped admin user plus CLI keys gives auditable, revocable access.

**What I did**
- Created AWS account and secured root (MFA, no access keys)
- Created IAM admin user and enabled MFA
- Installed and configured AWS CLI
- Created an SSH key pair for EC2 access
- Launched and terminated a temporary EC2 instance to verify access

**Verified by:** <!-- e.g. `aws sts get-caller-identity` output (redacted) -->

**Issues and fixes:** <!-- anything that went wrong -->

---

## Phase 2: VPC networking

**Date:** <!-- YYYY-MM-DD -->

**Goal:** Build the segmented network: `10.0.0.0/16` with public jumpbox, public web, and private DB subnets.

**Why:** Tiered subnets limit blast radius. Only what must face the internet does.

**What I did**
- Created VPC `10.0.0.0/16`
- Created subnets: `10.0.0.0/24` (jumpbox), `10.0.1.0/24` (web), `10.0.2.0/24` (DB, private)
- Attached IGW; public route table sends `0.0.0.0/0` to IGW
- <!-- NAT gateway and private route table, if done in this phase -->

**Verified by:** <!-- route table screenshots, reachability tests -->

**Issues and fixes:** <!-- ... -->

---

## Phase 3: Compute (jumpbox and web server)

**Date:** <!-- YYYY-MM-DD -->

**Goal:** Deploy the bastion and web server with tightly scoped security groups.

**Why:** The jumpbox is the only SSH entry point, so the web tier never exposes SSH to the internet.

**What I did**
- Created JUMP-SG: SSH from my IP only
- Created WEB-SG: HTTP/HTTPS from internet, SSH from JUMP-SG only
- Launched `projectx-jumpbox` (t3.nano, Ubuntu 22.04)
- Launched `projectx-web-svr` (t3.small, Ubuntu 22.04) and configured the web app
- Tested SSH hop: laptop → jumpbox → web server

**Verified by:** <!-- screenshots of SGs, successful hop, web page loading -->

**Issues and fixes:** <!-- ... -->

---

## Phase 4: Database
_Not started_
