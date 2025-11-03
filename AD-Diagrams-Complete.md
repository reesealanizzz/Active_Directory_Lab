# Active Directory Lab - Network Diagram

## Overview
This diagram illustrates the complete Active Directory lab environment including the domain controller, client workstations, organizational structure, and network services.

## Network Architecture

### Physical/Virtual Layer
```
┌─────────────────────────────────────────────────────────────┐
│                    VMware Workstation                        │
│                  Virtual Network: Host-Only                  │
│                 Network: 192.168.10.0/24                     │
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │   DC01       │    │  CLIENT01    │    │  CLIENT02    │  │
│  │   4 GB RAM   │    │   4 GB RAM   │    │   2 GB RAM   │  │
│  │   60 GB Disk │    │   40 GB Disk │    │   40 GB Disk │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Detailed Architecture

### 1. Domain Controller (DC01)
```
┌───────────────────────────────────────────────────────────────┐
│                    DC01 - Domain Controller                    │
│                   Windows Server 2022                          │
│                   IP: 192.168.10.10/24                         │
├───────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Active Directory Domain Services                        │  │
│  │  Domain: cyberlab.local                                  │  │
│  │  Forest Functional Level: Windows Server 2016            │  │
│  │  Domain Functional Level: Windows Server 2016            │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  DNS Server                                              │  │
│  │  • Primary Zone: cyberlab.local                          │  │
│  │  • Listening Address: 127.0.0.1, 192.168.10.10          │  │
│  │  • Forwarder: 8.8.8.8 (Google DNS)                       │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  DHCP Server                                             │  │
│  │  • Scope: 192.168.10.100 - 192.168.10.200              │  │
│  │  • Subnet Mask: 255.255.255.0                           │  │
│  │  • Gateway: 192.168.10.10                               │  │
│  │  • DNS: 192.168.10.10                                   │  │
│  │  • Lease Duration: 8 hours                              │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Group Policy Management                                 │  │
│  │  • 4 GPOs configured                                     │  │
│  │  • Applied to domain/OUs                                 │  │
│  └─────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

### 2. Organizational Unit Structure
```
cyberlab.local
├── Domain Controllers (Default)
│   └── DC01
│
├── CyberLab_Users
│   ├── IT_Department
│   │   ├── jsmith (John Smith)
│   │   └── mjohnson (Mike Johnson)
│   ├── Security_Team
│   │   ├── jdoe (Jane Doe)
│   │   └── edavis (Emily Davis)
│   ├── HR_Department
│   │   └── swilliams (Sarah Williams)
│   └── Sales_Department
│       └── tbrown (Tom Brown)
│
├── CyberLab_Computers
│   ├── Workstations
│   │   ├── CLIENT01
│   │   └── CLIENT02
│   ├── Laptops (Empty)
│   └── Servers (Empty)
│
├── CyberLab_Groups
│   ├── IT_Admins (jsmith, mjohnson)
│   ├── Security_Analysts (jdoe, edavis)
│   ├── HR_Staff (swilliams)
│   └── Sales_Team (tbrown)
│
└── Service_Accounts
    └── svc_backup
```

### 3. Client Workstations
```
┌────────────────────────────┐     ┌────────────────────────────┐
│       CLIENT01             │     │       CLIENT02             │
│   Windows 10/11 Pro        │     │   Windows 10/11 Pro        │
│   IP: 192.168.10.100       │     │   IP: 192.168.10.101       │
│   (DHCP Assigned)          │     │   (DHCP Assigned)          │
│   DNS: 192.168.10.10       │     │   DNS: 192.168.10.10       │
│   Domain: cyberlab.local   │     │   Domain: cyberlab.local   │
│                            │     │                            │
│   GPOs Applied:            │     │   GPOs Applied:            │
│   ✓ Password Security      │     │   ✓ Password Security      │
│   ✓ Desktop Restrictions   │     │   ✓ Desktop Restrictions   │
│   ✓ Windows Firewall       │     │   ✓ Windows Firewall       │
│   ✓ Screen Lock            │     │   ✓ Screen Lock            │
└────────────────────────────┘     └────────────────────────────┘
```

### 4. Group Policy Objects (GPOs)
```
┌──────────────────────────────────────────────────────────────┐
│  GPO 1: Password Security Policy                             │
│  Link: cyberlab.local                                        │
│  ───────────────────────────────────────────────────         │
│  • Enforce password history: 12 passwords                    │
│  • Maximum password age: 90 days                             │
│  • Minimum password age: 1 day                               │
│  • Minimum password length: 12 characters                    │
│  • Password must meet complexity requirements: Enabled       │
│  • Store passwords using reversible encryption: Disabled     │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  GPO 2: Desktop Restrictions                                 │
│  Link: CyberLab_Users                                        │
│  ───────────────────────────────────────────────────         │
│  • Prevent access to command prompt: Enabled                 │
│  • Prohibit access to Control Panel and PC settings: Enabled │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  GPO 3: Windows Firewall Policy                              │
│  Link: CyberLab_Computers                                    │
│  ───────────────────────────────────────────────────         │
│  • Firewall state (Domain Profile): On                       │
│  • Inbound connections: Block (default)                      │
│  • Outbound connections: Allow (default)                     │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  GPO 4: Screen Lock Policy                                   │
│  Link: cyberlab.local                                        │
│  ───────────────────────────────────────────────────         │
│  • Machine inactivity limit: 900 seconds (15 minutes)        │
│  • Enable screen saver: Enabled                              │
│  • Screen saver timeout: 900 seconds                         │
│  • Password protect the screen saver: Enabled                │
└──────────────────────────────────────────────────────────────┘
```

### 5. Security Features
```
┌──────────────────────────────────────────────────────────────┐
│  Account Lockout Policy                                      │
│  ───────────────────────────────────────────────────         │
│  • Account lockout duration: 30 minutes                      │
│  • Account lockout threshold: 5 invalid logon attempts       │
│  • Reset account lockout counter after: 30 minutes           │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  Audit Policies                                              │
│  ───────────────────────────────────────────────────         │
│  • Audit Credential Validation: Success and Failure          │
│  • Audit Security Group Management: Success and Failure      │
│  • Audit User Account Management: Success and Failure        │
│  • Audit Logon: Success and Failure                          │
│  • Audit Logoff: Success                                     │
│  • Audit File Share: Success and Failure                     │
│  • Audit Policy Change: Success and Failure                  │
│  • Audit Sensitive Privilege Use: Success and Failure        │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  Protected Users Group                                       │
│  ───────────────────────────────────────────────────         │
│  Members: Emily Davis (edavis), Jane Doe (jdoe)              │
│  Protection:                                                 │
│  • Forces Kerberos authentication only (no NTLM)             │
│  • Requires AES encryption for Kerberos                      │
│  • Clears credentials from memory on logoff                  │
│  • Cannot use DES or RC4 encryption                          │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  Time-Based Access Control                                   │
│  ───────────────────────────────────────────────────         │
│  User: tbrown (Sales Department)                             │
│  Allowed Hours:                                              │
│  • Monday - Friday: 8:00 AM - 6:00 PM                        │
│  • Saturday - Sunday: Denied                                 │
└──────────────────────────────────────────────────────────────┘
```

## Network Flow Diagrams

### Authentication Flow
```
┌──────────────┐                                ┌──────────────┐
│  CLIENT01    │                                │     DC01     │
│              │                                │   (Domain    │
│  User: jsmith│                                │  Controller) │
└──────┬───────┘                                └──────┬───────┘
       │                                               │
       │  1. User enters credentials                   │
       │  (username: jsmith, password: ******)         │
       │───────────────────────────────────────────────>
       │                                               │
       │  2. Kerberos TGT Request (Port 88)            │
       │───────────────────────────────────────────────>
       │                                               │
       │                    3. Validate credentials    │
       │                       Check password policy   │
       │                       Check account status    │
       │                       Check logon hours       │
       │                                               │
       │  4. Issue TGT (Ticket Granting Ticket)        │
       │<───────────────────────────────────────────────
       │                                               │
       │  5. Request Service Ticket                    │
       │───────────────────────────────────────────────>
       │                                               │
       │  6. Issue Service Ticket                      │
       │<───────────────────────────────────────────────
       │                                               │
       │  7. Authentication successful                 │
       │  User logged in, GPOs applied                 │
       │                                               │
```

### DHCP Assignment Flow
```
┌──────────────┐                                ┌──────────────┐
│  CLIENT01    │                                │     DC01     │
│  (No IP)     │                                │ DHCP Server  │
└──────┬───────┘                                └──────┬───────┘
       │                                               │
       │  1. DHCP DISCOVER (Broadcast)                 │
       │───────────────────────────────────────────────>
       │     "I need an IP address"                    │
       │                                               │
       │                    2. Check available IPs     │
       │                       Range: .100 - .200      │
       │                       Next available: .100    │
       │                                               │
       │  3. DHCP OFFER                                │
       │<───────────────────────────────────────────────
       │     "Use 192.168.10.100"                      │
       │                                               │
       │  4. DHCP REQUEST                              │
       │───────────────────────────────────────────────>
       │     "I accept 192.168.10.100"                 │
       │                                               │
       │                    5. Bind IP to MAC address  │
       │                       Create lease (8 hours)  │
       │                                               │
       │  6. DHCP ACK                                  │
       │<───────────────────────────────────────────────
       │     IP: 192.168.10.100                        │
       │     Subnet: 255.255.255.0                     │
       │     Gateway: 192.168.10.10                    │
       │     DNS: 192.168.10.10                        │
       │     Lease: 8 hours                            │
       │                                               │
       │  7. Configure network interface                │
       │     CLIENT01 now has IP address               │
       │                                               │
```

### DNS Resolution Flow
```
┌──────────────┐                                ┌──────────────┐
│  CLIENT01    │                                │     DC01     │
│              │                                │  DNS Server  │
└──────┬───────┘                                └──────┬───────┘
       │                                               │
       │  1. User types: \\dc01\share                  │
       │     Need to resolve: dc01                     │
       │                                               │
       │  2. DNS Query: "What is IP of dc01?"          │
       │     Query Type: A (IPv4 address)              │
       │───────────────────────────────────────────────>
       │                                               │
       │                    3. Check local zone        │
       │                       Zone: cyberlab.local    │
       │                       Record: dc01            │
       │                       IP: 192.168.10.10       │
       │                                               │
       │  4. DNS Response                              │
       │<───────────────────────────────────────────────
       │     dc01.cyberlab.local = 192.168.10.10       │
       │                                               │
       │  5. Connect to \\192.168.10.10\share          │
       │                                               │
```

### Group Policy Application Flow
```
┌──────────────┐                                ┌──────────────┐
│  CLIENT01    │                                │     DC01     │
│              │                                │    AD DS     │
└──────┬───────┘                                └──────┬───────┘
       │                                               │
       │  1. Computer startup / User logon             │
       │───────────────────────────────────────────────>
       │     "I need Group Policies"                   │
       │                                               │
       │                    2. Determine GPO list      │
       │                       Check computer OU       │
       │                       Check user OU           │
       │                       Collect linked GPOs     │
       │                                               │
       │  3. Send GPO list                             │
       │<───────────────────────────────────────────────
       │     GPOs to apply:                            │
       │     - Password Security Policy                │
       │     - Desktop Restrictions                    │
       │     - Windows Firewall Policy                 │
       │     - Screen Lock Policy                      │
       │                                               │
       │  4. Request GPO settings                      │
       │───────────────────────────────────────────────>
       │                                               │
       │  5. Download GPO settings                     │
       │<───────────────────────────────────────────────
       │                                               │
       │  6. Apply settings locally                    │
       │     - Registry changes                        │
       │     - Security policies                       │
       │     - Desktop restrictions                    │
       │                                               │
       │  7. Log Event ID 4016: Completed              │
       │                                               │
```

## IP Address Allocation

| Device/Purpose | IP Address | Type | Notes |
|----------------|------------|------|-------|
| Domain Controller (DC01) | 192.168.10.10 | Static | Primary DNS, DHCP, AD DS |
| Reserved Range | 192.168.10.1 - 192.168.10.99 | Static | For servers and infrastructure |
| DHCP Scope Start | 192.168.10.100 | Dynamic | First available DHCP address |
| CLIENT01 | 192.168.10.100 | DHCP | Assigned by DHCP |
| CLIENT02 | 192.168.10.101 | DHCP | Assigned by DHCP |
| DHCP Scope End | 192.168.10.200 | Dynamic | Last available DHCP address |
| Unused Range | 192.168.10.201 - 192.168.10.254 | - | Available for future expansion |
| Broadcast | 192.168.10.255 | Broadcast | Network broadcast address |

## Port Reference

| Service | Ports | Protocol | Purpose |
|---------|-------|----------|---------|
| DNS | 53 | TCP/UDP | Domain name resolution |
| DHCP | 67, 68 | UDP | IP address assignment |
| Kerberos | 88 | TCP/UDP | Authentication |
| RPC | 135 | TCP | Remote procedure calls |
| NetBIOS | 137-139 | TCP/UDP | NetBIOS over TCP/IP |
| LDAP | 389 | TCP/UDP | Directory queries |
| SMB/CIFS | 445 | TCP | File sharing |
| LDAPS | 636 | TCP | Secure LDAP |
| Global Catalog | 3268 | TCP | Global catalog queries |
| Global Catalog SSL | 3269 | TCP | Secure global catalog |
| RDP | 3389 | TCP | Remote Desktop (if enabled) |

## Security Event IDs Reference

| Event ID | Description | Significance |
|----------|-------------|--------------|
| 4624 | Successful logon | Track user authentication |
| 4625 | Failed logon | Detect brute force attempts |
| 4634 | Account logged off | Session tracking |
| 4672 | Special privileges assigned | Track elevated access |
| 4720 | User account created | Monitor new accounts |
| 4722 | User account enabled | Track account changes |
| 4723 | Password change attempt | Password management |
| 4724 | Password reset | Administrative action |
| 4738 | User account changed | Detect permission escalation |
| 4740 | Account locked out | Security incident |
| 4767 | Account unlocked | Administrative action |

## How to Use These Diagrams

1. **For GitHub README.md**: Use the Mermaid diagram (.mmd file)
2. **For Documentation**: Use the ASCII diagram (.txt file)
3. **For Presentations**: Convert to PNG/SVG using online tools
4. **For Reports**: Copy sections as needed

## Converting Mermaid to Image

### Online Tools:
- https://mermaid.live (paste the .mmd file content)
- https://mermaid.ink (generate PNG from code)

### Command Line:
```bash
npm install -g @mermaid-js/mermaid-cli
mmdc -i AD-Network-Diagram.mmd -o AD-Network-Diagram.png
```

### VS Code:
Install "Markdown Preview Mermaid Support" extension, then view the diagram in preview.

---

**Created by**: Reese
**Project**: Active Directory Enterprise Lab
**Date**: November 2025
