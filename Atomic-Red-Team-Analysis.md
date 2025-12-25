# Atomic Red Team (ART) Testing on Target Machine

This section demonstrates the use of **Atomic Red Team (ART)** to simulate adversary techniques on a Windows endpoint and validate detection coverage in Splunk. These tests help identify gaps in logging and monitoring from a blue-team perspective.

---

## Prerequisites

- Windows 10 target machine
- PowerShell running as **Administrator**
- Splunk Universal Forwarder installed and forwarding logs
- Internet access enabled on the target machine

---

## Prepare the System

### Set PowerShell Execution Policy

Open **PowerShell as Administrator** and run:

```powershell
Set-ExecutionPolicy Bypass -Scope CurrentUser
```
Press Y to confirm.

---

## Add Windows Defender Exclusion

To prevent test execution from being blocked:
   1. Click the up arrow in the system tray
   2. Open: `Windows Security → Virus & threat protection`
   3. Select: `Manage settings → Add or remove exclusions`
   4. Click: `Add an exclusion → Folder`
   5. Select: `This PC → Local Disk (C:)`

---

## Install Atomic Red Team

Return to PowerShell and run:
```powershell
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
```
Then install Atomic Red Team atomics:
```powershell
Install-AtomicRedTeam -getAtomics
```
Press Y when prompted.

---

## Atomic Red Team Directory Structure

After installation, Atomic Red Team is located at:
`C:\AtomicRedTeam\Atomics`
Each folder represents a MITRE ATT&CK technique.

---

## MITRE ATT&CK Reference

MITRE ATT&CK techniques can be referenced at:
`https://attack.mitre.org/`
The technique ID (e.g., T1136.001) is used when running Atomic tests.

---

## Execute Atomic Red Team Test
### Create Local Account (Persistence)

Run the following test:
`Invoke-AtomicTest T1136.001`

- Technique: T1136.001 – Create Local Account
- Tactic: Persistence

