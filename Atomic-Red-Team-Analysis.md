# Atomic Red Team (ART) Testing on Target Machine

This section demonstrates the use of **Atomic Red Team (ART)** to simulate adversary techniques on a Windows endpoint and validate detection coverage in Splunk. These tests help identify gaps in logging and monitoring from a blue-team perspective.

---

## Prerequisites

- Windows 10 target machine
- PowerShell running as **Administrator**
- Splunk Universal Forwarder installed and forwarding logs
- Make sure you are running both Splunk Server and Active Directory.

---

## Prepare the System

### Set PowerShell Execution Policy

Open **PowerShell as Administrator** and run:

```powershell
Set-ExecutionPolicy Bypass CurrentUser
```
Press Y to confirm.

![policy](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-20%20121708.png)

---

## Add Windows Defender Exclusion

To prevent test execution from being blocked:
   1. Click the up arrow in the system tray
   2. Open: `Windows Security → Virus & threat protection`
   3. Select: `Manage settings → Add or remove exclusions`
   4. Click: `Add an exclusion → Folder`
   5. Select:`This PC → Local Disk (C:)`

![exclusion](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-20%20121951.png)

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

![art](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-20%20125039.png)

---

## Atomic Red Team Directory Structure

- After installation, Atomic Red Team is located at:`C:\AtomicRedTeam\Atomics`
- Set Path to Atomics
```powershell
$AtomicRedTeamPath = "C:\AtomicRedTeam\atomics"
```
---

## MITRE ATT&CK Reference

MITRE ATT&CK techniques can be referenced at:
`https://attack.mitre.org/`
The technique ID (e.g., T1136.001) is used when running Atomic tests.

![mitre](https://github.com/Niharika80/Active-Directory-Project-/blob/3ef0802005d727601a19ae88356161625da8a986/images/Screenshot%202025-12-28%20140915.png)

---

## Execute Atomic Red Team Test
### Create Local Account (Persistence)

Run the following test:
```powershell
Invoke-AtomicTest T1136.001 -Path $AtomicRedTeamPath
```
- Technique: T1136.001 – Create Local Account
- Tactic: Persistence

![attack](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-28%20103448.png)

---

### Windows Security Event Logs generated:
EventCode 4720 → New account created

Splunk search example:
`index="endpoint" EventCode=4720`

![splunk](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-28%20103806.png)

- Multiple accounts show different execution paths (PowerShell, CMD, Admin)
- Event logs demonstrate persistence activity and detection capability
  
## Account created – Admin labeled simulation 

![splunk](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-28%20104608.png)

## Account created – PowerShell-labeled simulation

![splunk](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-28%20104545.png)

## Account created – CMD-labeled simulation
![splunk](https://github.com/Niharika80/Active-Directory-Project-/blob/75014fc325e10db04346c350cb609ad1f6cd0942/images/Screenshot%202025-12-28%20104527.png)

---

## Summary
Atomic Red Team was used to safely simulate real-world attacker techniques on a Windows endpoint. By executing specific MITRE ATT&CK techniques, I validated whether the activity was properly logged and detected in Splunk. This testing helped identify gaps in visibility, such as actions that occurred on the system but did not generate meaningful alerts. 
