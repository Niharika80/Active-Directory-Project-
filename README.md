# Active Directory Project

This repository documents a hands-on Active Directory lab focused on
attack simulation, detection, and hardening.

## 📄 Documentation

1. [AD Installation & Kali Attack](AD-Installation-and-Kali-Attack.md)
2. [Atomic Red Team Analysis](Atomic-Red-Team-Analysis.md)
3. [Group Policy Hardening](Group-Policy-Hardening.md)

## Network Architecture

![network](https://github.com/Niharika80/Active-Directory-Project-/blob/99e845e9c41a8335deb229e2960eed811ce01245/images/Flowchart%20(2).png)

## Features

- Active Directory setup with Windows Server 2022
- Windows 10 client domain join
- Kali Linux attack simulations (RDP brute force)
- Atomic Red Team tests mapped to MITRE ATT&CK
- Splunk logging and detection analysis
- Group Policy hardening and security recommendations

## Prerequisites

To replicate this Active Directory lab, you will need the following:

- **VirtualBox** – for running all virtual machines
- **Windows Server 2022** – to set up Active Directory Domain Services
- **Windows 10** – as a domain-joined client
- **Kali Linux** – for attack simulations
- **Ubuntu Server** – to host the Splunk server
