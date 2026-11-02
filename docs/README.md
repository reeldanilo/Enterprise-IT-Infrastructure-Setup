# Enterprise IT Infrastructure — Setup Documentation

This document covers how I built and tested my Windows Server home lab. It includes the main configuration steps, settings, and commands used to check the results.

[Back to project overview](../README.md)

## Contents

1. [Lab Environment](#1-lab-environment)
2. [VMware and Virtual Machines](#2-vmware-and-virtual-machines)
3. [Windows Server and Network Configuration](#3-windows-server-and-network-configuration)
4. [Active Directory and DNS](#4-active-directory-and-dns)
5. [Windows 11 Clients and Domain Join](#5-windows-11-clients-and-domain-join)
6. [Organizational Units, Users, and Groups](#6-organizational-units-users-and-groups)
7. [Group Policy](#7-group-policy)
8. [File Sharing and Permissions](#8-file-sharing-and-permissions)
9. [DHCP Server](#9-dhcp-server)
10. [Final Verification](#10-final-verification)
11. [What I Learned](#11-what-i-learned)

## 1. Lab Environment

I built the environment in VMware Workstation Pro using Windows Server 2025 Evaluation and two Windows 11 Enterprise Evaluation clients. The goal was to practice common domain administration tasks in a small business-style network.

### Software and services

- VMware Workstation Pro
- Windows Server 2025 Evaluation
- Windows 11 Enterprise Evaluation
- Active Directory Domain Services (AD DS)
- DNS Server and DHCP Server
- Group Policy Management
- SMB file sharing and NTFS permissions

### Machines

| Machine | Operating system | Purpose | IP address during testing |
| --- | --- | --- | --- |
| DC01 | Windows Server 2025 Evaluation | Domain controller, DNS, DHCP, file shares | `192.168.50.10` (static) |
| PC01 | Windows 11 Enterprise Evaluation | Domain-joined client | `192.168.50.100` (DHCP) |
| PC02 | Windows 11 Enterprise Evaluation | Domain-joined client | `192.168.50.101` (DHCP) |

The client IP addresses are DHCP leases observed during testing, not static assignments.

### Network settings

| Setting | Value |
| --- | --- |
| Active Directory domain | `enterpriseit.local` |
| VMware network | VMnet8 (NAT) |
| Subnet | `192.168.50.0/24` |
| Subnet mask | `255.255.255.0` |
| NAT gateway | `192.168.50.2` |
| Domain DNS server | `192.168.50.10` |
| Windows Server DHCP range | `192.168.50.100–192.168.50.200` |

```text
                      enterpriseit.local
                              |
                    DC01 — Windows Server
                    AD DS / DNS / DHCP
                       192.168.50.10
                              |
                  VMware VMnet8 — NAT
                      192.168.50.0/24
                              |
                   +----------+----------+
                   |                     |
             PC01 — Windows 11     PC02 — Windows 11
              192.168.50.100       192.168.50.101
               DHCP client          DHCP client
```

## 2. VMware and Virtual Machines

1. Installed VMware Workstation Pro on the host computer.
2. Created **DC01** with the Windows Server 2025 Evaluation ISO.
3. Created **PC01** and **PC02** with the Windows 11 Enterprise Evaluation ISO.
4. Connected all three virtual machines to **VMnet8 (NAT)** so they could communicate on the same subnet.
5. Configured VMnet8 with the `192.168.50.0/24` subnet.

DC01 was allocated 4 GB of RAM. The Windows 11 clients were set up as separate virtual machines with their own computer names. VMware NAT provides the virtual gateway, the DHCP service was later moved from VMware to DC01.

## 3. Windows Server and Network Configuration

### 3.1 Install and rename the server

I installed Windows Server 2025 on the first VM, completed the initial setup, and renamed it **DC01**.

### 3.2 Configure a static IP

I set a static IPv4 address on DC01 using the Windows network adapter settings.

```text
IP address:       192.168.50.10
Subnet mask:      255.255.255.0
Default gateway:  192.168.50.2
Preferred DNS:    192.168.50.10
```

A domain controller needs a stable address so clients can consistently reach Active Directory and DNS. After configuring the network, I checked the adapter settings with:

```cmd
ipconfig /all
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/da1f89b7-d956-4b61-985f-112559852df4" alt="DC01 network configuration" width="680">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/d8a63cd3-2a19-4e73-94aa-9df9bb0e815a" alt="DC01 IPv4 settings" width="350">
</p>



## 4. Active Directory and DNS

### 4.1 Install AD DS

1. Opened **Server Manager → Add Roles and Features**.
2. Installed the **Active Directory Domain Services** role.
3. Selected **Promote this server to a domain controller**.
4. Created a new forest named `enterpriseit.local`.
5. Completed the prerequisite checks and restarted DC01 after promotion.

### 4.2 Configure and verify DNS

DNS was installed with the domain controller setup. DC01 uses its own IP address (`192.168.50.10`) for DNS, and the Windows clients use DC01 to locate domain services.

From a client, I checked connectivity and DNS resolution:

```cmd
ping 192.168.50.10
nslookup enterpriseit.local
```

I also checked that the domain was visible in **Active Directory Users and Computers** and that DNS Manager was available on DC01.

<p align="center">
  <img src="https://github.com/user-attachments/assets/1295b8c7-a1ad-4e3a-a872-ac38969e220c" alt="Active Directory and DNS configuration" width="680">
</p>

## 5. Windows 11 Clients and Domain Join

### 5.1 Prepare PC01 and PC02

1. Installed Windows 11 Enterprise Evaluation on each VM.
2. Created a local setup account (`LabAdmin`).
3. Renamed the computers **PC01** and **PC02**.
4. Confirmed they could reach DC01 over VMnet8.
5. Configured the clients to use `192.168.50.10` for DNS before joining the domain.

Initially, VMware supplied client IP addresses through its DHCP service. Later in the project, Windows Server DHCP took over that role.

### 5.2 Join the domain

On each Windows 11 client:

1. Opened the computer's system/domain settings.
2. Selected the option to join a domain.
3. Entered `enterpriseit.local`.
4. Supplied a domain account with permission to join computers.
5. Restarted the client and signed in with a domain account.

I verified a domain sign-in with:

```cmd
whoami
```

For example, signing in as John returned:

```text
enterpriseit\john.doe
```

Both computer accounts were then moved to the **Workstations** OU in Active Directory.

<p align="center">
  <img src="https://github.com/user-attachments/assets/7a2b1374-e3ee-4fad-b082-69bc63dee401" alt="Domain-joined Windows 11 workstation" width="650">
</p>


## 6. Organizational Units, Users, and Groups

### 6.1 Create the OU structure

In **Active Directory Users and Computers**, I created the parent OUs **Employees**, **Groups**, and **Workstations**. I organized Employees into three departmental OUs: **HR**, **Finance**, and **IT**.

The IT OU was originally created at the domain root. I moved it under Employees and re-enabled **Protect object from accidental deletion** afterward.

```text
enterpriseit.local
├── Employees
│   ├── HR
│   │   └── john.doe
│   ├── Finance
│   │   └── jane.doe
│   └── IT
│       └── alex.smith
├── Groups
│   ├── GRP_HR
│   ├── GRP_Finance
│   └── GRP_IT
└── Workstations
    ├── PC01
    └── PC02
```

### 6.2 Create users and security groups

I created three test users and placed them in their respective department OUs. I also created three **Global / Security** groups in the Groups OU.

| User | Department OU | Security group |
| --- | --- | --- |
| `john.doe` | HR | `GRP_HR` |
| `jane.doe` | Finance | `GRP_Finance` |
| `alex.smith` | IT | `GRP_IT` |

I added each user to the matching department group through the account's **Member Of** tab or the group's **Members** tab.

The OUs organize users and determine where Group Policies apply. The security groups control access to resources such as shared folders.

<p align="center">
  <img src="https://github.com/user-attachments/assets/9a70cd5a-f373-47ae-b1e5-28e9321cbf29" alt="Active Directory organizational units and security groups" width="650">
</p>

## 7. Group Policy

I created two Group Policy Objects (GPOs): one for workstation security and one for department-based user restrictions.

### 7.1 Workstation inactivity lock

**GPO:** `Workstations - Auto Lock 10 min`  
**Linked to:** `Workstations` OU  
**Setting:** `Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options → Interactive logon: Machine inactivity limit`  
**Value:** `600` seconds

After linking the GPO, I updated policy on the Windows clients:

```cmd
gpupdate /force
```

I checked the applied computer GPOs with an elevated Command Prompt:

```cmd
gpresult /scope computer /r
```

I also verified that the inactivity limit was stored in the registry:

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v InactivityTimeoutSecs
```

The result was `0x258`, which is 600 seconds. This confirmed that the setting was applied; the full 10-minute idle timeout was not timed separately.

### 7.2 Departmental Control Panel restriction

**GPO:** `Employees - Restrict Control Panel`  
**Linked to:** `Employees\HR` and `Employees\Finance`  
**Setting:** `User Configuration → Policies → Administrative Templates → Control Panel → Prohibit access to Control Panel and PC settings`  
**Value:** Enabled

The GPO was originally linked to the parent Employees OU. After creating the departmental OUs, I linked the existing GPO directly to **HR** and **Finance** and removed its link from **Employees**. I kept the same GPO object rather than creating duplicate policies.

To check user policy, I ran:

```cmd
gpupdate /force
gpresult /scope user /r
```

I also opened Control Panel using **Win + R** and:

```cmd
control
```

### 7.3 Results

| User | Department | Control Panel access |
| --- | --- | --- |
| `john.doe` | HR | Blocked |
| `jane.doe` | Finance | Blocked |
| `alex.smith` | IT | Allowed |

The tests confirmed that HR and Finance receive the restriction while IT does not.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b1f5d628-af65-4806-a2ff-ecbe4880cc39" alt="Group Policy configuration" width="650">
</p>

## 8. File Sharing and Permissions

### 8.1 Create the departmental folders

On DC01, I created three folders under `C:\Shared Folders`:

```text
C:\Shared Folders\
├── HR
├── Finance
└── IT
```

### 8.2 Configure NTFS permissions

For each folder, I opened **Properties → Security → Advanced**, reviewed the inherited permissions, and configured access for the appropriate Active Directory group.

- Disabled inheritance and converted the existing permissions so they could be adjusted.
- Removed broad access entries that were not needed for department users.
- Kept system and administrative access.
- Granted **Modify** permissions to the matching department security group.

| Folder | Department group | NTFS access |
| --- | --- | --- |
| `HR` | `GRP_HR` | Modify |
| `Finance` | `GRP_Finance` | Modify |
| `IT` | `GRP_IT` | Modify |

### 8.3 Create the SMB shares

I opened each folder's **Properties → Sharing → Advanced Sharing**, enabled sharing, and created these network paths:

| Share | Network path | Department group |
| --- | --- | --- |
| HR | `\\DC01\HR` | `GRP_HR` |
| Finance | `\\DC01\Finance` | `GRP_Finance` |
| IT | `\\DC01\IT` | `GRP_IT` |

At the share-permission level, the corresponding department group received **Change** and **Read**. Domain administrators retained **Full Control**, and broad `Everyone` access was removed.

### 8.4 Test access from a domain client

I signed in with different domain users and opened the shares through File Explorer. For example:

```text
\\DC01\HR
\\DC01\Finance
\\DC01\IT
```

The tests included creating files where access was allowed and attempting access with a user from another department.

| Test | Result |
| --- | --- |
| John accessing and writing to HR | Allowed |
| Alex accessing HR | Denied |
| Alex accessing and writing to IT | Allowed |
| John accessing IT | Denied |
| Jane accessing and writing to Finance | Allowed |
| John accessing Finance | Denied |

These tests confirmed that both share permissions and NTFS permissions were restricting access by department.

<!-- Screenshots to add: NTFS/share permissions and allowed/denied access tests -->

## 9. DHCP Server

### 9.1 Install the role

On DC01, I installed **DHCP Server** through **Server Manager → Add Roles and Features**.

The post-deployment authorization initially failed. I signed in using a domain administrator account with the required Active Directory privileges and manually authorized DC01 from an elevated PowerShell session:

```powershell
Add-DhcpServerInDC -DnsName "DC01.enterpriseit.local" -IPAddress 192.168.50.10
```

I verified the authorization with:

```powershell
Get-DhcpServerInDC
```

The result listed `DC01.enterpriseit.local` with IP `192.168.50.10`.

### 9.2 Create the IPv4 scope

In **Server Manager → Tools → DHCP**, I created a new IPv4 scope named **EnterpriseIT Clients**.

| Scope setting | Value |
| --- | --- |
| Name | `EnterpriseIT Clients` |
| Network | `192.168.50.0/24` |
| Starting IP | `192.168.50.100` |
| Ending IP | `192.168.50.200` |
| Subnet mask | `255.255.255.0` |
| Exclusions | None |
| Lease duration | 8 days |
| 003 Router | `192.168.50.2` |
| 006 DNS Servers | `192.168.50.10` |
| 015 DNS Domain Name | `enterpriseit.local` |

I left the scope inactive while VMware's DHCP service was still enabled so the two services would not hand out addresses on the same network.

### 9.3 Switch DHCP from VMware to DC01

1. Shut down PC01 and PC02, leaving DC01 running.
2. Opened **VMware Workstation Pro → Edit → Virtual Network Editor**.
3. Selected **VMnet8 (NAT)**.
4. Disabled **Use local DHCP service to distribute IP addresses to VMs**.
5. Kept the VMnet8 NAT network and subnet settings unchanged.
6. Returned to DHCP Manager on DC01 and activated **EnterpriseIT Clients**.

I checked the scope state with:

```powershell
Get-DhcpServerv4Scope
```

The scope was listed as **Active**.

### 9.4 Renew and verify client leases

On each Windows 11 client, I opened an elevated Command Prompt and ran:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

The verified values were:

| Setting | PC01 | PC02 |
| --- | --- | --- |
| IPv4 address | `192.168.50.100` | `192.168.50.101` |
| DHCP server | `192.168.50.10` | `192.168.50.10` |
| DNS server | `192.168.50.10` | `192.168.50.10` |
| Default gateway | `192.168.50.2` | `192.168.50.2` |
| DNS suffix | `enterpriseit.local` | `enterpriseit.local` |

Finally, I opened **DHCP Manager → IPv4 → EnterpriseIT Clients → Address Leases** and confirmed that both client leases appeared.

<p align="center">
  <img src="https://github.com/user-attachments/assets/446c0c8e-504a-4af3-87a1-d4cd8db73fb8" alt="DHCP address leases for PC01 and PC02" width="650">
</p>


## 10. Final Verification

The following checks were completed during the project:

| Component | Verification |
| --- | --- |
| AD DS | `enterpriseit.local` forest created; users and computers visible in ADUC |
| Domain join | PC01 and PC02 joined to the domain; domain account sign-in worked |
| DNS | Client connectivity and domain name resolution checked with `ping` and `nslookup` |
| Security groups | Department users placed in their matching groups |
| Workstation GPO | Applied on both workstations; 600-second registry setting confirmed |
| Department GPO | HR and Finance blocked from Control Panel; IT allowed |
| File shares | Allowed and denied access tested with different domain users |
| DHCP | DC01 authorized; scope active; both clients received leases from `192.168.50.10` |

Useful commands from the verification process:

```cmd
ipconfig /all
ping 192.168.50.10
nslookup enterpriseit.local
whoami
gpupdate /force
gpresult /scope user /r
gpresult /scope computer /r
```

```powershell
Get-DhcpServerInDC
Get-DhcpServerv4Scope
```

## 11. What I Learned

This project gave me hands-on practice installing and managing Windows Server, setting up a domain controller, and configuring DNS and DHCP. I also worked with Active Directory users, OUs, and security groups, tested computer and user Group Policies, and configured access to shared folders.

The troubleshooting was an important part of the lab. I worked through domain sign-in issues, permissions when moving OUs, DHCP authorization, and switching from VMware DHCP to Windows Server DHCP. Testing each change from a client helped me understand how the services work together.
