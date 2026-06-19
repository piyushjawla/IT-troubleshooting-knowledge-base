# Active Directory File Share Access Denied Troubleshooting

## Project Overview

This project simulates a common enterprise IT support scenario where a domain user is unable to access a shared network folder.

Rather than immediately creating a file share and troubleshooting access issues, the project begins by building the Active Directory structure that would typically exist in a real organization.

The goal is to understand how:

- Active Directory users
- Security groups
- Organizational Units (OUs)
- Share permissions
- NTFS permissions

work together to control access to shared resources.

---

## Environment

### Domain Controller

- Windows Server 2019
- Active Directory Domain Services (AD DS)
- Domain: `homelab.local`

### Client System

- Windows 10 Pro (Domain Joined)

### Host System

- Windows 11 Home Single Language

---

# Building The Organizational Structure

Before users and permissions can be managed effectively, an organizational structure must be created inside Active Directory.

In real environments, placing every user and group inside the default containers quickly becomes difficult to manage.

To improve organization and scalability, dedicated Organizational Units (OUs) were created.

---

## Opening Active Directory Users and Computers

Active Directory Users and Computers (ADUC) is one of the primary management consoles used by Windows administrators.

From this interface administrators can create and manage:

- Users
- Groups
- Computers
- Organizational Units

The domain `homelab.local` was verified before any configuration work began.

<img width="1440" height="854" alt="ADUC-opened" src="https://github.com/user-attachments/assets/2a7a9f07-4465-4c28-9970-35bf44193dce" />


---

## Creating Department OUs

A parent OU named:

```text
Departments
```

was created directly under the domain.

Inside this OU, separate departmental containers were created:

```text
Departments
├── IT
├── HR
└── Finance
```

This structure simulates how organizations separate departments and provides a foundation for future administration tasks such as:

- User management
- Group Policy deployment
- Delegation
- Department-specific administration

<img width="850" height="595" alt="department-OUs-created" src="https://github.com/user-attachments/assets/42ed5016-2e90-4ca2-8d08-2d7e8d1580f3" />


---

## Creating A Dedicated Security Groups OU

A separate OU named:

```text
Security Groups
```

was created.

Rather than storing groups throughout the directory, many organizations centralize security groups in a dedicated location to simplify administration and auditing.

<img width="847" height="595" alt="security-groups-OU" src="https://github.com/user-attachments/assets/3ad704d6-f82e-420b-83d6-dcb3c8397711" />


---

## Creating A Dedicated User Accounts OU

A separate OU named:

```text
User Accounts
```

was created to store employee accounts.

Separating user objects from groups and computers helps maintain a cleaner directory structure and makes future administration significantly easier.

<img width="849" height="595" alt="user-accounts-ou" src="https://github.com/user-attachments/assets/1368f5f6-ec06-4f0f-8420-1a6b7d2209d3" />


---

## Creating A Security Group

To prepare for future access control, a security group named:

```text
IT-Share-Users
```

was created.

Configuration:

| Setting | Value |
|----------|----------|
| Group Scope | Global |
| Group Type | Security |

This follows a common enterprise principle:

```text
Users → Groups → Permissions
```

rather than:

```text
Users → Permissions
```

Using groups makes access management significantly more scalable as organizations grow.

<img width="850" height="595" alt="it-share-users-group" src="https://github.com/user-attachments/assets/d5f98d79-3248-40f3-a54c-d60ebb2bc39c" />


---

## Creating A Test Domain User

A test account named:

```text
ITUser01
```

was created inside the User Accounts OU.

This account will be used throughout the project to validate access to shared resources and simulate a real employee account.

<img width="846" height="596" alt="ituser01-created" src="https://github.com/user-attachments/assets/23003740-7025-444b-ae44-a88e87a297e5" />


---

## Assigning Group Membership

The account:

```text
ITUser01
```

was added to:

```text
IT-Share-Users
```

using the **Member Of** tab.

This is an important step because access will eventually be granted through group membership rather than directly to the user account.

This mirrors how permissions are commonly managed in enterprise environments.

<img width="461" height="602" alt="ituser01-added-to-SG" src="https://github.com/user-attachments/assets/80bb1539-706d-4e29-99e9-8a9b25d01a35" />


---

# Concepts Learned

### Organizational Units (OUs)

Used to organize Active Directory objects into logical administrative structures.

### Security Groups

Used to assign permissions efficiently to multiple users.

### Group-Based Access Control

Permissions are generally assigned to groups rather than individual users.

### User Administration

Creating and managing domain user accounts within Active Directory.

---

# Current Status

Completed:

- Active Directory organizational structure
- Department OUs
- Security Groups OU
- User Accounts OU
- Security Group creation
- Domain User creation
- Group membership assignment

Upcoming Work:

- Create departmental file share
- Configure share permissions
- Configure NTFS permissions
- Test successful access
- Simulate Access Denied scenario
- Troubleshoot and restore access

---

## Next Phase

The next phase will focus on deploying a departmental file share, implementing group-based access control, and troubleshooting access failures using Active Directory security groups and NTFS permissions.
