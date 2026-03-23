# ad-helpdesk-lab
# Active Directory Help Desk Lab

Simulating real IT support workflows using Active Directory, a ticketing system, and a hybrid cloud/on-prem environment.

---

## Overview

This lab simulates the day-to-day responsibilities of an IT Support / Help Desk technician. The focus is on **Active Directory account management** integrated with a structured ticketing workflow — covering the two most common ticket categories in any support environment: account lockouts and access permission issues.

Rather than just studying concepts, I built the environment from scratch and worked through realistic user scenarios from ticket open to ticket close.

---

## Lab Architecture

| Component | Role |
|---|---|
| Windows Server 2022 | Domain Controller — Active Directory Domain Services |
| Windows 10 (domain-joined) | End-user workstation for `bluegate.local` |
| Peppermint OS (Docker on GCP) | Helpdesk ticketing system |
| Tailscale | VPN tunnel connecting on-prem AD to cloud-hosted ticketing |

**Domain:** `bluegate.local`  
**Server hostname:** `BLUEGATE-SERVER`

### Organisational Unit Structure

```
bluegate.local
├── IT
├── Finance
└── HR
```

**Users created:**
- `Adam Kinsley` — IT department (end user)
- `Support` — Help Desk staff account (admin)

---

## Scenario 1 — Password Reset & Account Unlock

### User Story
Adam Kinsley forgot his password, exceeded the failed login attempt threshold, and was locked out of his account. He contacted the help desk for assistance.

### Workflow

**Step 1 — Ticket created**  
Adam emailed the help desk. Peppermint auto-generated ticket **#2**:
> *"I forgot my password and I think my account is locked."*

**Step 2 — Ticket received and triaged**  
Logged in as the Support account and reviewed the open ticket in Peppermint dashboard.

**Step 3 — Resolved in ADUC**  
In Active Directory Users and Computers:
- Confirmed account lockout status on the domain controller
- Reset password to temporary credentials
- Checked **"User must change password at next logon"**
- Checked **"Unlock the user's account"**

**Step 4 — Ticket updated and closed**  
Added resolution note to the ticket:
> *"Password reset to temporary credentials. Account unlocked. User must change password at next login."*

Ticket status updated to **Done** and closed.

---

## Scenario 2 — Inaccessible Shared Folder

### User Story
Adam reported he could not access the IT department shared folder at `\\bluegate-srv\IT_FileShare`, receiving an "Access Denied" error.

### Workflow

**Step 1 — Ticket created**  
Adam submitted ticket **#1** in Peppermint:
> *"I am unable to open the IT shared folder. It says I don't have permission."*

**Step 2 — Investigation**  
As IT staff:
- Confirmed `IT_FileShare` was restricted to the `IT_Staff` security group
- Opened ADUC and checked Adam's group memberships
- Found he was not a member of `IT_Staff`

**Step 3 — Access restored**  
Added `Adam Kinsley (akinsley@bluegate.local)` to the `IT_Staff` security group via ADUC.

**Step 4 — Verified**  
Asked Adam to log off and back on. Confirmed he could now access `\\bluegate-srv\IT_FileShare` successfully.

**Step 5 — Ticket closed**  
Updated and closed the ticket:
> *"Added user to shared folder security group. Access to IT_FileShare confirmed."*

---

## Skills Demonstrated

- Active Directory administration — users, groups, OUs, and domain setup
- Account management — password resets, lockout resolution, logon policies
- Access control — NTFS permissions, security group membership management
- Ticketing workflow — ticket intake, documentation, resolution notes, closure
- Docker deployment — self-hosted Peppermint ticketing on a GCP VM
- Hybrid networking — Tailscale VPN connecting on-prem AD with cloud infrastructure

---

## Tools & Technologies

`Windows Server 2022` `Active Directory` `ADUC` `Windows 10` `Peppermint` `Docker` `Google Cloud Platform` `Tailscale` `NTFS Permissions`

---

## What I'd Build Next

- Add a third scenario covering **new user onboarding** (account creation, group assignment, shared drive access)
- Automate the password reset workflow using **PowerShell**
- Integrate **Wazuh** to capture and alert on AD events (lockout events, group membership changes)
