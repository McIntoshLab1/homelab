# Access Control

## Principle of Least Privilege
Every service and user account follows 
least privilege - only minimum access 
required to function.

## Access Levels
- Admin = full access, only for management
- Power = can manage services, no system access
- Read Only = monitoring and viewing only

## Network Access Control
Tailscale ACLs
- Admin devices = full access all services
- Mobile devices = limited service access
- Guest devices = no access

## Enterprise Equivalent
| Homelab | Enterprise |
|---------|-----------|
| Tailscale ACLs | Firewall rules / NAC |
| Service roles | RBAC (Role Based Access Control) |
| Admin accounts | Privileged Access Management |
