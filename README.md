[README (4).md](https://github.com/user-attachments/files/29610302/README.4.md)
# 🖥️ Active Directory Lab on AWS EC2

A hands-on lab project demonstrating the deployment and configuration of Active Directory Domain Services (AD DS) on a Windows Server EC2 instance in AWS.

---

## 📋 Project Overview

This project simulates a real-world enterprise identity management environment built entirely in AWS. It covers domain setup, user and group management, Organizational Units (OUs), and Group Policy Objects (GPOs) — skills used daily in sysadmin, help desk, and cloud roles.

---

## 🛠️ Technologies Used

| Technology | Purpose |
|-----------|---------|
| AWS EC2 | Hosted Windows Server instance |
| Windows Server 2019/2022 | Operating system for Domain Controller |
| Active Directory Domain Services (AD DS) | Directory service for identity management |
| Active Directory Users and Computers (ADUC) | GUI tool for managing users and computers |
| Group Policy Management Console (GPMC) | Creating and managing GPOs |
| PowerShell | Command-line automation and configuration |
| Remote Desktop Protocol (RDP) | Remote access to EC2 instance |

---

## 🏗️ Architecture

```
AWS Cloud
└── VPC
    └── EC2 Instance (Windows Server - t3.medium)
        └── Active Directory Domain Services
            └── lab.local (Domain)
                ├── Organizational Units
                │   └── IT Department
                │       └── Test Users
                ├── Security Groups
                │   └── IT Staff
                └── Group Policy Objects
                    └── IT Policy (Password & Lockout)
```

---

## 🚀 Implementation Steps

### Step 1: Launch Windows Server EC2 Instance
- Launched Windows Server EC2 instance (t3.medium) in AWS
- Configured Security Group to allow RDP (port 3389)
- Retrieved Administrator password using EC2 key pair
- Connected via Remote Desktop Protocol (RDP)

### Step 2: Install Active Directory Domain Services
- Opened Server Manager on the Windows Server instance
- Used Add Roles and Features wizard
- Installed Active Directory Domain Services role with management tools

### Step 3: Promote Server to Domain Controller
- Imported ADDSDeployment PowerShell module
- Created new forest with domain name: `lab.local`
- Set NetBIOS name: `LAB`
- Configured DNS alongside AD DS
- Server rebooted automatically after promotion

```powershell
Import-Module ADDSDeployment

Install-ADDSForest `
  -DomainName "lab.local" `
  -DomainNetbiosName "LAB" `
  -InstallDns:$true `
  -Force:$true
```

### Step 4: Configure Active Directory Structure
- Created Organizational Unit: `IT Department`
- Created test user accounts within the OU
- Created Security Group: `IT Staff`
- Added users to security groups

```powershell
# Create a new user
New-ADUser `
  -Name "John Doe" `
  -SamAccountName "jdoe" `
  -UserPrincipalName "jdoe@lab.local" `
  -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) `
  -Enabled $true

# Create a security group
New-ADGroup -Name "IT Staff" -GroupScope Global -GroupCategory Security

# Add user to group
Add-ADGroupMember -Identity "IT Staff" -Members "jdoe"
```

### Step 5: Configure Group Policy Objects (GPOs)
- Created GPO `IT Policy` linked to `IT Department` OU
- Configured Password Policy:
  - Minimum password length: 10 characters
  - Password complexity: Enabled
  - Maximum password age: 90 days
- Configured Account Lockout Policy:
  - Lockout threshold: 5 invalid attempts
  - Lockout duration: 30 minutes
- Applied policies with `gpupdate /force`

---

## 🧩 Challenges & Troubleshooting

| Challenge | Solution |
|-----------|----------|
| AD DS promotion link not appearing in Server Manager | Used PowerShell ADDSDeployment module directly |
| ADUC showing empty tree | Identified server was not yet promoted to Domain Controller |
| `Install-ADDSForest` command not recognized | Imported the ADDSDeployment module first |
| Test users from AWS Managed AD not visible | Identified these as separate directories requiring a forest trust |

---

## ✅ Skills Demonstrated

- AWS EC2 provisioning and management
- Windows Server administration
- Active Directory deployment and configuration
- User, group, and OU management
- Group Policy creation and enforcement
- PowerShell scripting for AD automation
- Identity and access management (IAM)
- Real-world troubleshooting

---

## 🔭 Next Steps

- [ ] Join a second Windows EC2 instance to `lab.local` domain
- [ ] Test domain user login on member server
- [ ] Deploy AWS Managed Microsoft AD
- [ ] Establish forest trust between `lab.local` and AWS Managed AD
- [ ] Integrate AWS IAM Identity Center with Active Directory
- [ ] Monitor AD authentication events with CloudWatch


