# OSCP Exam Cheat Sheet
This cheat sheet was created throughout my studies in Hack The Box Academy's Penetration Tester Job Role Path and OffSec's PEN-200 (PWK) course, as part of my preparation for the CPTS and OSCP certifications.

It contains commands, techniques, and notes collected throughout my training and served as a practical reference during my OSCP exam. It also includes a collection of tools and scripts used in practice labs throughout my studies and exam preparation.

I'm sharing it as a resource for other students and aspiring penetration testers who may find it useful during their own learning journey. I plan to continue expanding and updating it as I gain more experience and pursue additional certifications.
## Enumeration
- **[Nmap](01-Enumeration/README.md#nmap)**
  - [Regular Scanning](01-Enumeration/README.md#regular-scanning)
  - [Scanning Techniques](01-Enumeration/README.md#scanning-techniques)
  - [Aggressive Scanning](01-Enumeration/README.md#aggressive-scanning)
  - [NSE Scripts](01-Enumeration/README.md#nse-scripts)
  - [Firewall Evasion](01-Enumeration/README.md#firewall-evasion)
  - [Output](01-Enumeration/README.md#output)

- **[Footprinting](01-Enumeration/README.md#footprinting)**
  - [FTP](01-Enumeration/README.md#ftp)
  - [SMB](01-Enumeration/README.md#smb)
    - [Null Authentication](01-Enumeration/README.md#null-authentication)
    - [SMB Enumeration](01-Enumeration/README.md#smb-enumeration)
      - [Enum4Linux](01-Enumeration/README.md#enum4linux)
      - [SMBClient](01-Enumeration/README.md#smbclient)
      - [RPCClient](01-Enumeration/README.md#rpcclient)
      - [NetExec](01-Enumeration/README.md#netexec)
  - [NFS](01-Enumeration/README.md#nfs)
  - [DNS](01-Enumeration/README.md#dns)
  - [SMTP](01-Enumeration/README.md#smtp)
  - [IMAP / POP3](01-Enumeration/README.md#imap--pop3)
    - [IMAP Commands](01-Enumeration/README.md#imap-commands)
    - [POP3 Commands](01-Enumeration/README.md#pop3-commands)
  - [SNMP](01-Enumeration/README.md#snmp)
  - [MySQL](01-Enumeration/README.md#mysql)
  - [MSSQL](01-Enumeration/README.md#mssql)
  - [PostgreSQL](01-Enumeration/README.md#postgresql)
  - [Oracle TNS](01-Enumeration/README.md#oracle-tns)

- **[Web Enumeration](01-Enumeration/README.md#web-enumeration)**
  - [Basic Enumeration Checklist](01-Enumeration/README.md#basic-enumeration-checklist)
    - [Nikto](01-Enumeration/README.md#nikto)
  - [Virtual Host Scanning](01-Enumeration/README.md#virtual-host-scanning)
  - [Directory Brute Forcing](01-Enumeration/README.md#directory-brute-forcing)

## Exploitation
- **[SQL Injection](02-Exploitation/README.md#sql-injection)**
  - [Error Based](02-Exploitation/README.md#error-based)
  - [Union Based Payloads](02-Exploitation/README.md#union-based-payloads)
  - [Blind SQL Injection](02-Exploitation/README.md#blind-sql-injection)
  - [MSSQL Payloads](02-Exploitation/README.md#mssql-payloads)

- **[Database Exploitation](02-Exploitation/README.md#database-exploitation)**
  - [MySQL / MariaDB](02-Exploitation/README.md#mysql--mariadb)
  - [PostgreSQL](02-Exploitation/README.md#postgresql)
  - [MSSQL](02-Exploitation/README.md#mssql)

- **[Reverse Shells](02-Exploitation/README.md#reverse-shells)**
  - [Useful One Liners](02-Exploitation/README.md#useful-one-liners)
  - [Shell Upgrade](02-Exploitation/README.md#shell-upgrade)

- **[File Transfer](02-Exploitation/README.md#file-transfer)**
  - [Linux](02-Exploitation/README.md#linux)
  - [Windows](02-Exploitation/README.md#windows)
  - [SCP](02-Exploitation/README.md#scp)
## Privilege Escalation
- **[Windows](03-Privilege%20Escalation/README.md#windows)**
  - [Checklist](03-Privilege%20Escalation/README.md#checklist)
  - **[Enumeration](03-Privilege%20Escalation/README.md#enumeration)**
    - [Privilege Enumeration](03-Privilege%20Escalation/README.md#privilege-enumeration)
    - [User Enumeration](03-Privilege%20Escalation/README.md#user-enumeration)
    - [File Enumeration](03-Privilege%20Escalation/README.md#file-enumeration)
    - [PowerShell History](03-Privilege%20Escalation/README.md#powershell-history)
    - [Service Enumeration](03-Privilege%20Escalation/README.md#service-enumeration)
      - [Service Privilege Check](03-Privilege%20Escalation/README.md#service-privilege-check)
    - [Scheduled Tasks](03-Privilege%20Escalation/README.md#scheduled-tasks)
    - [Program Enumeration](03-Privilege%20Escalation/README.md#program-enumeration)
    - [Automated Scanning](03-Privilege%20Escalation/README.md#automated-scanning)
  - **[Privilege Exploitation](03-Privilege%20Escalation/README.md#privilege-exploitation)**
    - [SeImpersonate](03-Privilege%20Escalation/README.md#seimpersonate)
    - [SeTcbPrivilege](03-Privilege%20Escalation/README.md#setcbprivilege)
    - [SeTakeOwnership Privilege](03-Privilege%20Escalation/README.md#setakeownership-privilege)
    - [SeDebugPrivilege](03-Privilege%20Escalation/README.md#sedebugprivilege)
    - [SeBackupPrivilege](03-Privilege%20Escalation/README.md#sebackupprivilege)
    - [SeManageVolumePrivilege](03-Privilege%20Escalation/README.md#semanagevolumeprivilege)
    - [SeRestorePrivilege](03-Privilege%20Escalation/README.md#serestoreprivilege)

- **[Linux](03-Privilege%20Escalation/README.md#linux)**
  - [Checklist](03-Privilege%20Escalation/README.md#checklist-1)
  - **[Enumeration](03-Privilege%20Escalation/README.md#enumeration-1)**
    - [User Enumeration](03-Privilege%20Escalation/README.md#user-enumeration-1)
    - [System Enumeration](03-Privilege%20Escalation/README.md#system-enumeration)
    - [Cronjobs Enumeration](03-Privilege%20Escalation/README.md#cronjobs-enumeration)

## Tunneling and Pivoting
- **[Port Forwarding](04-Tunneling%20and%20Pivoting/README.md#port-forwarding)**
  - **[Simple Port Forwarding](04-Tunneling%20and%20Pivoting/README.md#simple-port-forwarding)**
    - [SSH](04-Tunneling%20and%20Pivoting/README.md#ssh)
    - [Socat](04-Tunneling%20and%20Pivoting/README.md#socat)
    - [Plink.exe (Windows only)](04-Tunneling%20and%20Pivoting/README.md#plinkexe-windows-only)
    - [Netsh.exe (Windows only)](04-Tunneling%20and%20Pivoting/README.md#netshexe-windows-only)

  - **[Dynamic Port Forwarding](04-Tunneling%20and%20Pivoting/README.md#dynamic-port-forwarding)**
    - [SSH](04-Tunneling%20and%20Pivoting/README.md#ssh-1)
    - [Chisel](04-Tunneling%20and%20Pivoting/README.md#chisel)
    - [SSHUTTLE](04-Tunneling%20and%20Pivoting/README.md#sshuttle)
    - [Ligolo-ng](04-Tunneling%20and%20Pivoting/README.md#ligolo-ng)
## Active Directory and Lateral Movement
- **[Enumeration](05-Active-Directory%20and%20Lateral%20Movement/README.md#enumeration)**
  - [PowerView](05-Active-Directory%20and%20Lateral%20Movement/README.md#powerview)
  - [SharpHound](05-Active-Directory%20and%20Lateral%20Movement/README.md#sharphound)
  - [LDAP](05-Active-Directory%20and%20Lateral%20Movement/README.md#ldap)

- **[Authentication Attacks](05-Active-Directory%20and%20Lateral%20Movement/README.md#authentication-attacks)**
  - [Password-Spraying](05-Active-Directory%20and%20Lateral%20Movement/README.md#password-spraying)
    - [Linux](05-Active-Directory%20and%20Lateral%20Movement/README.md#linux)
    - [Windows](05-Active-Directory%20and%20Lateral%20Movement/README.md#windows)

- **[Kerberos](05-Active-Directory%20and%20Lateral%20Movement/README.md#kerberos)**
  - [Kerberos Clock Skew Fix](05-Active-Directory%20and%20Lateral%20Movement/README.md#kerberos-clock-skew-fix)
  - [Kerberoasting](05-Active-Directory%20and%20Lateral%20Movement/README.md#kerberoasting)
  - [AS-REP Roasting](05-Active-Directory%20and%20Lateral%20Movement/README.md#as-rep-roasting)
  - [Ticket Attacks](05-Active-Directory%20and%20Lateral%20Movement/README.md#ticket-attacks)

- **[bloodyAD](05-Active-Directory%20and%20Lateral%20Movement/README.md#bloodyad)**
- **[NetExec](05-Active-Directory%20and%20Lateral%20Movement/README.md#netexec)**

## Tools

Collection of tools and binaries used during my OSCP preparation.

[Browse Tools](06-Tools/)

## Shells

Collection of reverse shell scripts and payloads.

[Browse Shells](07-Shells/)
