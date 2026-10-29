# Enterprise IT Infrastructure Home Lab

## Overview

This project simulates a small enterprise IT environment using Windows Server 2025, Active Directory, and VMware Workstation Pro.

The goal is to gain hands-on experience with IT infrastructure administration, including server deployment, network configuration, domain management, user authentication, and centralized computer management.

## Technologies Used

| Category | Technologies |
|---|---|
| Virtualization | VMware Workstation Pro |
| Server OS | Windows Server 2025 Evaluation |
| Client OS | Windows 11 Enterprise Evaluation |
| Directory Services | Active Directory Domain Services (AD DS) |
| Networking | TCP/IP, DNS |
| Administration | Active Directory Users and Computers |
| Planned | Group Policy, DHCP Server |

## Network Architecture

The lab uses a virtual NAT network connecting a Windows Server domain controller and Windows client machines.

```text
             enterpriseit.local
                     |
           DC01 - Domain Controller
             Windows Server 2025
               192.168.50.10
                     |
            VMware NAT Network
              192.168.50.0/24
                     |
          -----------------------
          |                     |
    PC01 - Windows 11      PC02 - Windows 11
    Enterprise Eval.          (Planned)
    Domain-Joined
    DHCP: 192.168.50.129
```

**Domain:** `enterpriseit.local`  
**Domain Controller:** `DC01`  
**Network:** `192.168.50.0/24`  
**DNS Server:** `192.168.50.10`

PC01 receives its IP address through DHCP and uses DC01 as its preferred DNS server.

## Implementation Progress

### Completed

**1. Virtual Infrastructure Setup**
- Installed and configured VMware Workstation Pro.
- Created virtual machines for Windows Server 2025 and Windows 11 Enterprise.
- Configured virtual networking using VMware NAT.

**2. Windows Server & Active Directory**
- Installed and configured Windows Server 2025.
- Assigned a static IP address to DC01.
- Installed Active Directory Domain Services (AD DS).
- Promoted DC01 to a domain controller.
- Created the `enterpriseit.local` domain and configured DNS.

**3. Windows Client Deployment**
- Installed Windows 11 Enterprise Evaluation on PC01.
- Configured DNS to communicate with the domain controller.
- Verified connectivity using `ping` and `nslookup`.
- Successfully joined PC01 to the Active Directory domain.

**4. Active Directory Management**
- Created Organizational Units (IT, Employees, Workstations).
- Moved PC01 into the Workstations OU.
- Created a test domain user account.
- Successfully authenticated on PC01 using domain credentials.

### Planned Improvements

- Configure and test Group Policies (GPO).
- Expand the Active Directory structure with additional users, groups, and departments.
- Deploy and join a second Windows 11 client (PC02).
- Configure DHCP services on Windows Server.
- Test access restrictions and centralized computer management.

## Documentation

The complete step-by-step documentation includes installation procedures, network configurations, Active Directory setup, verification commands, and screenshots.

**[View Full Step-by-Step Documentation](docs/README.md)**

## What I Learned

Through this project, I have gained hands-on experience with:

- Installing and configuring Windows Server in a virtualized environment.
- Understanding the role of Active Directory and domain controllers.
- Configuring IP addresses and DNS for domain communication.
- Joining Windows client machines to an Active Directory domain.
- Creating and organizing user and computer objects using Organizational Units.
- Testing domain authentication and network connectivity.

## Future Development

This project is ongoing and will be expanded with additional Windows Server services, Group Policy configurations, and enterprise IT administration scenarios.
