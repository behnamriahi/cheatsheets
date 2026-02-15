# Vim Cheatsheet

## Modes
```
ESC         # Normal mode
i           # Insert mode (before cursor)
a           # Insert mode (after cursor)
I           # Insert at beginning of line
A           # Insert at end of line
o           # Open new line below
O           # Open new line above
v           # Visual mode (character)
V           # Visual mode (line)
Ctrl+v      # Visual block mode
R           # Replace mode
:           # Command mode
```

## Basic Navigation
```
h           # Move left
j           # Move down
k           # Move up
l           # Move right
w           # Next word
b           # Previous word
e           # End of word
0           # Beginning of line
^           # First non-blank character
$           # End of line
gg          # First line of file
G           # Last line of file
{n}G        # Go to line n
{n}gg       # Go to line n
Ctrl+f      # Page down
Ctrl+b      # Page up
Ctrl+d      # Half page down
Ctrl+u      # Half page up
%           # Jump to matching bracket
```

## Editing
```
x           # Delete character
X           # Delete character before cursor
dd          # Delete line
D           # Delete from cursor to end of line
dw          # Delete word
d$          # Delete to end of line
d0          # Delete to beginning of line
yy          # Copy line
yw          # Copy word
y$          # Copy to end of line
p           # Paste after cursor
P           # Paste before cursor
u           # Undo
Ctrl+r      # Redo
.           # Repeat last command
~           # Toggle case
J           # Join line below
cc          # Change entire line
cw          # Change word
C           # Change to end of line
s           # Substitute character
S           # Substitute entire line
r{char}     # Replace one character
```

## Search and Replace
```
/{pattern}  # Search forward
?{pattern}  # Search backward
n           # Next match
N           # Previous match
*           # Search word under cursor (forward)
#           # Search word under cursor (backward)
:%s/old/new/g       # Replace all occurrences in file
:%s/old/new/gc      # Replace all with confirmation
:s/old/new/g        # Replace in current line
:'<,'>s/old/new/g   # Replace in visual selection
:noh        # Clear search highlight
```

## File Operations
```
:w          # Save file
:w filename # Save as filename
:q          # Quit
:q!         # Quit without saving
:wq         # Save and quit
:x          # Save and quit (only if changed)
ZZ          # Save and quit
ZQ          # Quit without saving
:e filename # Open file
:saveas filename # Save as new file
:bn         # Next buffer
:bp         # Previous buffer
:bd         # Close buffer
:ls         # List buffers
```

## Visual Mode
```
v           # Start visual mode (character)
V           # Start visual mode (line)
Ctrl+v      # Start visual block mode
o           # Move to other end of selection
O           # Move to other corner of block
aw          # Select a word
ab          # Select a block with ()
aB          # Select a block with {}
ib          # Select inner block with ()
iB          # Select inner block with {}
```

## Visual Mode Operations
```
d           # Delete selection
y           # Copy selection
c           # Change selection
>           # Indent right
<           # Indent left
~           # Toggle case
u           # Lowercase
U           # Uppercase
```

## Multi-line Editing (Block Visual)
```
Ctrl+v      # Enter visual block mode
{select}    # Select lines
I           # Insert at beginning of each line
A           # Append at end of each line
c           # Change selected text
d           # Delete selected text
```

## Cut, Copy, Paste
```
yy          # Copy line
{n}yy       # Copy n lines
yw          # Copy word
y$          # Copy to end of line
dd          # Delete (cut) line
{n}dd       # Delete n lines
dw          # Delete word
p           # Paste after cursor
P           # Paste before cursor
"ayy        # Copy line to register a
"ap         # Paste from register a
:reg        # Show registers
```

## Marks and Jumps
```
m{a-z}      # Set mark
`{a-z}      # Jump to mark
'{a-z}      # Jump to mark (first non-blank)
`.          # Jump to last change
`"          # Jump to last position before exit
Ctrl+o      # Jump to older position
Ctrl+i      # Jump to newer position
:marks      # List marks
:jumps      # List jump history
```

## Macros
```
q{a-z}      # Start recording macro in register {a-z}
q           # Stop recording
@{a-z}      # Execute macro from register {a-z}
@@          # Re-execute last macro
{n}@{a-z}   # Execute macro n times
```

## Indentation
```
>>          # Indent line right
<<          # Indent line left
>%          # Indent block
=%          # Auto-indent block
gg=G        # Auto-indent entire file
={motion}   # Auto-indent motion
:set tabstop=4      # Set tab width
:set shiftwidth=4   # Set indent width
:set expandtab      # Use spaces instead of tab
```

## Folding
```
zf{motion}  # Create fold
zf#j        # Create fold for # lines down
zf/string   # Fold until string
zd          # Delete fold
za          # Toggle fold
zo          # Open fold
zc          # Close fold
zR          # Open all folds
zM          # Close all folds
```

## Windows and Tabs
```
:sp         # Horizontal split
:vsp        # Vertical split
:sp filename # Open file in horizontal split
:vsp filename # Open file in vertical split
Ctrl+w s    # Horizontal split
Ctrl+w v    # Vertical split
Ctrl+w w    # Switch windows
Ctrl+w h    # Move to left window
Ctrl+w j    # Move to window below
Ctrl+w k    # Move to window above
Ctrl+w l    # Move to right window
Ctrl+w q    # Close window
Ctrl+w o    # Close all other windows
Ctrl+w =    # Equal size windows
Ctrl+w +    # Increase height
Ctrl+w -    # Decrease height
Ctrl+w >    # Increase width
Ctrl+w <    # Decrease width

:tabnew     # New tab
:tabnew filename # Open file in new tab
gt          # Next tab
gT          # Previous tab
{n}gt       # Go to tab n
:tabclose   # Close tab
:tabonly    # Close all other tabs
:tabs       # List tabs
```

## Command Mode
```
:!{command} # Execute shell command
:r !{command} # Read command output into file
:w !sudo tee % # Save file with sudo
:cd {path}  # Change directory
:pwd        # Show current directory
:set number # Show line numbers
:set nonumber # Hide line numbers
:set relativenumber # Show relative line numbers
:set paste  # Paste mode
:set nopaste # Normal mode
:syntax on  # Enable syntax highlighting
:syntax off # Disable syntax highlighting
:colorscheme {name} # Change color scheme
```

## Line Numbers
```
:set number         # Show line numbers
:set nonumber       # Hide line numbers
:set relativenumber # Show relative line numbers
:set nornu          # Hide relative line numbers
```

## Advanced Search and Replace
```
:%s/foo/bar/g       # Replace all foo with bar in file
:%s/foo/bar/gc      # Replace with confirmation
:'<,'>s/foo/bar/g   # Replace in visual selection
:5,12s/foo/bar/g    # Replace in lines 5-12
:.,$s/foo/bar/g     # Replace from current line to end
:.,+5s/foo/bar/g    # Replace in current line and next 5
:g/pattern/d        # Delete all lines matching pattern
:v/pattern/d        # Delete all lines not matching pattern
:g/pattern/s/foo/bar/g # Replace in lines matching pattern
```

## Text Objects
```
iw          # Inner word
aw          # A word (including space)
iW          # Inner WORD (includes punctuation)
aW          # A WORD
is          # Inner sentence
as          # A sentence
ip          # Inner paragraph
ap          # A paragraph
i[          # Inner block [ ]
a[          # A block [ ]
i(          # Inner block ( )
a(          # A block ( )
i{          # Inner block { }
a{          # A block { }
i<          # Inner block < >
a<          # A block < >
i"          # Inner string " "
a"          # A string " "
i'          # Inner string ' '
a'          # A string ' '
it          # Inner tag block
at          # A tag block
```

## Combining Operators with Text Objects
```
diw         # Delete inner word
daw         # Delete a word
ciw         # Change inner word
caw         # Change a word
yi(         # Copy inside parentheses
da"         # Delete around quotes
ci{         # Change inside braces
dit         # Delete inside tag
```

## Autocomplete (Insert Mode)
```
Ctrl+n      # Next autocomplete suggestion
Ctrl+p      # Previous autocomplete suggestion
Ctrl+x Ctrl+f # File path completion
Ctrl+x Ctrl+l # Line completion
Ctrl+x Ctrl+o # Omni completion
```

## Useful Settings (.vimrc)
```vim
set number              " Show line numbers
set relativenumber      " Show relative line numbers
set tabstop=4           " Tab width
set shiftwidth=4        " Indentation width
set expandtab           " Use spaces instead of tabs
set autoindent          " Auto indentation
set smartindent         # Smart indentation
set hlsearch            " Highlight search
set incsearch           " Incremental search
set ignorecase          " Ignore case in search
set smartcase           " Case-sensitive if uppercase used
set mouse=a             " Enable mouse
set clipboard=unnamedplus " Use system clipboard
syntax on               " Enable syntax highlighting
```

## Quick Tips
```
:earlier 5m # Go to file state 5 minutes ago
:later 5m   # Go forward in time
:%!xxd      # View file in hex mode
:%!xxd -r   # Convert from hex
:TOhtml     # Convert to HTML with syntax highlighting
Ctrl+a      # Increment number under cursor
Ctrl+x      # Decrement number under cursor
gf          # Open file under cursor
K           # Open man page for word under cursor
```
