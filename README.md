# Splunk LDAP Search App Configuration Guide




<img width="928" height="546" alt="ldap_search_app_view" src="https://github.com/user-attachments/assets/6812c6f5-f89a-432c-912a-fa2cbf57a822" />





This document explains how to configure LDAP connectivity between Splunk and Microsoft Active Directory for the LDAP Search App.

---

# Overview

This guide covers:

- Active Directory information required by Splunk
- How to retrieve LDAP values from Windows
- LDAP Search App configuration
- LDAP connectivity testing
- Common troubleshooting steps

---

# Prerequisites

Before starting, ensure the following requirements are met:

- Splunk Enterprise installed
- LDAP Search App (Splunk Supporting Add-on for Active Directory) installed
- Network connectivity to Domain Controller
- Valid Active Directory service account

---

# Required Active Directory Information

The following values are required during configuration:

| Field | Description |
|------|-------------|
| Domain Name | DNS name of the AD domain |
| Alternate Domain Name | NetBIOS domain name |
| Base DN | LDAP Distinguished Name of the domain |
| LDAP Server | Domain Controller hostname or IP |
| LDAP Port | LDAP/LDAPS port |
| Bind DN | Service account used for LDAP bind |
| Password | Password of the service/user account |

---

# How to Retrieve Active Directory Information

## Step 1 - Get Domain Name

### CMD
```cmd
echo %USERDNSDOMAIN%
```

### PowerShell
```powershell
(Get-ADDomain).DNSRoot
```

Example output:
```text
test.local
```

---

## Step 2 - Get Alternate Domain Name (NetBIOS Name)

### CMD
```cmd
echo %USERDOMAIN%
```

### PowerShell
```powershell
(Get-ADDomain).NetBIOSName
```

Example output:
```text
TEST
```

Note: NetBIOS name is always uppercase.

---

## Step 3 - Get Base DN

### PowerShell
```powershell
(Get-ADDomain).DistinguishedName
```

Example output:
```text
DC=corp,DC=local
```

---

## Step 4 - Find LDAP Server (Domain Controller)

### CMD
```cmd
nltest /dsgetdc:%USERDOMAIN%
```

### Alternative CMD
```cmd
ipconfig
```

### PowerShell
```powershell
Get-ADDomainController
```

Example output:
```text
dc01.corp.local
```

---

## Step 5 - Get Service Account DN

### PowerShell
```powershell
Get-ADUser splunk_ldap -Properties DistinguishedName
```

Example output:
```text
CN=splunk_ldap,CN=Users,DC=corp,DC=local
```

---

# Create Dedicated Service Account for Splunk LDAP

It is recommended to create a dedicated service account with minimal privileges.

## Step 1 - Create User (on Domain Controller)

Open Command Prompt as Administrator:

```cmd
net user splunk_ldap StrongPassword123! /add /domain
```

---

## Step 2 - Verify User

```cmd
net user splunk_ldap /domain
```

---

## Step 3 - Prevent Password Expiration (Recommended)

### PowerShell
```powershell
Set-ADUser splunk_ldap -PasswordNeverExpires $true
```

---

# Notes

- Use a dedicated service account for Splunk LDAP integration
- Avoid using Domain Admin accounts
- Ensure network access to LDAP ports (389 / 636)
- Prefer LDAPS in production environments

---
