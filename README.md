# IT Infrastructure & Virtualization Project on Proxmox VE

## Project Overview

This project simulates an enterprise identity and network infrastructure environment deployed on a Proxmox VE Type-1 hypervisor. The lab implements VLAN segmentation, firewall isolation using pfSense, DHCP relay configuration, redundant domain controllers, and failover validation to demonstrate service resiliency and operational troubleshooting.

The objective of this lab was to build a functional corporate environment featuring Active Directory, Automated Monitoring, and VLAN Segmentation.

---

## Infrastructure Architecture

### Hypervisor
- Proxmox VE (Type-1 bare-metal hypervisor)
- 8GB RAM / 128GB SSD host
- Linux bridge networking
- VM resource allocation and lifecycle management

### Network Segmentation

| VLAN | Purpose | Subnet |
|------|---------|--------|
| VLAN 1  | pfSense | 10.0.0.0/24 |
| VLAN 10 | Client Systems | 10.0.10.0/24 |
| VLAN 20 | Domain Controllers | 10.0.20.0/24 |

Routing, firewall enforcement, and NAT handled by a pfSense virtual firewall.

---

## Core Components

### pfSense Firewall
- WAN interface connected to home network
- LAN interfaces for VLAN 10 and VLAN 20
- DHCP relay forwarding to Active Directory server
- NAT isolation preventing exposure to home LAN
- Firewall rules restricting unnecessary east-west traffic

### Domain Controllers
- DC1 – Windows Server 2022
- DC2 – Windows Server 2022
- Domain: `corp.local`
- Services:
  - Active Directory Domain Services
  - DNS
  - DHCP

### Client Systems
- Windows 10 VM (domain joined)
- Lubuntu VM (network validation)

---

## Identity Services Configuration

### Active Directory
- New forest created: `corp.local`
- Domain functional level: Default (Server 2022)
- Global Catalog enabled
- DNS-integrated zones

### DNS
- AD-integrated forward lookup zone
- `_msdcs.corp.local` SRV records validated
- Clients configured to use domain controller DNS

### DHCP
- Scope for VLAN 10 (Clients)
- DHCP relay configured on pfSense
- Options configured:
  - Router (gateway)
  - DNS server
  - Domain suffix

---

## Redundancy & Failover Validation

A secondary domain controller (DC2) was deployed to validate authentication continuity during primary controller outage.

### Replication Validation

Commands executed on DC2:

```powershell
repadmin /replsummary
repadmin /showrepl

