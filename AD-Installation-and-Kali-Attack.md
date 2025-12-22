# Active Directory Project 
This project demonstrates the deployment of a Windows Active Directory environment, centralized log collection using Splunk SIEM, and detection of a simulated RDP brute-force attack from Kali Linux.

## Network Architecture
All virtual machines are connected to the same **VirtualBox NAT Network**.

**Network:** `192.168.10.0/24`  
**Default Gateway:** `192.168.10.1`

| Machine | Role | IP Address |
|------|------|-----------|
| Ubuntu Server | Splunk Enterprise | 192.168.10.10 |
| Windows Server 2022 | Active Directory | 192.168.10.8 |
| Windows 10 | Domain Client (Target) | 192.168.10.100 |
| Kali Linux | Attacker | 192.168.10.20 |

---

## 1. VirtualBox Network Configuration

## Splunk Server Configuration (Ubuntu)

### Set Static IP Address

Edit the netplan configuration:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
Apply changes:

```bash
sudo netplan apply
```

## 2. Splunk Universal Forwarder (Windows 10 & AD Server)

Rename Machines
 - Windows Server → `ADDS-01`
 - Windows 10 → `target-PC`

