# Enterprise IT Infrastructure — Setup Documentation

This is a short walkthrough of my Windows Server home lab, from setting up the virtual machines to joining a Windows 11 client to the domain and testing Group Policy. I've kept the main steps, settings, and results here rather than including every installation wizard screen.

[← Back to project overview](../README.md)

## Table of Contents

- [1. Overview](#1-overview)
- [2. Objectives](#2-objectives)
- [3. Technologies Used](#3-technologies-used)
- [4. Prerequisites](#4-prerequisites)
- [5. Required Software](#5-required-software)
- [6. Network Architecture](#6-network-architecture)
- [7. Setup Steps](#7-setup-steps)
- [8. Active Directory Structure](#8-active-directory-structure)
- [9. File Sharing & Permissions (Planned)](#9-file-sharing--permissions-planned)
- [10. What I Learned](#10-what-i-learned)
- [11. Next Steps](#11-next-steps)

## 1. Overview

I built this lab to practice common IT infrastructure tasks using VMware, Windows Server 2025, and Active Directory. The environment currently has a domain controller (DC01) and one Windows 11 client (PC01). Both are connected to the same virtual NAT network.

## 2. Objectives

The main goals were to set up a domain controller, configure DNS, join a Windows client to the domain, manage users and computers in Active Directory, and test Group Policy.

DHCP server configuration, a second Windows client, and file sharing are planned for later.

## 3. Technologies Used

- VMware Workstation Pro
- Windows Server 2025 Evaluation
- Windows 11 Enterprise Evaluation
- Active Directory Domain Services (AD DS)
- DNS and VMware NAT networking
- Active Directory Users and Computers (ADUC)
- Group Policy Management

## 4. Prerequisites

- A computer capable of running multiple VMs (16 GB RAM minimum; 32 GB recommended)
- Enough free storage for the virtual disks and Windows updates
- Administrator access to the host computer

## 5. Required Software

- VMware Workstation Pro
- Windows Server 2025 Evaluation ISO
- Windows 11 Enterprise Evaluation ISO

## 6. Network Architecture

```text
               enterpriseit.local
                       |
           DC01 — Windows Server 2025
           Domain Controller + DNS
                 192.168.50.10
                       |
               VMware NAT Network
                192.168.50.0/24
                       |
              PC01 — Windows 11
              Domain-joined client
              IP assigned by DHCP

          PC02 — Planned, not deployed
```

| Setting | Value |
|---|---|
| Domain | `enterpriseit.local` |
| DC01 IP / DNS | `192.168.50.10` |
| Subnet | `255.255.255.0` |
| NAT gateway | `192.168.50.2` |
| PC01 IP during setup | `192.168.50.129` (DHCP; may change) |
| PC01 preferred DNS | `192.168.50.10` |

## 7. Setup Steps

### 7.1 VMware Installation & VM Setup

I installed VMware Workstation Pro and created two virtual machines:

| VM | Operating System | RAM | Virtual disk |
|---|---|---|---|
| DC01 | Windows Server 2025 | 4 GB | 60 GB |
| PC01 | Windows 11 Enterprise Evaluation | 4 GB | 80 GB |

Both VMs use VMware NAT networking. PC01 was configured with 2 CPU cores. PC02 will be added later.

![VMware virtual machine hardware settings](https://github.com/user-attachments/assets/4d01a150-9187-4b89-9d2b-3e65e438e631)

### 7.2 Windows Server Installation

I installed Windows Server 2025 on DC01 using an ISO in VMware. After installation, I renamed the server to `DC01` and prepared its network settings.

![Selecting the Windows Server edition during installation](https://github.com/user-attachments/assets/838dc4ad-2a68-4208-b50d-78ea68c8b3fd)

### 7.3 Network Configuration

I assigned DC01 a static IP address so that clients could consistently reach the domain controller and DNS server.

```text
Server:          DC01
IP address:      192.168.50.10
Subnet mask:     255.255.255.0
Default gateway: 192.168.50.2
Preferred DNS:   192.168.50.10
```

![DC01 network configuration](https://github.com/user-attachments/assets/a77a5e5f-0cfb-4592-9c1c-ca6aa3ce267b)

### 7.4 Active Directory Installation

Using **Server Manager → Add Roles and Features**, I installed **Active Directory Domain Services (AD DS)** on DC01. This installed the server role needed before promoting the machine to a domain controller.

![Selecting the Active Directory Domain Services role](https://github.com/user-attachments/assets/2f7048db-c210-4801-b7b2-1455a53908c9)

### 7.5 Domain Promotion

After installing AD DS, I promoted DC01 to a domain controller and created a new forest named `enterpriseit.local`. I kept the default installation paths and completed the prerequisite checks.

After the restart, I verified that Active Directory Users and Computers and DNS were available.

![Creating the enterpriseit.local forest](https://github.com/user-attachments/assets/75c54e14-1436-4dd4-a947-5ff2bf0b6a3f)

![Verifying Active Directory after domain promotion](https://github.com/user-attachments/assets/3def871a-cf67-4175-a75b-44b82fa83d2f)

### 7.6 Windows Client Setup

I installed Windows 11 Enterprise Evaluation on PC01 and created a local setup account named `LabAdmin`. PC01 received its IP address through DHCP, but I changed its preferred DNS server to `192.168.50.10` so it could find the domain controller.

I checked connectivity and DNS resolution with:

```cmd
ping 192.168.50.10
nslookup enterpriseit.local
```

Both checks succeeded. I then renamed the computer `PC01`, joined it to `enterpriseit.local`, and confirmed that its computer account appeared in Active Directory.

![PC01 successfully joined to the domain](https://github.com/user-attachments/assets/37d66f80-b5e6-4963-901d-0c484086896f)

I also created a test domain account, `john.doe`, and signed into PC01 with it. Running `whoami` returned `enterpriseit\john.doe`, confirming the domain login.

![Domain user authentication on PC01](https://github.com/user-attachments/assets/d66b442e-a901-493a-b5f0-82807b6fe979)

### 7.7 Group Policy Configuration

I created two GPOs to test the difference between computer and user settings.

**GPO 1 — Workstations - Auto Lock 10 min**

- **Linked to:** `Workstations` OU
- **Type:** Computer Configuration
- **Setting:** `Interactive logon: Machine inactivity limit`
- **Value:** `600` seconds

I ran `gpupdate /force` on PC01 and checked the applied computer policies using `gpresult /scope computer /r`. I also confirmed the registry value `InactivityTimeoutSecs = 0x258` (600 seconds). **The setting was applied, but I haven't tested the full 10-minute idle lock yet.**

![Auto-lock GPO setting](https://github.com/user-attachments/assets/53e0d701-634a-4c18-a273-e6979a1a9cf7)

**GPO 2 — Employees - Restrict Control Panel**

- **Linked to:** `Employees` OU
- **Type:** User Configuration
- **Setting:** `Prohibit access to Control Panel and PC settings`
- **Value:** Enabled

I updated the user policy with `gpupdate /target:user /force`, signed in as `ENTERPRISEIT\john.doe`, and tried opening Control Panel. Windows displayed a restriction message. I confirmed the GPO was applied using `gpresult /scope user /r`.

![Control Panel restriction when signed in as john.doe](https://github.com/user-attachments/assets/0d7cc03c-a738-42a7-a616-10d93bbca47e)

## 8. Active Directory Structure

I created OUs to organize the objects in the domain:

```text
enterpriseit.local
├── IT
├── Employees
│   └── john.doe
└── Workstations
    └── PC01
```

PC01 was moved from the default `Computers` container into `Workstations`. The test user `john.doe` was added to `Employees`. These OUs also let me link the computer and user GPOs to different targets.

![Active Directory user and OU configuration](https://github.com/user-attachments/assets/7ba58ce7-11a5-4435-97fb-1c97d1a60811)

## 9. File Sharing & Permissions (Planned)

I haven't configured file sharing yet. The plan is to create departmental folders, assign access through Active Directory security groups, and test both share and NTFS permissions using domain accounts.

## 10. What I Learned

This lab gave me hands-on practice with setting up a domain controller, configuring DNS for Active Directory, joining a Windows client to a domain, managing OUs and user accounts, and applying computer and user GPOs. I also practiced checking results with `ping`, `nslookup`, `whoami`, `gpupdate`, and `gpresult`.

## 11. Next Steps

- Add a second client (PC02) and join it to the domain.
- Create departmental users and security groups.
- Configure shared folders and NTFS permissions.
- Configure and test DHCP on Windows Server.
- Test the actual 10-minute inactivity lock.
