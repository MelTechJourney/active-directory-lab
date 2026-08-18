# 🏢 Active Directory HomeLab

## 🎯 Project Goal

This project documents the creation and administration of a small **Active Directory environment** built with Windows Server, Windows 11 and VirtualBox.

The objective is to practice tasks commonly encountered in **IT Support and Systems Administration**, while gradually introducing security concepts and troubleshooting scenarios.

The repository documents both successful configurations and incidents encountered during the lab.

---

# 🖥️ Lab Environment

| Machine    | Role                            | IPv4          |
| ---------- | ------------------------------- | ------------- |
| `DC01`     | Primary Domain Controller / DNS | `172.16.0.1`  |
| `DC02`     | Secondary Windows Server        | `172.16.0.2`  |
| `CLIENT01` | Windows 11 Pro domain client    | `172.16.0.20` |

### Virtualization

* Oracle VirtualBox
* Internal network: `AD-LAB`

### Active Directory Domain

```text
ad.meltech.local
```

---

# 🏗️ Current Architecture

```text
                    ad.meltech.local
                           │
                          DC01
                       172.16.0.1
                    AD DS + DNS + GC
                           │
              ┌────────────┴────────────┐
              │                         │
            DC02                    CLIENT01
         172.16.0.2                172.16.0.20
                                        │
                                        │
                              Domain users & GPO
```

---

# ✅ What Has Been Implemented

## Infrastructure

* [x] Windows Server virtual machines
* [x] VirtualBox internal network
* [x] Static IPv4 configuration
* [x] Connectivity testing between virtual machines
* [x] DNS configuration

## Active Directory

* [x] Active Directory Domain Services installation
* [x] New forest creation
* [x] Domain creation: `ad.meltech.local`
* [x] DC01 promotion to Domain Controller
* [x] DNS and SRV record validation
* [x] Organizational Units
* [x] User accounts
* [x] Security groups
* [x] Windows 11 Pro domain join
* [x] Domain user authentication

## Group Policy

* [x] First computer GPO
* [x] Windows Defender Firewall configuration
* [x] Microsoft Defender configuration
* [x] Account lockout policy
* [x] GPO validation with `gpupdate` and `gpresult`

## Troubleshooting & Security

* [x] DNS troubleshooting
* [x] Duplicate MAC address diagnosis
* [x] Windows edition incompatibility diagnosis
* [x] Account lockout simulation
* [x] Locked account detection with PowerShell
* [x] Windows Security Event Log analysis
* [x] Event ID `4740` investigation
* [x] Account unlock with PowerShell

---

# 🗂️ Active Directory Structure

```text
ad.meltech.local
│
├── Utilisateurs
│   ├── IT
│   ├── RH
│   └── Direction
│
├── Postes
│   ├── Clients
│   │   └── CLIENT01
│   └── Serveurs
│
└── Groupes
    ├── GG_IT
    ├── GG_RH
    └── GG_DIRECTION
```

---

# 🔐 Group Policies

Several GPOs are currently used in the lab.

### `GPO-CLIENTS-Securite-Base`

Basic security configuration applied to client computers.

### `GPO-CLIENTS-PareFeu`

Centralized Windows Defender Firewall configuration.

### `GPO-CLIENTS-Defender`

Microsoft Defender security configuration.

### `GPO-DOMAINE-Politique-Comptes`

Domain account lockout policy.

Current lab configuration:

```text
Lockout threshold          : 5 attempts
Lockout duration           : 15 minutes
Observation window         : 15 minutes
```

---

# 🧪 Example Troubleshooting Scenario

One exercise reproduced a common support incident:

```text
User cannot sign in
        ↓
Account status checked
        ↓
Account found locked
        ↓
Failed authentication attempts investigated
        ↓
Windows Event ID 4740 analyzed
        ↓
Source workstation identified
        ↓
Account unlocked
        ↓
Successful login confirmed
```

This allows the lab to go beyond configuration and include a real **diagnostic and resolution workflow**.

---

# 📚 Documentation

Detailed steps are available in the [`notes`](./notes) directory.

Topics currently documented include:

* Internal network preparation
* Windows Server installation
* Network configuration
* Active Directory deployment
* Active Directory organization
* User and group management
* Windows client domain integration
* Group Policy configuration
* Windows Defender Firewall
* Security policies

---

# 🧰 Knowledge Base

The [`kb`](./kb) directory contains incidents encountered during the project.

Instead of removing mistakes from the project history, they are documented using a support-oriented approach:

```text
Problem
   ↓
Diagnosis
   ↓
Cause
   ↓
Resolution
   ↓
Verification
```

Examples include:

* Windows Server ISO installation issue
* Windows Server Core installed instead of Desktop Experience
* Duplicate MAC address between virtual machines
* Windows 11 edition incompatible with Active Directory domain join

---

# 🛠️ Technologies & Skills

```text
Windows Server
Windows 11
Active Directory Domain Services
DNS
Group Policy
Microsoft Defender
Windows Defender Firewall
PowerShell
VirtualBox
TCP/IP
Windows Event Viewer
IT troubleshooting
Technical documentation
```

---

# 🚀 Next Steps

Planned improvements include:

* Additional security GPOs
* Second Domain Controller configuration
* Active Directory replication
* DNS redundancy
* File shares and NTFS permissions
* AGDLP permission management
* Administrative delegation
* Additional PowerShell administration
* Active Directory security hardening

---

## 📝 About this repository

This repository is part of my hands-on learning journey in **IT Support, Systems Administration and Cybersecurity**.

The goal is not only to reproduce tutorials, but to understand each configuration, test it, troubleshoot failures and document the results.
