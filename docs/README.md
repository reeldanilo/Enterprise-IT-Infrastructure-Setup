# Enterprise-IT-Infrastructure-Setup

## Table of Contents

- [1. Overview](#1-overview)
- [2. Objectives](#2-objectives)
- [3. Technologies Used](#3-technologies-used)
- [4. Prerequisites](#4-prerequisites)
- [5. Required Software](#5-required-software)
- [6. Network Architecture](#6-network-architecture)

- [7. Setup Steps](#7-setup-steps)
  - 7.1 VMware Installation & Virtual Machine Setup
  - 7.2 Windows Server Installation
  - 7.3 Network Configuration (Static IP)
  - 7.4 Active Directory Installation
  - 7.5 Domain Promotion
  - 7.6 Windows Client Setup
  - 7.7 Group Policy Configuration

- [8. Active Directory Structure](#8-active-directory-structure)
- [9. File Sharing & Permissions](#9-file-sharing--permissions)
- [10. What I Learned](#10-what-i-learned)
- [11. Future Improvements](#11-future-improvements)


## 1. Overview

This project simulates a small enterprise IT environment using Windows Server, Active Directory, and virtualization technologies. The goal is to replicate real-world IT infrastructure including domain management, user control, and network configuration.

## 2. Objectives

- Deploy a virtualized enterprise environment
- Configure Active Directory
- Implement DNS and DHCP
- Join client devices to the domain
- Configure Group Policies
- Create departmental file shares
- Document deployment procedures

## 3. Technologies Used

- VMware Workstation Pro
- Windows Server 2025
- Active Directory Domain Services
- DNS
- DHCP
- Group Policy
- Windows 11
- File Services

## 4. Prerequisites

Before starting, ensure you have:

- A computer with at least 16 GB RAM (32 GB recommended)
- At least 80–100 GB free storage
- Windows 10/11 (Home or Pro)
- Administrator access

## 5. Required Software

- VMware Workstation Pro (Virtualization)
- Windows Server 2025 Evaluation ISO
- Windows 11 ISO


## 6. Network Architecture

The environment consists of one domain controller and two client machines connected on a private virtual network.

            [ enterpriseit.local Domain ]
                        |
                [ Domain Controller ]
              DC01 - Windows Server 2025
                        |
        -----------------------------------
        |                                 |
    PC01 - Windows 11               PC02 - Windows 11
    (Client Machine)                (Client Machine)

## 7. Setup Steps

## 7.1 VMware Installation & VM Setup

VMware Workstation Pro was installed to create and manage virtual machines for the lab environment.

Three virtual machines were created:

- **DC01** (Windows Server 2025) — Domain Controller  
- **PC01** (Windows 11) — Client Machine  
- **PC02** (Windows 11) — Client Machine  

Each VM was configured with:

- 4 GB RAM (Server)
- 2–4 GB RAM (Clients)
- NAT network (initial setup)
- 60 GB virtual disk (single file)

---

## 7.2 Windows Server Installation

A new virtual machine was created in VMware, and Windows Server 2025 was installed using an ISO file.

### Selecting ISO File
<img width="427" height="422" alt="Screenshot 2026-06-03 190614" src="https://github.com/user-attachments/assets/71a1d075-0633-40b2-90f3-d4eda262f903" />

---

### Selecting Windows Server Edition
<img width="424" height="421" alt="Screenshot 2026-06-03 190633" src="https://github.com/user-attachments/assets/838dc4ad-2a68-4208-b50d-78ea68c8b3fd" />

---

### Naming the Virtual Machine
<img width="425" height="434" alt="Screenshot 2026-06-03 190654" src="https://github.com/user-attachments/assets/4056b308-5c73-4a83-b031-760961aabe9d" />

---

### Disk Configuration (60 GB Single File)
<img width="426" height="427" alt="Screenshot 2026-06-03 190914" src="https://github.com/user-attachments/assets/09698bf3-d5ca-4dcc-b408-b8ffc830eda2" />

---

### VM Hardware Configuration
<img width="427" height="428" alt="Screenshot 2026-06-03 190950" src="https://github.com/user-attachments/assets/ca30f751-533c-4831-ac13-25277e048abd" />

---

### Final VM Settings (RAM, CPU, Network)
<img width="753" height="707" alt="Screenshot 2026-06-03 191155" src="https://github.com/user-attachments/assets/4d01a150-9187-4b89-9d2b-3e65e438e631" />

---

## 7.3 Network Configuration

After installation, network configuration was completed inside Windows Server.

### Rename Server

The server was renamed to:

- **DC01**

<img width="325" height="382" alt="Screenshot 2026-06-03 194218" src="https://github.com/user-attachments/assets/ab6fa7f9-d697-4f0b-9598-c1acb89714c1" />

---

### Configure Static IP Address

A static IP address was configured to support Active Directory services.

#### Configuration Details:

- IP Address: `192.168.50.10`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.50.2`
- Preferred DNS: `192.168.50.10`

This configuration ensures DC01 maintains a stable identity required for Active Directory and DNS services.

<img width="1119" height="631" alt="Screenshot 2026-06-03 195201" src="https://github.com/user-attachments/assets/a77a5e5f-0cfb-4592-9c1c-ca6aa3ce267b" />

---

<img width="396" height="449" alt="Screenshot 2026-06-03 212805" src="https://github.com/user-attachments/assets/65956d01-52ee-4ab4-b71e-db7b117460d1" />

---

## 7.4 Active Directory Installation

This section covers the installation and configuration of Active Directory Domain Services (AD DS) on DC01.
This step enabled DC01 to function as a domain controller and provide authentication services within the network.

### Add Roles and Features Wizard

<img width="789" height="560" alt="Screenshot 2026-06-04 182845" src="https://github.com/user-attachments/assets/255a2c7f-0bed-45d5-8872-d56d60dd2466" />

Open the **Add Roles and Features Wizard** from Server Manager.

---

### Installation Type

<img width="787" height="554" alt="Screenshot 2026-06-04 182858" src="https://github.com/user-attachments/assets/3f16cb66-b1f7-4c04-a187-41129b407d89" />

Select **Role-based or feature-based installation**.

---

### Server Selection

<img width="787" height="556" alt="Screenshot 2026-06-04 182914" src="https://github.com/user-attachments/assets/4c007778-a334-4fc6-88fd-cebe7b8e0397" />

Select the target server: **DC01**.

---

### Server Roles

<img width="768" height="552" alt="Screenshot 2026-06-04 182935" src="https://github.com/user-attachments/assets/2f7048db-c210-4801-b7b2-1455a53908c9" />

Select **Active Directory Domain Services**.

---

### Features

<img width="786" height="556" alt="Screenshot 2026-06-04 182949" src="https://github.com/user-attachments/assets/f43c8854-6717-4023-a415-3415c692ec54" />

Proceed through the **Features** section without changes.

---

### AD DS Information

<img width="784" height="560" alt="Screenshot 2026-06-04 183839" src="https://github.com/user-attachments/assets/25cbf5e3-7ba9-41de-b141-78dc9119d40a" />

Continue through the AD DS information page.

---

### Confirmation

<img width="787" height="557" alt="Screenshot 2026-06-04 184015" src="https://github.com/user-attachments/assets/b0a08cb1-fe00-47cb-b043-0bbac0dd9511" />

Verify the configuration and click **Install**.

---

## 7.5 Domain Promotion

After installation, the server was promoted to a domain controller.

### Promote This Server

<img width="421" height="351" alt="Screenshot 2026-06-04 184523" src="https://github.com/user-attachments/assets/5cfe8d36-3e5f-4886-bdd0-8f0186114edf" />

Click **Promote this server to a domain controller**.

---

### Deployment Configuration

<img width="759" height="562" alt="Screenshot 2026-06-04 191307" src="https://github.com/user-attachments/assets/75c54e14-1436-4dd4-a947-5ff2bf0b6a3f" />

Select **Add a new forest** and set the domain name to:

- **enterpriseit.local**

---

### Domain Controller Options

<img width="759" height="562" alt="Screenshot 2026-06-04 191748" src="https://github.com/user-attachments/assets/4ec9c866-c7ba-4ab0-a087-ad8ecfef8b3f" />

Set the **DSRM password** and continue.

---

### DNS Configuration

<img width="761" height="558" alt="Screenshot 2026-06-04 191831" src="https://github.com/user-attachments/assets/d083db4c-6fd3-4c1f-a4da-d67ac36a8b2a" />

Continue with default DNS options.

---

### NetBIOS Name

<img width="761" height="561" alt="Screenshot 2026-06-04 191856" src="https://github.com/user-attachments/assets/d05b196e-daf6-4866-8102-d7d1929aa49b" />

Proceed with the automatically generated NetBIOS name.

---

### Paths

<img width="763" height="563" alt="Screenshot 2026-06-04 191918" src="https://github.com/user-attachments/assets/5e8e90e9-79a4-43ef-8933-a35f350dd37e" />

Accept default file paths.

---

### Review Options

<img width="763" height="565" alt="Screenshot 2026-06-04 192240" src="https://github.com/user-attachments/assets/5e72bb5b-f912-49e9-867c-581719c4b4a9" />

Review the configuration and continue.

---

### Prerequisites Check & Installation

<img width="768" height="561" alt="Screenshot 2026-06-04 193836" src="https://github.com/user-attachments/assets/6c4147ff-0caf-4969-ba15-38bdc07452f9" />

Run prerequisite checks and click **Install**. The system restarts automatically.

---

## Post-Installation Verification

After restart, Active Directory services were verified successfully:

Active Directory Users and Computers and DNS were confirmed in Server Manager.

The domain **enterpriseit.local** is now active and fully operational.

<img width="919" height="640" alt="Screenshot 2026-06-04 205028" src="https://github.com/user-attachments/assets/3def871a-cf67-4175-a75b-44b82fa83d2f" />

---

<img width="368" height="720" alt="Screenshot 2026-06-04 201704" src="https://github.com/user-attachments/assets/18bda716-326c-4b43-8b47-ef2d08ff78dd" />

## 7.6 Windows Client Setup

This section covers the installation and configuration of a Windows 11 client machine (PC01) and its integration into the `enterpriseit.local` Active Directory domain.

### Windows 11 Virtual Machine Installation

A new virtual machine was created in VMware Workstation Pro with the following configuration:

- **VM Name:** PC01 - Windows 11 Client
- **Operating System:** Windows 11 Enterprise Evaluation
- **Memory:** 4 GB RAM
- **Processors:** 2 CPU cores
- **Virtual Disk:** 80 GB
- **Network Adapter:** NAT

Windows 11 Enterprise Evaluation was installed, and a local administrator account named `LabAdmin` was created for initial configuration.

---

<img width="430" height="433" alt="Screenshot 2026-10-28 233801" src="https://github.com/user-attachments/assets/1b0c8fe0-cb5b-4564-a29a-18497234bfe1" />

---

<img width="1024" height="795" alt="Screenshot 2026-10-28 234052" src="https://github.com/user-attachments/assets/f6fb36e2-f549-4f2e-9cff-80f35f18a0d8" />

---

<img width="1019" height="775" alt="Screenshot 2026-10-29 002354" src="https://github.com/user-attachments/assets/d9fc425e-130a-41dd-8653-fdb8240bf0c2" />

---

### Network Configuration

After installation, the network settings were configured to allow PC01 to communicate with the domain controller (DC01).

**Network Configuration:**

| Setting | Value |
|---|---|
| IP Address | 192.168.50.129 (DHCP) |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.50.2 |
| Preferred DNS | 192.168.50.10 |
| Domain Controller | DC01 |

The DNS server was manually configured to point to DC01 (`192.168.50.10`) while the IP address remained dynamically assigned through DHCP.

<img width="400" height="197" alt="Screenshot 2026-10-29 003122" src="https://github.com/user-attachments/assets/de48c819-54ac-4249-be8f-c10a06be9aa3" />

---

<img width="479" height="389" alt="Screenshot 2026-10-29 003144" src="https://github.com/user-attachments/assets/2ea5935d-6958-404e-b988-433297b04842" />

---

<img width="359" height="456" alt="Screenshot 2026-10-29 003211" src="https://github.com/user-attachments/assets/ebec76e3-8566-48fe-bf58-81fb4db40c05" />

---
<img width="390" height="447" alt="Screenshot 2026-10-29 003345" src="https://github.com/user-attachments/assets/2e823426-42ee-42d2-b237-0386e3a81611" />

---

This allows the client to locate the Active Directory domain through DNS.

<img width="785" height="601" alt="Screenshot 2026-10-29 003454" src="https://github.com/user-attachments/assets/f515669b-2c93-400c-97d6-a0e1e25abcbb" />


### Connectivity and DNS Verification

Network connectivity between PC01 and DC01 was tested using the following commands:

- `ping 192.168.50.10` — Verified communication with the domain controller.
<img width="529" height="276" alt="Screenshot 2026-10-29 004343" src="https://github.com/user-attachments/assets/dc0fce3f-491a-43bc-a21b-788bdfc0b3e4" />

---
- `nslookup enterpriseit.local` — Verified DNS resolution of the Active Directory domain.
<img width="429" height="182" alt="Screenshot 2026-10-29 004504" src="https://github.com/user-attachments/assets/07791f79-57e6-4c80-9ad3-4b45db0368f5" />


Both tests completed successfully, confirming network connectivity and domain name resolution.

### Joining the Active Directory Domain

The Windows 11 computer was renamed to `PC01` and joined to the `enterpriseit.local` domain.

Steps performed:

1. Opened System Properties (`sysdm.cpl`).
2. Renamed the computer to `PC01`.
3. Selected the option to join a domain.
4. Entered `enterpriseit.local`.
5. Authenticated using an authorized domain account.
6. Restarted the computer to complete the process.

<img width="315" height="388" alt="Screenshot 2026-10-29 004814" src="https://github.com/user-attachments/assets/3b2b1ab6-c487-45cb-8e0b-34aa10371975" />

---

<img width="322" height="384" alt="Screenshot 2026-10-29 005610" src="https://github.com/user-attachments/assets/212209af-55e9-44a0-b70a-3c48beb8eb21" />

---

<img width="309" height="145" alt="Screenshot 2026-10-29 005928" src="https://github.com/user-attachments/assets/37d66f80-b5e6-4963-901d-0c484086896f" />

---

After restarting, PC01 was successfully registered in Active Directory.

The computer account was verified in Active Directory Users and Computers on DC01.

<img width="482" height="268" alt="Screenshot 2026-10-29 010339" src="https://github.com/user-attachments/assets/2fb5b4d9-4c03-43af-bc98-b89a0a062774" />

---

### Domain User Authentication

A test domain account, `john.doe`, was created in Active Directory.

The account was used to sign in to PC01, confirming successful domain authentication.

<img width="559" height="376" alt="Screenshot 2026-10-29 011042" src="https://github.com/user-attachments/assets/7ba58ce7-11a5-4435-97fb-1c97d1a60811" />

---

<img width="436" height="375" alt="Screenshot 2026-10-29 011128" src="https://github.com/user-attachments/assets/1c5c00d6-cc66-460f-96c7-501448d50265" />

---

<img width="551" height="232" alt="Screenshot 2026-10-29 011700" src="https://github.com/user-attachments/assets/d66b442e-a901-493a-b5f0-82807b6fe979" />


---

<img width="551" height="232" alt="Screenshot 2026-10-29 011700" src="https://github.com/user-attachments/assets/23bf49eb-d683-4342-a5af-4c022bee8d61" />

---

## 7.7 Group Policy Configuration

Group Policy Objects (GPOs) were configured to centrally manage computer security settings and user restrictions through Active Directory.

Two policies were implemented to demonstrate the differences between Computer Configuration and User Configuration.

### GPO 1 — Automatic Workstation Lock

**GPO Name:** `Workstations - Auto Lock 10 min`  
**Linked OU:** Workstations  
**Configuration Type:** Computer Configuration

<img width="761" height="522" alt="Screenshot 2026-10-29 134118" src="https://github.com/user-attachments/assets/6bcce853-f8a4-4852-930d-322b7948af53" />


`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options`

**Policy:** Interactive logon: Machine inactivity limit  
**Value:** 600 seconds (10 minutes)

<img width="874" height="558" alt="Screenshot 2026-10-29 143957" src="https://github.com/user-attachments/assets/53e0d701-634a-4c18-a273-e6979a1a9cf7" />

---

The configured inactivity limit was also confirmed in the Windows Registry:

`InactivityTimeoutSecs = 0x258 (600 seconds)`

<img width="1007" height="109" alt="Screenshot 2026-10-29 172751" src="https://github.com/user-attachments/assets/4b25e407-24ed-4bdb-9966-1c8fa923aea6" />


### GPO 2 — Restrict Control Panel Access

**GPO Name:** `Employees - Restrict Control Panel`  
**Linked OU:** Employees  
**Configuration Type:** User Configuration

The policy was configured using the following path:

`User Configuration → Policies → Administrative Templates → Control Panel`

**Policy:** Prohibit access to Control Panel and PC settings  
**Configuration:** Enabled

<img width="1095" height="475" alt="Screenshot 2026-10-29 194436" src="https://github.com/user-attachments/assets/e95bba4f-679b-4a6f-9ceb-4e309efa1600" />

---

<img width="684" height="633" alt="Screenshot 2026-10-29 194508" src="https://github.com/user-attachments/assets/733b77cf-61e7-4589-aa86-e5080b4f3f53" />

---

After signing back into PC01 with the domain account `ENTERPRISEIT\john.doe`, access to Control Panel was tested.

Windows displayed a restriction message preventing access to Control Panel.

<img width="400" height="199" alt="Screenshot 2026-10-29 194803" src="https://github.com/user-attachments/assets/0d7cc03c-a738-42a7-a616-10d93bbca47e" />

---

<img width="1020" height="541" alt="Screenshot 2026-10-29 194824" src="https://github.com/user-attachments/assets/07d3d4be-640b-4b42-88fe-920d36ab5a1e" />


## 8. Active Directory Structure

To organize domain resources and prepare for Group Policy management, Organizational Units (OUs) were created in Active Directory.

### Organizational Units

The following OUs were configured:

- **IT:** Created for organizing IT-related users and resources.
- **Workstations:** Contains domain-joined client computers, including PC01.
- **Employees:** Contains test employee accounts.

The PC01 computer object was moved from the default Computers container into the Workstations OU.

A test user account (`john.doe`) was created in the Employees OU.

### Purpose

This structure provides a foundation for centralized management of users and computers.

It will also allow Group Policies to be applied to specific organizational units in future configurations.

**Screenshot:** Active Directory Users and Computers showing the OUs, PC01, and the test employee account.


