# Pulumi RDS QA – Errors, Causes, and Resolution

---

## 1. Missing Pulumi Project File

### Error

error: could not detect current project: no Pulumi.yaml project file found

### Cause

Pulumi requires a `Pulumi.yaml` file to identify a project.
Without it, no stack or preview can be executed.

### Resolution

Ensure `Pulumi.yaml` exists at the project root and defines:

- Project name
- Runtime (python)

Status: **Resolved**

---

## 2. Missing Python Pulumi SDK

### Error

ModuleNotFoundError: No module named ‘pulumi’

### Cause

The Pulumi Python SDK was not installed in the active environment.

### Resolution

Activate the virtual environment and install dependencies:

pip install pulumi pulumi-aws

Status: **Resolved**

---

## 3. Missing Required Configuration Keys

### Error

Missing required configuration variable ‘rds-deployment:aws_region’

(similar errors for `environment`, `db_instance_class`,
`vpc_security_group_id`, `db_password`)

### Cause

The Pulumi program enforces required configuration keys at runtime.
If any are missing, execution fails fast.

### Resolution

Set required configuration values using:

pulumi config set  
pulumi config set –secret

Status: **Resolved (validated behavior)**

---

## 4. Secret Handling Validation

### Error

Triggered when attempting to store sensitive values without `--secret`.

### Cause

Pulumi enforces encrypted storage for secrets.
Unmarked secrets are rejected by design.

### Resolution

Always store sensitive values using:

pulumi config set rds-deployment:db_password –secret

Status: **Resolved (validated behavior)**

---

## 5. Missing AWS Credentials

### Error

No valid credential sources found.

### Cause

Pulumi requires programmatic AWS credentials.
Console-only AWS access is insufficient.

### Resolution

One of the following is required:

- AWS SSO CLI authentication
- Assume-role access
- QA-scoped access keys provided by DevSecOps

Status: **Blocked (external dependency)**

---

## 6. IAM Permission Errors

### Error

AccessDenied: iam:ListUsers

### Cause

The current AWS identity does not have IAM permissions.
This is expected for restricted QA users.

### Resolution

IAM permissions are managed externally by DevSecOps.
No changes are required in the Pulumi code.

Status: **Blocked (external dependency)**

---

## Summary

### Validated

- Pulumi project structure
- Configuration enforcement
- Secret handling
- Failure behavior without AWS access
- Provider authentication error clarity

### Blocked

- End-to-end RDS provisioning
- AWS-side resource validation
- Cleanup and destroy validation

All blocked items require **QA programmatic AWS access** and explicit approval.

No AWS resources have been created or modified.
