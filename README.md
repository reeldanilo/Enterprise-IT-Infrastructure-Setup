# Enterprise-IT-Infrastructure-Setup

## Table of Contents

- [1. Overview](#1-overview)
- [2. Objectives](#2-objectives)
- [3. Technologies Used](#3-technologies-used)
- [4. Prerequisites](#4-prerequisites)
- [5. Required Software](#5-required-software)
- [6. Network Architecture](#6-network-architecture)

- [7. Setup Steps](#7-setup-steps)
  - [7.1 VMware Installation & Virtual Machine Setup](#71-vmware-installation--virtual-machine-setup)
  - [7.2 Windows Server Installation](#72-windows-server-installation)
  - [7.3 Network Configuration (Static IP)](#73-network-configuration-static-ip)
  - [7.4 Active Directory Installation](#74-active-directory-installation)
  - [7.5 Domain Promotion](#75-domain-promotion)
  - [7.6 Windows Client Setup](#76-windows-client-setup)
  - [7.7 Group Policy Configuration](#77-group-policy-configuration)

- [8. Active Directory Structure](#8-active-directory-structure)
- [9. File Sharing & Permissions](#9-file-sharing--permissions)
- [10. Screenshots (Step-by-Step Documentation)](#10-screenshots-step-by-step-documentation)
- [11. Challenges & Solutions](#11-challenges--solutions)
- [12. What I Learned](#12-what-i-learned)
- [13. Future Improvements](#13-future-improvements)
- [14. Conclusion](#14-conclusion)

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

## Project Status

Planning Phase
