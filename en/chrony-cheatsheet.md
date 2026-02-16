# Chrony Cheatsheet

Chrony is an implementation of the Network Time Protocol (NTP) that synchronizes system clocks with high accuracy.

## Installation

```bash
# Debian/Ubuntu
sudo apt install chrony

# RHEL/CentOS/Fedora
sudo dnf install chrony

# Enable and start service
sudo systemctl enable chronyd
sudo systemctl start chronyd
```

## Configuration File

Main configuration file: `/etc/chrony/chrony.conf` or `/etc/chrony.conf`

### Common Configuration Options

#### Time Sources

```bash
# NTP servers
server 0.pool.ntp.org iburst
server 1.pool.ntp.org iburst
server 2.pool.ntp.org iburst
server 3.pool.ntp.org iburst

# Pool of servers
pool pool.ntp.org iburst maxsources 4

# Prefer specific server
server ntp.example.com iburst prefer
```

#### Access Control

```bash
# Allow NTP client access from local network
allow 192.168.1.0/24
allow 10.0.0.0/8

# Allow all (not recommended for security)
allow all

# Deny specific network
deny 192.168.2.0/24
```

#### Local Time Source

```bash
# Act as a stratum 10 server when disconnected
local stratum 10

# More aggressive stratum level
local stratum 8
```

#### Drift and Correction

```bash
# File to store drift data
driftfile /var/lib/chrony/drift

# Make step adjustment if offset is greater than 1 second
makestep 1.0 3

# Real-time clock (RTC) synchronization
rtcsync
```

#### Logging

```bash
# Log directory
logdir /var/log/chrony

# Log measurements, statistics, tracking
log measurements statistics tracking
```

#### Network Settings

```bash
# Port number (default 123)
port 123

# Bind to specific interface
bindaddress 192.168.1.10

# Command port (default 323)
cmdport 323
```

## Chronyc Commands

`chronyc` is the command-line interface for chrony.

### Status and Monitoring

```bash
# Show current status
chronyc tracking

# Show time sources
chronyc sources

# Show time sources with verbose output
chronyc sources -v

# Show NTP sources details
chronyc sourcestats

# Show clients accessing the server
chronyc clients

# Show server and peer statistics
chronyc serverstats

# Check system time status
chronyc activity
```

### Time Adjustment

```bash
# Make immediate step adjustment
chronyc makestep

# Force synchronization
chronyc burst 4/4

# Manual time adjustment
chronyc manual on
chronyc settime HH:MM:SS
```

### Server Management

```bash
# Add new NTP server
chronyc add server ntp.example.com

# Delete server
chronyc delete ntp.example.com

# Online/Offline mode
chronyc online
chronyc offline

# Dump all measurements
chronyc dump
```

### Source Management

```bash
# Show source selection
chronyc sources -v

# Reselect source
chronyc reselect

# Show NTP data
chronyc ntpdata
```

## Service Management

```bash
# Start service
sudo systemctl start chronyd

# Stop service
sudo systemctl stop chronyd

# Restart service
sudo systemctl restart chronyd

# Check service status
sudo systemctl status chronyd

# Enable on boot
sudo systemctl enable chronyd

# Disable on boot
sudo systemctl disable chronyd

# Reload configuration
sudo systemctl reload chronyd
```

## Chronyc Sources Output Explained

```bash
chronyc sources
```

Output columns:
- `M` - Mode: `^` = server, `=` = peer, `#` = local reference clock
- `S` - State: `*` = current synced, `+` = combined, `-` = not combined, `?` = unreachable
- `Name/IP` - Hostname or IP address
- `Stratum` - Distance from reference clock
- `Poll` - Polling interval (log2 seconds)
- `Reach` - Reachability register (octal)
- `LastRx` - Time since last packet received
- `Last sample` - Offset, estimated error

## Chronyc Tracking Output

```bash
chronyc tracking
```

Shows:
- Reference ID and name
- Stratum level
- System time offset
- Root delay and dispersion
- Frequency adjustment
- Last update time

## Troubleshooting

```bash
# Check if chronyd is running
systemctl status chronyd

# Verify time synchronization
chronyc tracking

# Check which sources are reachable
chronyc sources

# View chrony logs
sudo journalctl -u chronyd

# Check for errors
sudo journalctl -u chronyd -p err

# Force immediate sync
sudo chronyc -a makestep
```

## Best Practices

1. **Use multiple time sources** - Configure at least 3-4 NTP servers
2. **Use iburst option** - Speeds up initial synchronization
3. **Secure access** - Use `allow`/`deny` to restrict client access
4. **Enable RTC sync** - Use `rtcsync` to keep hardware clock synchronized
5. **Monitor regularly** - Check `chronyc tracking` periodically
6. **Use pool** - Prefer pool directive for better reliability
7. **Configure makestep** - Allow step adjustments for large time differences

## Example Configuration

```bash
# /etc/chrony/chrony.conf

# NTP servers
pool 2.debian.pool.ntp.org iburst
pool ntp.ubuntu.com iburst maxsources 2

# Local reference if disconnected
local stratum 10

# Allow local network
allow 192.168.1.0/24

# Drift file
driftfile /var/lib/chrony/drift

# RTC synchronization
rtcsync

# Make step if offset > 1 second in first 3 updates
makestep 1.0 3

# Log directory
logdir /var/log/chrony
```

## Quick Reference

| Command | Description |
|---------|-------------|
| `chronyc tracking` | Show current time synchronization status |
| `chronyc sources` | List time sources |
| `chronyc sources -v` | Detailed source information |
| `chronyc sourcestats` | Show source statistics |
| `chronyc clients` | Show clients accessing this server |
| `chronyc makestep` | Make immediate time adjustment |
| `chronyc activity` | Show server activity |
| `systemctl restart chronyd` | Restart chrony service |

---

*For more information, see `man chrony.conf` and `man chronyc`*
