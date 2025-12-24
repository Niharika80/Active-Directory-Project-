# Active Directory Project 
This project demonstrates the deployment of a Windows Active Directory environment, centralized log collection using Splunk SIEM, and detection of a simulated RDP brute-force attack from Kali Linux.

## What is Active Directory?

Active Directory (AD) is a directory service used in Windows network environments. It acts as a centralized database that stores and manages information about users, computers, groups, and other network objects. The primary services provided by Active Directory are **authentication** (verifying identity) and **authorization** (granting access to resources).

Active Directory is organized in a **hierarchical, tree-like structure**, which allows administrators to manage large and complex networks efficiently. This structure helps apply policies, control access, and organize resources across different levels of the domain environment.

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

![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

---

## 1. VirtualBox Network Configuration

## Splunk Server Configuration (Ubuntu)

### Set Static IP Address

Edit the netplan configuration:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

Apply changes:

```bash
sudo netplan apply
```

---

## 2. Splunk Universal Forwarder (Windows 10 & AD Server)

Rename Machines
 - Windows Server → `ADDS-01`
 - Windows 10 → `target-PC`

Install Splunk UF
 - Receiving Host: `192.168.10.10`
 - Port: `9997`

![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

inputs.conf

![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

Run UF as Local System and restart the service.

![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

---

## 3. Splunk Server — Configure Receiver

1. Visit: http://192.168.10.10:8000

2. Settings → Forwarding & Receiving → Configure Receiving

3. Add port: 9997

4. Verify logs:
       `index = endpoint`

![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

---

## 4. Active Directory Domain (Windows Server 2022)

### Install AD DS Role
1. Log in to the **Windows Server**
2. Open **Server Manager**
3. Navigate to: `Manage → Add Roles and Features`
4. Click **Next** until you reach **Server Roles**
5. Select: `Active Directory Domain Services`
6. Click **Add Features**
7. Continue with default options
8. Click **Install**


### Promote Server to Domain Controller

After installation completes, you will see the message:

> *Configuration required. Installation succeeded on ADDC01.*

1. Click the **flag icon** at the top of Server Manager
2. Select **Promote this server to a domain controller**
3. Choose: `Add a new forest`
4. Enter domain name: `cloud.local`
5. Leave default options
6. Set a **Directory Services Restore Mode (DSRM) password**
7. Advance until prerequisite checks complete
8. Click **Install**

⚠️ The server will automatically **sign out and restart**.


### Verify Domain Controller Setup

After reboot:
- Login user should appear as: `CLOUD\Administrator`
- Open: `Server Manager → Tools → Active Directory Users and Computers`

### Create Organizational Units & Users

1. Right-click: `cloud.local`
2. Select: `New → Organizational Unit`
3. Name: `Sales`

### Create User in Sales OU

1. Right-click inside **Sales OU**
2. Select: `New → User`
3. Enter details:
   `First Name: Will`
   `Last Name: Byer`
   `Full Name: Will Byer`
   `User Logon Name: wbyer`
4. Set password
5. Click **Finish**

![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

---

## 5. Join Windows 10 to Domain

### Configure DNS Settings

1. On the **Windows 10 target machine**, open Windows Search
2. Enter: `Advanced System Properties`
3. Navigate to: `Computer Name → Change`
4. Select: `Domain`
5. Enter: `CLOUD.LOCAL`

![pfSense Firewall Lab topology](https://github.com/Niharika80/pfSense-Firewall-Lab/blob/4621b3d6e1ccd42388671008b5de2c54cb87d25a/images/Firewall%20.png)

### Set Preferred DNS Server

1. Right-click the **network icon** (bottom-right)
2. Select: `Open Network & Internet Settings`
3. Click: `Change Adapter Options`
4. Right-click the active adapter → **Properties**
5. Double-click: `Internet Protocol Version 4 (TCP/IPv4)`
6. Set: `Preferred DNS Server: 192.168.10.8 (your AD's IP)`
7. Click **OK** and close all network configuration windows.

### Verify DNS Configuration

1. Open **Command Prompt** and run:
```cmd
ipconfig /all
```
Confirm output shows:
DNS Servers . . . . . . . . . . : 192.168.10.8

### Join the Domain
1. Return to Computer Name / Domain Changes
2. Click OK
3. When prompted, enter domain administrator credentials
4. Restart the machine when prompted

### Domain Login Verification

After reboot:
  1. On the login screen, select: `Other User`
  2. Verify the domain displayed is: `CLOUD`
  3. Login using a domain user created earlier: `wbyer`

---

## 6. Kali Linux Brute Force Attack on Target Machine 

This section demonstrates an RDP brute-force attack from Kali Linux against a domain-joined Windows 10 machine using a **custom password list**, and detection of failed authentication attempts using Splunk SIEM.

### Configure Kali Linux Network

1. Power on the **Kali Linux VM** and log in
2. Right-click the **network icon** (top-right)
3. Navigate to: `Edit Connections → Wired Connection 1 → Edit`
4. 4. Go to **IPv4 Settings**
5. Set **Method** to: `Manual`
6. Click **Add** and configure:
        `Address: 192.168.10.20`
        `Netmask: 24`
        `Gateway: 192.168.10.1`
        `DNS Servers: 8.8.8.8`
7. Click **Save**
8. Disconnect and reconnect the network
9. Verify configuration:
   ```bash
   ip a
   ```
10. Test connectivity:
   ```bash
   ping google.com
   ping 192.168.10.10
   ```
11. Update the system:
   ```bash
   sudo apt-get update && sudo apt-get upgrade -y
   ```

### Prepare Brute Force Attack

1. Create Project Directory:
   ```bash
   mkdir ad-project
   cd ad-project
   ```
2. Create Custom Password List
   Create a password file manually with random test passwords:
   ```bash
   nano passwords.txt
   ```


### Enable Remote Desktop on Target Machine

On Windows 10 (Target):
  1. Search This PC
  2. Right-click → Properties
  3. Select Advanced System Settings
  4. Navigate to Remote
  5. Enable: `Allow remote connections to this computer`
  6. Click Select Users
  7. Add domain users: `wbyer`
  8. Click Apply

### Execute RDP Brute Force Attack (Hydra)

1. Review Hydra options:
   ```bash
   hydra -h
   ```
2. Run the brute-force attack:
   ```bash
   hydra -l wbyer -P passwords.txt rdp://192.168.10.100
   ```
What this means:
- -l wbyer → single username
- -P passwords.txt → password list
- rdp:// → RDP protocol
- 192.168.10.100 → Windows 10 victim

### Analyze Brute Force Activity in Splunk
Search Query
 ```spl
index="endpoint" wbyer
```

Observed Events
- Event ID: `4625` — Failed Logon
- Source IP: `192.168.10.20` (Kali Linux)
- Pattern: Multiple failures in a short time window

This behavior is indicative of brute-force attack activity.

## Summary

Setting up Active Directory helped me understand how real enterprise Windows domains are structured, including domain controllers, DNS, organizational units, and user authentication. Joining a Windows client to the domain showed how endpoint identity and authentication are centrally managed in an enterprise environment. Using Kali Linux to perform an RDP brute-force attack helped me see how attackers target domain users and how failed authentication attempts are generated at the OS level. By forwarding these logs into Splunk, I learned how SOC teams detect brute-force attacks using Windows Event IDs, source IP correlation, and event frequency analysis, even when the attack is unsuccessful. This lab strengthened my practical understanding of Active Directory security, attack behavior, and SIEM-based detection.
