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



## Project Status

Execution
