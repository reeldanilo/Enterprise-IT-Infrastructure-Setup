# Enterprise IT Infrastructure Home Lab

I built this Windows Server home lab using VMware Workstation Pro to practice managing a small business IT environment. The project covers Active Directory, DNS, DHCP, Group Policy, and access to departmental network shares across two Windows 11 workstations.

For the configuration steps and verification commands, see the [setup documentation](docs/README.md).

## Lab Environment

| Machine | Operating system | Role | IP address |
| --- | --- | --- | --- |
| DC01 | Windows Server 2025 Evaluation | Domain controller, DNS, DHCP, file server | `192.168.50.10` (static) |
| PC01 | Windows 11 Enterprise Evaluation | Domain-joined workstation | DHCP (`192.168.50.100`) |
| PC02 | Windows 11 Enterprise Evaluation | Domain-joined workstation | DHCP (`192.168.50.101`) |

| Network setting | Configuration |
| --- | --- |
| Domain | `enterpriseit.local` |
| Virtual network | `192.168.50.0/24` |
| Default gateway | `192.168.50.2` |
| DNS server | `192.168.50.10` (DC01) |
| DHCP scope | `192.168.50.100` – `192.168.50.200` |

### Network Diagram

```text
                       enterpriseit.local
                               |
                    DC01 - Windows Server 2025
                      AD DS / DNS / DHCP
                         192.168.50.10
                               |
                   VMware VMnet8 (NAT network)
                         192.168.50.0/24
                               |
                   +-----------+-----------+
                   |                       |
              PC01 - Windows 11       PC02 - Windows 11
              192.168.50.100          192.168.50.101
              DHCP client             DHCP client
```

The two client addresses above are the leases observed during testing, not static assignments. VMware provides the NAT network, while DC01 handles DHCP for the lab clients.

## Configuration

### Active Directory and DNS

- Installed Windows Server 2025 on DC01 and configured a static IP address.
- Installed Active Directory Domain Services (AD DS) and created the `enterpriseit.local` forest.
- Configured DNS on DC01 for domain name resolution.
- Joined PC01 and PC02 to the domain and verified domain user sign-in.

### Organizational Units and Security Groups

I organized the domain users into departmental OUs and created security groups to manage access to shared folders.

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

Each employee belongs to the security group for their department. The OUs are used to organize accounts and target Group Policies, while the groups are used to grant access to shared resources.

### Group Policy

I configured two GPOs:

| GPO | Applied to | Configuration |
| --- | --- | --- |
| `Workstations - Auto Lock 10 min` | Workstations OU | Sets the machine inactivity limit to 600 seconds |
| `Employees - Restrict Control Panel` | HR and Finance OUs | Blocks access to Control Panel and PC settings |

I verified the workstation policy using `gpresult` and the `InactivityTimeoutSecs` registry value. I also tested the Control Panel restriction with domain accounts: HR and Finance users were blocked, while the IT user could open Control Panel.

### File Sharing and Permissions

I created departmental folders on DC01 and shared them over the network. Access is controlled with Active Directory security groups and NTFS permissions.

| Shared folder | UNC path | Authorized group |
| --- | --- | --- |
| HR | `\\DC01\HR` | `GRP_HR` |
| Finance | `\\DC01\Finance` | `GRP_Finance` |
| IT | `\\DC01\IT` | `GRP_IT` |

The authorized groups have Modify access through NTFS and Change/Read access through the share permissions. I tested access with different domain users to confirm they could work with their department's files and were denied access to other departments' shares.

### DHCP Server

I installed and authorized the DHCP Server role on DC01, then created the `EnterpriseIT Clients` IPv4 scope.

| DHCP option | Value |
| --- | --- |
| Address range | `192.168.50.100` – `192.168.50.200` |
| Subnet mask | `255.255.255.0` |
| Router (003) | `192.168.50.2` |
| DNS server (006) | `192.168.50.10` |
| DNS domain (015) | `enterpriseit.local` |
| Lease duration | 8 days |

I disabled VMware's DHCP service on VMnet8 and activated the Windows Server scope. Both PCs renewed their addresses successfully. Their `ipconfig /all` results identified `192.168.50.10` as the DHCP server, and both leases appeared in DHCP Manager on DC01.

## Verification

The following checks were completed during the lab:

- Domain join and domain user sign-in on both Windows 11 workstations.
- DNS resolution and connectivity to DC01.
- User and computer Group Policy application.
- Departmental file-share access and denied-access tests.
- DHCP address assignment and lease records for PC01 and PC02.


## What I Learned

This project gave me hands-on practice with setting up a Windows domain, managing users and computers in Active Directory, applying Group Policies, configuring network services, and troubleshooting access and DHCP issues.
