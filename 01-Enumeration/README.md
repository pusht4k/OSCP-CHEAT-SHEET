# Enumeration

## Nmap

### Regular Scanning

```bash
# Single IP scan
nmap TARGET_IP

# Scan Multiple Hosts
nmap <IP-1> <IP-2>
or
nmap --iL hosts.txt

# Scan All Ports
nmap -p- TARGET_IP

# Scan a specific port
nmap -p 80 TARGET_IP

# Scan specific ports
nmap -p 80,443 TARGET_IP

# Target Network Range
nmap 10.10.10.0/24
```
### Scanning Techniques

```bash
# TCP SYN Port Scan (Default)
nmap -sS TARGET_IP

# TCP Connect Port Scan (Default without root privilege)
nmap -sT TARGET_IP

# UDP Port Scan
nmap -sU TARGET_IP

# Faster UDP Port scan
nmap -sU TARGET_IP --min-rate 1000 --max-retries 5

# Find alive hosts
nmap -sn <SUBNET>
```
### Aggressive Scanning

```bash
# Aggressive Scanning (OS, Version detection, Script scanning, traceroute)
nmap -A TARGET_IP

# Version Detection
nmap -sV TARGET_IP

# Default Scripts  Scan
nmap -sC TARGET_IP

# OS Detection
nmap -O TARGET_IP
```
### NSE Scripts
```bash
# Scan with a single script
nmap TARGET_IP --script=BANNER

# Scan with script arguments
nmap TARGET_IP --script=BANNER --script-args <ARGS>
```
### Firewall Evasion
```bash
# Decoys - Send scans from spoofed IPs
nmap TARGET_IP -D 10.10.10.1, 10.10.10.2, 10.10.10.3

# Decoys - Random (RND:QUANTITY)
nmap TARGET_IP -D RND:5

# Use Different Source IP
nmap TARGET_IP -S 10.10.10.1

# Set offset size (8, 16, 32, 64)
nmap TARGET_IP --mtu 32

# SYN Scan from DNS Port
nmap TARGET_IP --source-port 53
```
### Output
```bash
# Normal output
nmap TARGET_IP -oN nmap-scan.txt

# Output in all formats
nmap TARGET_IP -oA nmap-scan
```

## Footprinting
### FTP
```bash
# Anonymous login
ftp anonymous@TARGET_IP

# List Files
ftp> ls

# Recursive Listing
ftp> ls -R

# List hidden files
ftp> ls -la

# Download File
ftp> get FILE_NAME

#Upload File
ftp> put FILE_NAME
```

### SMB
#### Null Session
```bash
# SMBmap null session
smbmap -H TARGET_IP -u ''

# Netexec null session
nxc smb TARGET_IP -u '' -p '' --shares

# rpcclient
rpcclient -U "" TARGET_IP

# smbclient
smbclient -N -L TARGET_IP
```
#### SMB Enumeration
##### Enum4linux
```bash
#Enumerate users
enum4linux TARGET_IP -U
```
##### SMBClient
```bash
# Connect
smbclient //TARGET_IP/share -U USERNAME

# List Files
smb: \> ls

# Move Directories
smb: \> cd PATH

# Download Files
smb: \> get FILE_NAME

# Upload Files (Need WRITE permission)
smb :\> put FILE_NAME
```
##### RPCClient
```bash
# Connect
rpcclient -U USER TARGET_IP

# Enumerate Domains
rpcclient $> enumdomains

# Enumerate Shares
rpcclient $> netshareenumall

# Enumerate Users
rpcclient $> enumdomusers

# Query User
rpcclient $> queryuser <RID>
```
##### Netexec
```bash
# List Shares
nxc smb TARGET_IP -u USER -p PASS --shares

# File Enumeration
nxc smb TARGET_IP -u USER -p PASS --share SHARE_NAME -M spider_plus

# User Enumeration
nxc smb TARGET_IP -u USER -p PASS --users

# Rid Brute
nxc smb TARGET_IP -u USER -p PASS --rid-brute
```
### NFS
```bash
# Show Available NFS Shares
showmount -e TARGET_IP

# Mount NFS Share
mkdir target-NFS
mount -t TARGET_IP:/share-name ./target-NFS
```
### DNS
```bash
# NS Query
dig ns DOMAIN_NAME @TARGET_IP

# Version Query
dig CH TXT version.bind TARGET_IP

# View All Records
dig any DOMAIN_NAME @TARGET_IP

# Zone Transfer
dig axfr DOMAIN_NAME @TARGET_IP

# Internal Zone Transfer
dig axfr INTERNAL_DOMAIN @TARGET_IP

# using DNSEnum
dnsenum DOMAIN_NAME

# using DNSRecon
dnsrecon -d DOMAIN_NAME -t SCAN_TYPE -D LIST_NAME
```
### SMTP
```bash
# Connection with telnet
telnet TARGET_IP 25

# Default nmap scripts
nmap -sC -sV -p 25 TARGET_IP

# Nmap - Open Relay
nmap -p 25 --script smtp-open-relay TARGET_IP
```
### IMAP / POP3
```bash
# Nmap Footprinting
nmap TARGET_IP -sV -sC -p 110,143,993,995

# cURL connection
curl -k 'imaps://TARGET_IP' --user USER:PASS

# OpenSSL POP3s connection
openssl s_client -connect TARGET_IP:pop3s

# OpenSSL IMAPs connection
openssl s_client -connect TARGET_IP:imaps
```
#### IMAP Commands
```bash
# User Login
1 LOGIN USER PASS

# List all directories
1 LIST "" *

# Creates a mailbox
1 CREATE "MAILBOX_NAME"

# Deletes a mailbox
1 DELETE "MAILBOX_NAME"

# Renames a mailbox
1 RENAME "MAILBOX_NAME" "NEW_NAME"

# Select a mailbox
1 SELECT "MAILBOX_NAME"

# Exits selected mailbox
1 UNSELECT "MAILBOX_NAME"

# Retrieves data associated with a message in the mailbox
1 FETCH <ID> all

# Removes all messages with the Deleted flag set
1 CLOSE

# Closes the connection
1 LOGOUT
```
#### POP3 Commands
```bash
# Identifies the user
USER username

# Authentication of the user using its password
PASS password

# Request number of saved emails
STAT

# Request the number and size of all emails
LIST

# Requests the email by ID
RETR id

# Delete email by ID
DELE id

# Request to display capabilities
CAPA

# Request to reset the transmitted information
RSET

# Closes connection
QUIT
```

### SNMP
```bash
# Check if the port is open
nmap -sU -p 161 TARGET_IP

# Footprint through snmpwalk
snmpwalk -v2c -c <community string> TARGET_IP

# OneSixtyOne community string identification
onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt TARGET_IP

# braa Enumeration
braa <community string>@TARGET_IP:.1.3.6.*

# Enumerate deeper with snmpwalk
snmpwalk -v2c -c <community string> -Oa TARGET_IP NET-SNMP-EXTEND-MIB::nsExtendObjects
```

### MySQL
```bash
# Scanning MySQL
nmap TARGET_IP -sV -sC -p 3306 --script mysql*

# Connect to MySQL
mysql -u 'USER' -p'PASS' -h TARGET_IP

# Show all databases
mysql> show databases;

# Select Database
mysql> use DATABASE_NAME;

# Show all available tables within current database
mysql> show tables;

# Show all columns from a selected table:
mysql> show columns from TABLE_NAME;

# Show everything from a desired table
select * from TABLE_NAME

# Search for specific string in a desired table
select * from TABLE_NAME where COLUMN_NAME = "STRING";
```
### MSSQL
```bash
# Connecting with Impacket-mssqlclient
impacket-mssqlclient USER@TARGET_IP -windows-auth
```

### PostgresQL
```bash
psql -U username -h HOST
```
### Oracle TNS
```bash
# Footprinting
nmap -p 1521 -sV TARGET_IP --open

# Nmap - SID Bruteforcing
nmap -p 1521 -sV TARGET_IP --open --script oracle-sid-brute

# odat enumeration
odat all -s TARGET_IP

# SQLplus Log In
sqlpluss USER/PASS@TARGET_IP/SID

# List all tables in current database
SQL> select table_name from all_tables;

# Show current user privileges
SQL> select * from user_role_privs;
```

## Web Enumeration
### Basic Enumeration
- Browse to Web Application URL
- Use Wappalyzer to identify Application/Version
- View Source Page
- View Network Inspection
- Intercept requests with Burp to find GET/POST requests
- Check for robots.txt file

```bash
# Misconfiguration enumeration with nikto
nikto -h TARGET_IP
```
### Virtual Host Enumeration
```bash
# gobuster vHost enumeration
gobuster vhost -u http://DOMAIN.NAME -w /PATH/TO/WORDLIST --append-domain

# ffuf vHost enumeration
ffuf -w /PATH/TO/WORDLIST:FUZZ -u http://DOMAIN.NAME -H "Host: FUZZ.DOMAIN.NAME"
```

### Directory Brute Forcing
```bash
# gobuster
gobuster dir -u http://TARGET_IP:PORT -w /PATH/TO/WORDLIST -x FILE_EXTENSION

# ffuf
ffuf -w /PATH/TO/WORDLIST:FUZZ -u http://TARGET_IP:PORT/FUZZ -e .FILE_EXTENSION -fs FALSE_RESPONSE_SIZE

# Recursive scan with ffuf
ffuf -w /PATH/TO/WORDLIST:FUZZ -u http://TARGET_IP:PORT/FUZZ -e .FILE_EXTENSION -recursion -ic -fs FALSE_RESPONSE_SIZE
```
