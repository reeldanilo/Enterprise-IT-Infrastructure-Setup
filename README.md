# Enterprise IT Infrastructure Home Lab

A Windows Server home lab built with VMware Workstation Pro. I'm using it to practice managing a small Windows domain, from setting up the domain controller to joining client computers and applying Group Policies.

For the installation steps, commands, and additional screenshots, see the [setup documentation](docs/README.md).

## Lab Setup

| Machine | Operating system | Role | IP address |
| --- | --- | --- | --- |
| DC01 | Windows Server 2025 Evaluation | Domain controller and DNS server | `192.168.50.10` (static) |
| PC01 | Windows 11 Enterprise Evaluation | Domain-joined workstation | DHCP |
| PC02 | Windows 11 Enterprise Evaluation | Second workstation | Planned |

- **Domain:** `enterpriseit.local`
- **Network:** `192.168.50.0/24` (VMware NAT)
- **DNS for PC01:** `192.168.50.10` (DC01)
- **Tools and services:** VMware Workstation Pro, Active Directory Domain Services (AD DS), DNS, Group Policy

### Network Diagram

```text
                   enterpriseit.local
                           |
                  DC01 - Windows Server
                 AD DS / DNS / 192.168.50.10
                           |
                 VMware NAT (192.168.50.0/24)
                           |
                  --------------------
                  |                  |
             PC01 - Windows 11    PC02 (planned)
             Domain-joined
             IP via DHCP
```

PC01 gets its IP address from VMware's NAT DHCP service and uses DC01 for DNS. A Windows Server DHCP role has **not** been configured yet.

## Completed So Far

### Windows Server and Active Directory

- Installed Windows Server 2025 on DC01 and configured a static IP address.
- Installed AD DS and promoted DC01 to a domain controller for `enterpriseit.local`.
- Configured DNS for the domain.

### Windows 11 Client

- Installed Windows 11 Enterprise Evaluation on PC01.
- Set PC01's preferred DNS server to DC01.
- Verified connectivity with `ping` and domain resolution with `nslookup`.
- Joined PC01 to `enterpriseit.local`.

### Users and Organizational Units

- Created the `IT`, `Employees`, and `Workstations` OUs.
- Moved PC01 into `Workstations`.
- Created the test user `john.doe` in `Employees` and signed in to PC01 with the domain account.

### Group Policy

- **Workstations - Auto Lock 10 min:** Applied a computer policy setting a 600-second inactivity limit. Verified the GPO with `gpresult` and the configured value in the registry. The actual 10-minute lock has not been timed yet.
- **Employees - Restrict Control Panel:** Applied a user policy to block Control Panel access. Tested it with `john.doe` and confirmed the restriction message.

## Next Steps

- Create departmental users and security groups.
- Configure shared folders and NTFS permissions.
- Add PC02 and test policies on another workstation.
- Configure and test the Windows Server DHCP role.
- Finish testing the automatic workstation lock.

## Documentation

[View setup and configuration notes](docs/README.md)

