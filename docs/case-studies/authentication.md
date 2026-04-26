# Case Study: Multi-Layer Authentication

## Problem
Self-hosted services need protection from 
unauthorized access both externally and 
internally on the network.

## Solution
Implemented defense in depth authentication:
- Network layer via Tailscale
- Proxy layer via Nginx Proxy Manager
- Application layer via service accounts

## Implementation
1. Deployed Tailscale on all devices
2. Configured Nginx Proxy Manager access lists
3. Created individual accounts per service
4. Documented all credentials in password manager

## Result
All access requires Tailscale authentication 
before reaching any service and zero exposure the open internet.

## Skills Demonstrated
- Defense in depth
- Zero trust networking
- Least privilege access
- Security documentation

## Enterprise Relevance
mirrors enterprise zero trust architecture
used in financial services environments
