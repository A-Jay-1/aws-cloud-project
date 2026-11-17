# 01: IAM Setup

## Goal

Secure the AWS account, stop using root, and set up CLI and SSH access for the rest of the build.

## Why

Root has unrestricted access that no policy can limit, so it is reserved for the few tasks that require it. A named admin user with MFA gives auditable, revocable access. The CLI makes the build repeatable, and an SSH key pair means no passwords on the instances.

## What I did

Created the account, enabled MFA on root, created IAM admin user `projectx-prod-admin` with MFA, configured AWS CLI v2 (region `US-East-1`), and created SSH key pair (stored outside the repo).

**Key decisions:** <!-- why AdministratorAccess vs a scoped policy; note if it's a lab simplification -->
1. In production environments, workforce access is typically managed through AWS IAM Identity Center (formerly AWS Single Sign-On), which provides centralized, federated access across multiple AWS accounts and applications using short-lived credentials. For simplicity, this project uses IAM users created directly in a single AWS account. These users are scoped to that account only and rely on long-lived access keys, which would not be appropriate at scale.
2. AdminstratorAccess policy was assigned to the `projectx-prod-admin` identity, to enable access to all services in the console as quickly as possible, rather than creating scoped policies for specific services.

## Verification

```powershell
aws sts get-caller-identity
```

Returns the admin user's ARN, not root. Also launched and terminated a test EC2 instance to confirm the account, CLI and key work.

![IAM User Screenshot](./screenshots/01-iam.png)
<!-- One screenshot: sts output with account ID redacted -->



## Production hardening

- Use SSO or short-lived credentials instead of long-lived access keys
- Scope the admin policy down to what's actually needed <!-- anything else -->
