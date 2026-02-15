# 🌐 Networking Cheatsheet

## 🔍 Network Information

```bash
# Show IP addresses
ip addr show                           # Show all network interfaces
ip a                                   # Short form
ifconfig                               # Legacy command

# Show specific interface
ip addr show eth0
ifconfig eth0

# Show routing table
ip route show
route -n                               # Legacy command
netstat -rn                            # Alternative

# Show default gateway
ip route | grep default
route -n | grep '^0.0.0.0'

# Show ARP table
ip neigh                               # Show ARP cache
arp -a                                 # Legacy command
```

---

## 🔌 Network Configuration

```bash
# Assign IP address
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0

# Remove IP address
sudo ip addr del 192.168.1.100/24 dev eth0

# Enable/disable interface
sudo ip link set eth0 up
sudo ip link set eth0 down
sudo ifconfig eth0 up
sudo ifconfig eth0 down

# Set default gateway
sudo ip route add default via 192.168.1.1
sudo route add default gw 192.168.1.1

# Set static route
sudo ip route add 10.0.0.0/24 via 192.168.1.254
sudo route add -net 10.0.0.0/24 gw 192.168.1.254

# Delete route
sudo ip route del 10.0.0.0/24
sudo route del -net 10.0.0.0/24

# Change MAC address
sudo ip link set dev eth0 address 00:11:22:33:44:55
```

---

## 📡 DNS Tools

```bash
# DNS lookup
nslookup google.com                    # Basic DNS query
nslookup google.com 8.8.8.8            # Query specific DNS server

# Dig command (detailed DNS info)
dig google.com                         # Query A record
dig google.com MX                      # Query MX records
dig google.com NS                      # Query nameservers
dig google.com +short                  # Short output
dig @8.8.8.8 google.com                # Use specific DNS server
dig -x 8.8.8.8                         # Reverse DNS lookup

# Host command
host google.com                        # Simple DNS lookup
host -t MX google.com                  # Query MX records
host -a google.com                     # All records

# Resolve hostname to IP
getent hosts google.com
```

---

## 🔐 Port and Service Checking

```bash
# Show listening ports
netstat -tuln                          # TCP/UDP listening ports
ss -tuln                               # Modern alternative to netstat
lsof -i                                # List open network connections

# Show all connections
netstat -tun                           # All TCP/UDP connections
ss -tun                                # Modern alternative

# Show connections for specific port
netstat -tuln | grep :80
ss -tuln | grep :80
lsof -i :80

# Show process using port
sudo lsof -i :80
sudo netstat -tulnp | grep :80
sudo ss -tulnp | grep :80

# Check if port is open
nc -zv hostname 80                     # Netcat port check
telnet hostname 80                     # Telnet check
timeout 1 bash -c "echo > /dev/tcp/hostname/80" && echo "open" || echo "closed"
```

---

## 🌐 Connectivity Testing

```bash
# Ping
ping google.com                        # Continuous ping
ping -c 4 google.com                   # Send 4 packets
ping -i 0.5 google.com                 # Set interval to 0.5 seconds
ping -W 2 google.com                   # Set timeout to 2 seconds

# Traceroute
traceroute google.com                  # Trace route to host
traceroute -n google.com               # No DNS resolution
tracepath google.com                   # Alternative to traceroute
mtr google.com                         # Continuous traceroute

# Test TCP connection
nc -vz hostname 80                     # Netcat TCP check
telnet hostname 80                     # Telnet TCP check

# Test UDP connection
nc -vzu hostname 53                    # Netcat UDP check

# Curl connectivity
curl -I https://google.com             # Get headers only
curl -v https://google.com             # Verbose output
curl -o /dev/null -s -w "%{http_code}\n" https://google.com  # HTTP status code
```

---

## 📊 Network Statistics

```bash
# Show network statistics
netstat -s                             # All statistics
ss -s                                  # Summary statistics

# Show interface statistics
ip -s link                             # Interface statistics
netstat -i                             # Interface table

# Monitor bandwidth
iftop                                  # Interactive bandwidth monitor
iftop -i eth0                          # Specific interface
nethogs                                # Per-process bandwidth
vnstat                                 # Network traffic monitor
nload                                  # Real-time traffic

# Show TCP connections
ss -t                                  # TCP connections
netstat -t                             # Legacy command

# Show UDP connections
ss -u                                  # UDP connections
netstat -u                             # Legacy command
```

---

## 🔥 Firewall (iptables)

```bash
# List rules
sudo iptables -L                       # List all rules
sudo iptables -L -n                    # No DNS resolution
sudo iptables -L -v                    # Verbose output
sudo iptables -L INPUT                 # List INPUT chain

# Allow incoming port
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Block IP address
sudo iptables -A INPUT -s 192.168.1.100 -j DROP
sudo iptables -A INPUT -s 10.0.0.0/8 -j DROP

# Allow from specific IP
sudo iptables -A INPUT -s 192.168.1.50 -j ACCEPT

# Delete rule
sudo iptables -D INPUT 3               # Delete rule number 3
sudo iptables -D INPUT -s 192.168.1.100 -j DROP  # Delete specific rule

# Flush all rules
sudo iptables -F                       # Flush all chains
sudo iptables -F INPUT                 # Flush INPUT chain

# Save rules
sudo iptables-save > /etc/iptables/rules.v4
sudo service iptables save             # On RHEL/CentOS

# Restore rules
sudo iptables-restore < /etc/iptables/rules.v4
```

---

## 🔥 Firewall (firewalld)

```bash
# Check status
sudo firewall-cmd --state
sudo systemctl status firewalld

# List all rules
sudo firewall-cmd --list-all
sudo firewall-cmd --list-all --zone=public

# Add port
sudo firewall-cmd --add-port=80/tcp
sudo firewall-cmd --add-port=80/tcp --permanent

# Remove port
sudo firewall-cmd --remove-port=80/tcp
sudo firewall-cmd --remove-port=80/tcp --permanent

# Add service
sudo firewall-cmd --add-service=http
sudo firewall-cmd --add-service=https --permanent

# Block IP
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.100" reject'

# Reload firewall
sudo firewall-cmd --reload

# List services
sudo firewall-cmd --get-services
```

---

## 🛡️ Firewall (UFW - Ubuntu)

```bash
# Enable/disable UFW
sudo ufw enable
sudo ufw disable

# Check status
sudo ufw status
sudo ufw status verbose
sudo ufw status numbered

# Allow port
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 22                      # SSH

# Deny port
sudo ufw deny 80/tcp

# Allow from specific IP
sudo ufw allow from 192.168.1.100
sudo ufw allow from 192.168.1.0/24 to any port 22

# Delete rule
sudo ufw delete allow 80/tcp
sudo ufw delete 2                      # Delete rule number 2

# Reset firewall
sudo ufw reset

# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

---

## 📦 Network Packet Capture

```bash
# tcpdump
sudo tcpdump -i eth0                   # Capture on interface
sudo tcpdump -i any                    # Capture on all interfaces
sudo tcpdump -i eth0 port 80           # Specific port
sudo tcpdump -i eth0 host 192.168.1.1  # Specific host
sudo tcpdump -i eth0 -w capture.pcap   # Save to file
sudo tcpdump -r capture.pcap           # Read from file
sudo tcpdump -i eth0 -n                # No DNS resolution
sudo tcpdump -i eth0 -c 100            # Capture 100 packets

# Advanced filters
sudo tcpdump -i eth0 'tcp port 80 and (src host 192.168.1.1)'
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'
```

---

## 🔧 Network Tools

```bash
# Netcat (nc)
nc -l 8080                             # Listen on port 8080
nc hostname 80                         # Connect to host port 80
nc -u hostname 53                      # UDP connection
echo "test" | nc hostname 80           # Send data

# Socat
socat TCP-LISTEN:8080,fork TCP:backend:80  # TCP proxy
socat - TCP:hostname:80                # Connect to TCP port

# wget
wget https://example.com/file.zip      # Download file
wget -c https://example.com/file.zip   # Continue download
wget -r https://example.com            # Recursive download
wget -O output.txt https://example.com # Save with custom name

# curl
curl https://example.com               # GET request
curl -o file.html https://example.com  # Save to file
curl -L https://example.com            # Follow redirects
curl -X POST https://api.example.com   # POST request
curl -d "key=value" https://api.example.com  # POST with data
curl -H "Content-Type: application/json" https://api.example.com  # Custom header
```

---

## 📡 Wi-Fi Commands

```bash
# iwconfig (wireless config)
iwconfig                               # Show wireless info
iwconfig wlan0                         # Specific interface
sudo iwconfig wlan0 essid "NetworkName"  # Connect to network

# iw (modern wireless tools)
iw dev                                 # List wireless devices
iw dev wlan0 scan                      # Scan for networks
iw dev wlan0 link                      # Show connection info
iw dev wlan0 connect "NetworkName"     # Connect to network

# nmcli (NetworkManager)
nmcli device                           # List devices
nmcli device wifi list                 # List Wi-Fi networks
nmcli device wifi connect "SSID" password "password"  # Connect
nmcli connection show                  # Show connections
nmcli connection up "connection-name"  # Activate connection
nmcli connection down "connection-name"  # Deactivate connection
```

---

## 🔍 Network Troubleshooting

```bash
# Check if interface is up
ip link show eth0 | grep "state UP"

# Check network cable
ethtool eth0 | grep "Link detected"

# Check DNS resolution
systemd-resolve --status               # systemd-resolved status
cat /etc/resolv.conf                   # DNS servers

# Check network service
sudo systemctl status NetworkManager
sudo systemctl status networking

# Restart network service
sudo systemctl restart NetworkManager
sudo systemctl restart networking
sudo /etc/init.d/networking restart

# Flush DNS cache
sudo systemd-resolve --flush-caches
sudo systemctl restart nscd            # NSCD cache
sudo systemctl restart dnsmasq         # Dnsmasq cache

# Test network speed
speedtest-cli                          # CLI speedtest
iperf3 -s                              # Start server
iperf3 -c server_ip                    # Connect as client
```

---

## 💡 Best Practices

- Always use specific interface names in production scripts
- Use `-n` flag to avoid DNS lookups for faster results
- Prefer `ip` and `ss` over deprecated `ifconfig` and `netstat`
- Save firewall rules to persist after reboot
- Use connection tracking for stateful firewalls
- Monitor network traffic regularly for anomalies
- Document all firewall rules and network changes
- Use VLANs and network segmentation for security
- Implement rate limiting to prevent DDoS attacks
- Regular security audits of open ports and services
