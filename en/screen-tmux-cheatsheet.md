````markdown
# Screen & Tmux Cheatsheet

## Screen

### Basic Commands
```bash
screen                          # Start new session
screen -S name                  # Start session with name
screen -ls                      # List sessions
screen -r                       # Reattach to session
screen -r name                  # Reattach to session by name
screen -d                       # Detach session
screen -d -r                    # Detach and reattach
screen -X quit                  # Quit session
```

### Screen Keys (Prefix Ctrl+a)
```
Ctrl+a ?        # Show help
Ctrl+a d        # Detach from session
Ctrl+a D D      # Detach and logout
Ctrl+a c        # Create new window
Ctrl+a n        # Next window
Ctrl+a p        # Previous window
Ctrl+a 0-9      # Switch to window 0-9
Ctrl+a "        # List all windows
Ctrl+a '        # Prompt for window number/name
Ctrl+a A        # Rename current window
Ctrl+a k        # Kill current window
Ctrl+a \        # Kill all windows and terminate
Ctrl+a [        # Enter copy mode
Ctrl+a ]        # Paste buffer
Ctrl+a S        # Split horizontally
Ctrl+a |        # Split vertically
Ctrl+a Tab      # Switch to next region
Ctrl+a X        # Close current region
Ctrl+a Q        # Close all regions except current
```

### Screen Copy Mode
```
Ctrl+a [        # Enter copy mode
Space           # Start selection
Enter           # End selection and copy
Ctrl+a ]        # Paste
```

---

## Tmux

### Session Management
```bash
tmux                            # Start new session
tmux new -s name                # Start session with name
tmux ls                         # List sessions
tmux list-sessions              # List sessions
tmux attach                     # Attach to last session
tmux attach -t name             # Attach to session by name
tmux a -t name                  # Short form
tmux kill-session -t name       # Kill session by name
tmux kill-server                # Kill all sessions
```

### Tmux Keys (Prefix Ctrl+b)

#### Session Commands
```
Ctrl+b s        # List sessions
Ctrl+b $        # Rename session
Ctrl+b d        # Detach from session
Ctrl+b (        # Previous session
Ctrl+b )        # Next session
```

#### Window Commands
```
Ctrl+b c        # Create new window
Ctrl+b ,        # Rename current window
Ctrl+b &        # Kill current window
Ctrl+b w        # List windows
Ctrl+b n        # Next window
Ctrl+b p        # Previous window
Ctrl+b 0-9      # Switch to window 0-9
Ctrl+b l        # Last window
Ctrl+b f        # Find window
```

#### Pane Commands
```
Ctrl+b %        # Split vertically
Ctrl+b "        # Split horizontally
Ctrl+b o        # Go to next pane
Ctrl+b ;        # Go to last active pane
Ctrl+b x        # Kill current pane
Ctrl+b q        # Show pane numbers
Ctrl+b q 0-9    # Switch to pane by number
Ctrl+b {        # Move pane left
Ctrl+b }        # Move pane right
Ctrl+b z        # Toggle pane zoom
Ctrl+b !        # Convert pane to window
Ctrl+b Ctrl+o   # Rotate panes
```

#### Pane Navigation
```
Ctrl+b ↑        # Move to pane above
Ctrl+b ↓        # Move to pane below
Ctrl+b ←        # Move to pane left
Ctrl+b →        # Move to pane right
```

#### Resize Pane
```
Ctrl+b :resize-pane -D 5    # Decrease size down
Ctrl+b :resize-pane -U 5    # Increase size up
Ctrl+b :resize-pane -L 5    # Decrease size left
Ctrl+b :resize-pane -R 5    # Increase size right
Ctrl+b Ctrl+↑   # Resize up
Ctrl+b Ctrl+↓   # Resize down
Ctrl+b Ctrl+←   # Resize left
Ctrl+b Ctrl+→   # Resize right
```

#### Copy Mode (Vi style)
```
Ctrl+b [        # Enter copy mode
q               # Exit copy mode
Space           # Start selection
Enter           # Copy selection
Ctrl+b ]        # Paste buffer
g               # Go to top
G               # Go to bottom
h,j,k,l         # Move cursor
w               # Next word
b               # Previous word
/               # Search forward
?               # Search backward
n               # Next search result
N               # Previous search result
```

### Tmux Command Mode
```
Ctrl+b :        # Enter command mode
:list-keys      # List all keys
:list-commands  # List all commands
:source-file ~/.tmux.conf # Reload configuration
```

### Useful Tmux Commands
```bash
# Session management
tmux new -s mysession           # New session with name
tmux attach -t mysession        # Attach to session
tmux switch -t mysession        # Switch to session
tmux rename-session -t old new  # Rename session
tmux kill-session -t mysession  # Kill session
tmux kill-session -a            # Kill all except current
tmux kill-session -a -t mysession # Kill all except named

# Window management
tmux new-window -n mywindow     # Create window with name
tmux select-window -t :0        # Select window by index
tmux rename-window newname      # Rename window
tmux kill-window -t :0          # Kill window

# Pane management
tmux split-window -h            # Horizontal split
tmux split-window -v            # Vertical split
tmux select-pane -t :.+         # Select next pane
tmux swap-pane -U               # Swap with previous pane
tmux swap-pane -D               # Swap with next pane
```

### Tmux Configuration (~/.tmux.conf)
```bash
# Change prefix to Ctrl+a
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# Enable mouse support
set -g mouse on

# Increase history limit
set -g history-limit 10000

# Start windows and panes from 1, not 0
set -g base-index 1
setw -g pane-base-index 1

# Vi mode
setw -g mode-keys vi

# Split panes with | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# Reload configuration
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Switch panes with Alt-arrow without prefix
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Don't auto-rename windows
set -g allow-rename off

# Status bar
set -g status-position bottom
set -g status-bg colour234
set -g status-fg colour137
```

## Comparison Table

| Feature | Screen | Tmux |
|---------|--------|------|
| Prefix | Ctrl+a | Ctrl+b |
| New window | Ctrl+a c | Ctrl+b c |
| Horizontal split | Ctrl+a S | Ctrl+b " |
| Vertical split | Ctrl+a \| | Ctrl+b % |
| Next window | Ctrl+a n | Ctrl+b n |
| Previous window | Ctrl+a p | Ctrl+b p |
| Detach | Ctrl+a d | Ctrl+b d |
| List windows | Ctrl+a " | Ctrl+b w |
| Copy mode | Ctrl+a [ | Ctrl+b [ |
| Paste | Ctrl+a ] | Ctrl+b ] |

## Common Workflows

### Screen Workflow
```bash
# Start session with name
screen -S myproject

# Work in multiple windows
Ctrl+a c    # Create windows
Ctrl+a n    # Navigate

# Detach
Ctrl+a d

# Later, reattach
screen -r myproject
```

### Tmux Workflow
```bash
# Start session with name
tmux new -s myproject

# Split into panes
Ctrl+b %    # Vertical split
Ctrl+b "    # Horizontal split

# Navigate panes
Ctrl+b arrow keys

# Create windows
Ctrl+b c

# Detach
Ctrl+b d

# Later, reattach
tmux attach -t myproject
```

## Tips and Tricks

### Screen
```bash
# Run command in new window
screen -t "logs" tail -f /var/log/syslog

# Hardcopy (save screen to file)
Ctrl+a h

# Lock screen
Ctrl+a x

# Show system info
Ctrl+a i
```

### Tmux
```bash
# Run command in new session
tmux new -s logs "tail -f /var/log/syslog"

# Create layout
tmux new -s dev "vim" \; split-window -h "npm start" \; split-window -v

# Capture pane output to file
tmux capture-pane -p > output.txt

# Synchronize panes (send input to all)
Ctrl+b :setw synchronize-panes on

# Clock
Ctrl+b t

# Show tmux info
Ctrl+b i
```

### Nested Sessions
```bash
# Screen in Screen: use Ctrl+a a to send prefix
Ctrl+a a n   # Next window in nested screen

# Tmux in Tmux: use prefix twice
Ctrl+b Ctrl+b n   # Next window in nested tmux
```

````
