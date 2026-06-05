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
- [10. What I Learned](#12-what-i-learned)
- [11. Future Improvements](#13-future-improvements)


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

                [ Domain Controller ]
              DC01 - Windows Server 2025
                        |
        -----------------------------------
        |                                 |
    PC01 - Windows 11               PC02 - Windows 11
    (Client Machine)                (Client Machine)

## 7. Setup Steps

---

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

---VMware virtual machines created


---

## 7.2 Windows Server Installation

A new virtual machine was created in VMware and Windows Server 2025 was installed using the ISO file.

During installation:

- The Windows Server ISO was selected
- **Windows Server Standard (Desktop Experience)** was chosen
- Administrator password was set
- Initial installation was completed successfully

📸 Installation Steps:

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

### Step 1 — Rename Server

The computer name was changed to:

- **DC01**

<img width="325" height="382" alt="Screenshot 2026-06-03 194218" src="https://github.com/user-attachments/assets/ab6fa7f9-d697-4f0b-9598-c1acb89714c1" />

---

### Step 2 — Configure Static IP Address

A static IP address was configured to support Active Directory services.

#### Configuration Details:

- IP Address: `192.168.50.10`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.50.2`
- Preferred DNS: `192.168.50.10`

This ensures that the server maintains a stable network identity and acts as its own DNS server, which is required for Active Directory functionality.

<img width="1119" height="631" alt="Screenshot 2026-06-03 195201" src="https://github.com/user-attachments/assets/a77a5e5f-0cfb-4592-9c1c-ca6aa3ce267b" />

---

<img width="396" height="449" alt="Screenshot 2026-06-03 212805" src="https://github.com/user-attachments/assets/65956d01-52ee-4ab4-b71e-db7b117460d1" />

This setup forms the foundation of a small enterprise IT infrastructure, allowing Active Directory, DNS, and domain services to be configured in later steps.

---
## 7.4 Active Directory Installation

The Active Directory Domain Services (AD DS) role was installed using Server Manager.

### Step 1 — Add Roles and Features Wizard

<img width="789" height="560" alt="Screenshot 2026-06-04 182845" src="https://github.com/user-attachments/assets/255a2c7f-0bed-45d5-8872-d56d60dd2466" />

Open the **Add Roles and Features Wizard** from Server Manager.

---

### Step 2 — Installation Type

<img width="787" height="554" alt="Screenshot 2026-06-04 182858" src="https://github.com/user-attachments/assets/3f16cb66-b1f7-4c04-a187-41129b407d89" />

Select **Role-based or feature-based installation**.

---

### Step 3 — Server Selection

<img width="787" height="556" alt="Screenshot 2026-06-04 182914" src="https://github.com/user-attachments/assets/4c007778-a334-4fc6-88fd-cebe7b8e0397" />

Select the target server: **DC01**.

---

### Step 4 — Server Roles

<img width="768" height="552" alt="Screenshot 2026-06-04 182935" src="https://github.com/user-attachments/assets/2f7048db-c210-4801-b7b2-1455a53908c9" />

Select **Active Directory Domain Services**.

---

### Step 5 — Features

<img width="786" height="556" alt="Screenshot 2026-06-04 182949" src="https://github.com/user-attachments/assets/f43c8854-6717-4023-a415-3415c692ec54" />

Proceed through the **Features** section without changes.

---

### Step 6 — AD DS Information

<img width="784" height="560" alt="Screenshot 2026-06-04 183839" src="https://github.com/user-attachments/assets/25cbf5e3-7ba9-41de-b141-78dc9119d40a" />

Continue through the **Active Directory Domain Services information page**.

---

### Step 7 — Confirmation

<img width="787" height="557" alt="Screenshot 2026-06-04 184015" src="https://github.com/user-attachments/assets/b0a08cb1-fe00-47cb-b043-0bbac0dd9511" />

Verify all settings and click **Install**.

---

## 7.5 Domain Promotion

After installation, a post-deployment configuration notification appeared.

---

### Step 1 — Promote This Server

<img width="421" height="351" alt="Screenshot 2026-06-04 184523" src="https://github.com/user-attachments/assets/5cfe8d36-3e5f-4886-bdd0-8f0186114edf" />


Click **Promote this server to a domain controller**.

---

### Step 2 — Deployment Configuration

<img width="759" height="562" alt="Screenshot 2026-06-04 191307" src="https://github.com/user-attachments/assets/75c54e14-1436-4dd4-a947-5ff2bf0b6a3f" />

Select **Add a new forest** and set the domain name to enterpriseit.local

---

### Step 3 — Domain Controller Options

<img width="759" height="562" alt="Screenshot 2026-06-04 191748" src="https://github.com/user-attachments/assets/4ec9c866-c7ba-4ab0-a087-ad8ecfef8b3f" />

Set the **DSRM password** and proceed.

---

### Step 4 — DNS Configuration

<img width="761" height="558" alt="Screenshot 2026-06-04 191831" src="https://github.com/user-attachments/assets/d083db4c-6fd3-4c1f-a4da-d67ac36a8b2a" />

Continue with default DNS options.

---

### Step 5 — NetBIOS Name

<img width="761" height="561" alt="Screenshot 2026-06-04 191856" src="https://github.com/user-attachments/assets/d05b196e-daf6-4866-8102-d7d1929aa49b" />


Proceed using the automatically generated NetBIOS name.

---

### Step 6 — Paths

<img width="763" height="563" alt="Screenshot 2026-06-04 191918" src="https://github.com/user-attachments/assets/5e8e90e9-79a4-43ef-8933-a35f350dd37e" />

Accept default file paths.

---

### Step 7 — Review Options

<img width="763" height="565" alt="Screenshot 2026-06-04 192240" src="https://github.com/user-attachments/assets/5e72bb5b-f912-49e9-867c-581719c4b4a9" />

Review settings and continue.

---

### Step 8 — Prerequisites Check & Installation

<img width="768" height="561" alt="Screenshot 2026-06-04 193836" src="https://github.com/user-attachments/assets/6c4147ff-0caf-4969-ba15-38bdc07452f9" />

Run prerequisite checks and click **Install**. The system will restart automatically.

---

## 7.6 Post-Installation Verification

After restart, log in and verify Active Directory services.

<img width="919" height="640" alt="Screenshot 2026-06-04 205028" src="https://github.com/user-attachments/assets/3def871a-cf67-4175-a75b-44b82fa83d2f" />

<img width="368" height="720" alt="Screenshot 2026-06-04 201704" src="https://github.com/user-attachments/assets/c21be900-75e1-4994-af7f-3fd0954ca8e9" />

Confirm that **Active Directory Users and Computers** and **DNS** are available in Server Manager.

The domain enterpriseit.local is now active and accessible.

---

## 7.7 Organizational Unit Setup

A new Organizational Unit (OU) was created to structure the domain:

- **IT**

This OU will be used for organizing users and resources in the domain environment.


## Project Status

Execution
