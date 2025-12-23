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
4. 4. Click **Next** until you reach **Server Roles**
5. Select: `Active Directory Domain Services`
6. 6. Click **Add Features**
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
3. Name: `IT`

### Create User in IT OU

1. Right-click inside **IT OU**
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

## 6. 
