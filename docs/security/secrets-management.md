# Secrets Management

## Overview
Public repo only contains .env.example 
with placeholder values.

## What Counts as a Secret
- Passwords
- API keys
- Auth tokens
- IP addresses
- Domain names

## Current Approach
.env file          → local only, never pushed
.env.example       → pushed, placeholders only
.gitignore         → prevents accidental commits

## Secret Rotation Policy
- Rotate all passwords every 90 days
- Rotate API keys if compromised immediately
- Document rotation in private notes

## Planned Improvements
- HashiCorp Vault for secret storage
- Automatic secret rotation
- Audit logging for secret access

## Enterprise Equivalent
| Homelab | Enterprise |
|---------|-----------|
| .env files | HashiCorp Vault / AWS Secrets Manager |
| .gitignore | Secret scanning in CI/CD |
| Manual rotation | Automated rotation policies |
