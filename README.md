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
- Lubuntu VM (domain joined)

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

Validation steps included:

- Replication health verification:
  repadmin /replsummary  
  repadmin /showrepl  
- SYSVOL replication confirmation  
- DNS SRV record verification  
- Controlled shutdown of DC1  
- Client authentication validation via DC2  
- Domain join test during DC1 outage  

Authentication and DNS services remained fully operational during primary controller failure.

Detailed validation process available in:
docs/failover-validation.md

---

## Monitoring & Operational Controls

A lightweight Bash script was implemented on the Proxmox host to:

- Monitor CPU utilization
- Monitor memory utilization
- Trigger alerts when usage exceeds defined thresholds

This simulates proactive infrastructure monitoring practices.

---

## Security & Isolation

- VLAN segmentation separating client and server networks
- Firewall rule enforcement via pfSense
- NAT configuration preventing exposure to home LAN
- Controlled domain join permissions

---

## Troubleshooting & Validation

Scenarios tested:

- DNS misconfiguration impact on domain join
- DHCP relay misconfiguration
- Domain controller outage simulation
- Replication health verification
- Logon server validation

---

## Skills Demonstrated

- Type-1 hypervisor deployment and management
- VLAN segmentation and virtual network design
- Firewall rule configuration and NAT isolation
- DHCP relay implementation
- Active Directory deployment and replication validation
- DNS dependency troubleshooting
- Authentication failover testing
- Infrastructure monitoring automation

---

## Operational Takeaway

This lab demonstrates practical understanding of identity service redundancy, network segmentation, and infrastructure validation.
