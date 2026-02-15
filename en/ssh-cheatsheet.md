# SSH Cheatsheet

## Basic Connection
```bash
ssh user@hostname                       # Connect to remote host
ssh user@hostname -p 2222               # Connect on custom port
ssh -i /path/to/key user@hostname       # Connect with specific key
ssh user@hostname 'command'             # Execute command on remote host
```

## SSH Keys
```bash
ssh-keygen                              # Generate new SSH key pair
ssh-keygen -t rsa -b 4096               # Generate RSA key with 4096 bits
ssh-keygen -t ed25519                   # Generate Ed25519 key (recommended)
ssh-keygen -t rsa -b 4096 -C "email@example.com" # With comment
ssh-copy-id user@hostname               # Copy public key to remote host
ssh-copy-id -i ~/.ssh/id_rsa.pub user@hostname # Copy specific key
ssh-add ~/.ssh/id_rsa                   # Add key to SSH agent
ssh-add -l                              # List keys in SSH agent
ssh-add -D                              # Remove all keys from agent
```

## SSH Config File (~/.ssh/config)
```
Host myserver
    HostName example.com
    User myuser
    Port 2222
    IdentityFile ~/.ssh/id_rsa
    
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

Then connect with: `ssh myserver`

## File Transfer (SCP)
```bash
scp file.txt user@host:/path/          # Copy file to server
scp user@host:/path/file.txt .         # Copy file from server
scp -r directory user@host:/path/      # Copy directory recursively
scp -P 2222 file.txt user@host:/path/  # Use custom port
scp -i ~/.ssh/key file.txt user@host:/ # Use specific key
```

## File Transfer (SFTP)
```bash
sftp user@hostname                      # Start SFTP session
```

SFTP commands:
```
put file.txt                            # Upload file
get file.txt                            # Download file
put -r directory                        # Upload directory
get -r directory                        # Download directory
ls                                      # List remote files
lls                                     # List local files
cd /path                                # Change remote directory
lcd /path                               # Change local directory
pwd                                     # Show remote directory
lpwd                                    # Show local directory
mkdir dirname                           # Create remote directory
exit                                    # Exit SFTP
```

## Port Forwarding
```bash
# Local port forwarding (access remote service via local)
ssh -L 8080:localhost:80 user@host      # Forward local 8080 to remote 80

# Remote port forwarding (make local service accessible remotely)
ssh -R 8080:localhost:80 user@host      # Forward remote 8080 to local 80

# Dynamic forwarding (SOCKS proxy)
ssh -D 1080 user@host                   # Create SOCKS proxy on port 1080
```

## SSH Tunneling
```bash
ssh -L 3306:db.internal:3306 user@gateway # Access internal database via gateway
ssh -N -L 8080:localhost:80 user@host     # -N: Don't execute command
ssh -f -N -L 8080:localhost:80 user@host  # -f: Run in background
```

## X11 Forwarding
```bash
ssh -X user@hostname                    # Enable X11 forwarding
ssh -Y user@hostname                    # Enable trusted X11 forwarding
```

## SSH Agent
```bash
eval "$(ssh-agent -s)"                  # Start SSH agent
ssh-add                                 # Add default keys
ssh-add ~/.ssh/id_rsa                   # Add specific key
ssh-add -l                              # List loaded keys
ssh-add -D                              # Remove all keys from agent
killall ssh-agent                       # Stop SSH agent
```

## Security & Debugging
```bash
ssh -v user@hostname                    # Verbose mode (debugging)
ssh -vv user@hostname                   # More verbose
ssh -vvv user@hostname                  # Maximum verbosity
ssh -o "StrictHostKeyChecking=no" user@host # Disable host key checking (not recommended)
ssh-keyscan hostname                    # Get host key
```

## SSH Session Management
```bash
# Inside SSH session:
~.                                      # Disconnect
~^Z                                     # Suspend connection
~?                                      # Show escape commands
```

## Multiple Sessions
```bash
ssh -M -S /tmp/ssh_socket user@host     # Master connection
ssh -S /tmp/ssh_socket user@host        # Use existing connection
ssh -O check -S /tmp/ssh_socket user@host # Check master connection
ssh -O exit -S /tmp/ssh_socket user@host  # Close master connection
```

## SSH Configuration Options
```
Host example
    HostName example.com
    User myuser
    Port 22
    IdentityFile ~/.ssh/id_rsa
    ForwardAgent yes
    Compression yes
    ServerAliveInterval 60
    ServerAliveCountMax 3
    TCPKeepAlive yes
    ControlMaster auto
    ControlPath ~/.ssh/sockets/%r@%h:%p
    ControlPersist 10m
```

## File Permissions
```bash
chmod 700 ~/.ssh                        # SSH directory
chmod 600 ~/.ssh/id_rsa                 # Private key
chmod 644 ~/.ssh/id_rsa.pub             # Public key
chmod 644 ~/.ssh/authorized_keys        # Authorized keys
chmod 644 ~/.ssh/known_hosts            # Known hosts
chmod 600 ~/.ssh/config                 # Config file
```

## ProxyJump (Jump Host)
```bash
ssh -J jumphost user@finalhost          # Connect via jump host
ssh -J user1@host1,user2@host2 user@final # Multiple jumps
```

Or in config file:
```
Host final
    HostName finalhost.com
    User myuser
    ProxyJump jumphost
```

## Common Issues
```bash
ssh-keygen -R hostname                  # Remove host from known_hosts
ssh-keygen -f ~/.ssh/known_hosts -R hostname # Same as above
eval "$(ssh-agent -s)" && ssh-add       # Fix agent issues
```

## Rsync over SSH
```bash
rsync -avz -e ssh source/ user@host:/dest/ # Sync with SSH
rsync -avz --progress -e "ssh -p 2222" source/ user@host:/dest/ # Custom port
```
