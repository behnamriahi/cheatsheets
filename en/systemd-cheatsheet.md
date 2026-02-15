````markdown
# Systemd Cheatsheet

## Service Management
```bash
systemctl start <service>               # Start service
systemctl stop <service>                # Stop service
systemctl restart <service>             # Restart service
systemctl reload <service>              # Reload service configuration
systemctl status <service>              # Display service status
systemctl enable <service>              # Enable service on boot
systemctl disable <service>             # Disable service on boot
systemctl is-enabled <service>          # Check if service is enabled
systemctl is-active <service>           # Check if service is active
systemctl is-failed <service>           # Check if service has failed
```

## List Services
```bash
systemctl list-units                    # List all active units
systemctl list-units --type=service     # List all services
systemctl list-units --all              # List all units (active + inactive)
systemctl list-unit-files               # List all unit files
systemctl list-unit-files --type=service # List all service files
systemctl list-dependencies <service>   # Display service dependencies
systemctl list-sockets                  # List all sockets
systemctl list-timers                   # List all timers
```

## System State
```bash
systemctl reboot                        # Reboot system
systemctl poweroff                      # Power off system
systemctl suspend                       # Suspend mode
systemctl hibernate                     # Hibernate mode
systemctl hybrid-sleep                  # Hybrid sleep
systemctl get-default                   # Display default target
systemctl set-default multi-user.target # Set default target
systemctl isolate multi-user.target     # Change to target
```

## Service Files
Service file locations:
- `/etc/systemd/system/` - System services
- `/usr/lib/systemd/system/` - Distribution services
- `~/.config/systemd/user/` - User services

Basic service file structure:
```ini
[Unit]
Description=My Service
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/myapp
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## Service Types
```ini
Type=simple         # Default, main process
Type=forking        # Process forks, parent exits
Type=oneshot        # Short-lived process
Type=notify         # Service sends notification when ready
Type=dbus           # Service is ready when it receives D-Bus name
Type=idle           # Delay until other jobs finish
```

## Restart Options
```ini
Restart=no              # Never restart (default)
Restart=on-success      # Only on clean exit
Restart=on-failure      # On failure
Restart=on-abnormal     # On abnormal exit
Restart=on-watchdog     # On watchdog timeout
Restart=on-abort        # On unclean signal
Restart=always          # Always restart
```

## Journal (Logs)
```bash
journalctl                              # Display all logs
journalctl -u <service>                 # Display specific service logs
journalctl -f                           # Follow logs (tail -f)
journalctl -u <service> -f              # Follow service logs
journalctl -b                           # Logs from current boot
journalctl -b -1                        # Logs from previous boot
journalctl --since "2024-01-01"         # Logs since date
journalctl --since "1 hour ago"         # Logs from past hour
journalctl --since today                # Today's logs
journalctl --until "2024-01-31"         # Logs until date
journalctl -p err                       # Errors only
journalctl -p warning                   # Warnings and above
journalctl -n 50                        # Display last 50 lines
journalctl --disk-usage                 # Display journal disk usage
journalctl --vacuum-size=100M           # Limit journal size
journalctl --vacuum-time=2weeks         # Keep only 2 weeks
journalctl -o json-pretty               # JSON output
```

## Priority Levels
```
0: emerg        # Emergency
1: alert        # Alert
2: crit         # Critical
3: err          # Error
4: warning      # Warning
5: notice       # Notice
6: info         # Information
7: debug        # Debug
```

## Unit File Management
```bash
systemctl daemon-reload                 # Reload systemd configuration
systemctl edit <service>                # Edit service override
systemctl edit --full <service>         # Edit complete service file
systemctl cat <service>                 # Display service file content
systemctl show <service>                # Display all properties
systemctl mask <service>                # Completely disable service (prevent start)
systemctl unmask <service>              # Re-enable service
```

## Targets (Runlevels)
```bash
systemctl get-default                   # Display default target
systemctl set-default multi-user.target # Set default target
systemctl list-units --type=target      # List available targets
systemctl isolate rescue.target         # Change to rescue mode
systemctl isolate multi-user.target     # Change to multi-user
systemctl isolate graphical.target      # Change to graphical mode
```

Common targets:
- `poweroff.target` - Power off
- `rescue.target` - Single user mode
- `multi-user.target` - Multi-user (no GUI)
- `graphical.target` - Multi-user with GUI
- `reboot.target` - Reboot

## Timers
```bash
systemctl list-timers                   # List all timers
systemctl list-timers --all             # List all timers (active + inactive)
systemctl start <timer>                 # Start timer
systemctl enable <timer>                # Enable timer on boot
```

Example timer file (`backup.timer`):
```ini
[Unit]
Description=Backup Timer

[Timer]
OnCalendar=daily
OnCalendar=*-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

## User Services
```bash
systemctl --user start <service>        # Start user service
systemctl --user enable <service>       # Enable user service
systemctl --user status <service>       # User service status
journalctl --user -u <service>          # User service logs
loginctl enable-linger <user>           # Enable user services without login
```

## Boot Time Analysis
```bash
systemd-analyze                         # Display boot time
systemd-analyze blame                   # Display each service time
systemd-analyze critical-chain          # Display critical chain
systemd-analyze plot > boot.svg         # Create boot plot
systemd-analyze verify <service>        # Verify unit file correctness
```

## Resource Control (cgroups)
```ini
[Service]
CPUQuota=50%                            # Limit CPU to 50%
MemoryLimit=1G                          # Limit memory to 1GB
TasksMax=100                            # Limit number of tasks
IOWeight=500                            # IO priority
```

```bash
systemctl show <service> -p CPUQuota    # Display CPU quota
systemctl set-property <service> CPUQuota=50% # Set CPU quota
```

## Environment Variables
```ini
[Service]
Environment="VAR1=value1"
Environment="VAR2=value2"
EnvironmentFile=/etc/myapp/env          # Load from file
```

## Socket Activation
Socket file (`myapp.socket`):
```ini
[Unit]
Description=My App Socket

[Socket]
ListenStream=8080

[Install]
WantedBy=sockets.target
```

## Service Dependencies
```ini
[Unit]
Requires=network.target                 # Hard dependency
Wants=network.target                    # Soft dependency
After=network.target                    # Start after
Before=multi-user.target                # Start before
Conflicts=other.service                 # Cannot run simultaneously
```

## Common Service Patterns

### Simple Daemon
```ini
[Unit]
Description=My Daemon
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/mydaemon
Restart=always

[Install]
WantedBy=multi-user.target
```

### Forking Daemon
```ini
[Service]
Type=forking
PIDFile=/var/run/mydaemon.pid
ExecStart=/usr/bin/mydaemon --daemon
```

### One-shot Script
```ini
[Service]
Type=oneshot
ExecStart=/usr/local/bin/myscript.sh
RemainAfterExit=yes
```

````
