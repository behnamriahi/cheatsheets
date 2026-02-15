# 🐚 Bash/Shell Scripting Cheatsheet

## 📜 Basic Script Structure

```bash
#!/bin/bash
# This is a comment
# Script description

# Variables
NAME="John"
AGE=30

# Script commands
echo "Hello, $NAME!"
echo "You are $AGE years old."
```

---

## 📌 Shebang Lines

```bash
#!/bin/bash                            # Bash script
#!/bin/sh                              # POSIX shell script
#!/usr/bin/env bash                    # Portable bash
#!/usr/bin/env python3                 # Python script
```

---

## 🔤 Variables

```bash
# Variable assignment
NAME="John Doe"
AGE=30
readonly PI=3.14                       # Read-only variable

# Variable usage
echo $NAME
echo ${NAME}                           # Preferred way
echo "Hello, $NAME!"
echo 'Hello, $NAME!'                   # Single quotes - no expansion

# Command substitution
TODAY=$(date)
FILES=$(ls)
USERS=`who`                            # Old syntax

# Special variables
$0                                     # Script name
$1, $2, $3...                          # Positional parameters
$#                                     # Number of arguments
$@                                     # All arguments as separate words
$*                                     # All arguments as single word
$?                                     # Exit status of last command
$$                                     # Process ID of current shell
$!                                     # PID of last background command
```

---

## 🔢 Arrays

```bash
# Array declaration
FRUITS=("Apple" "Banana" "Orange")
NUMBERS=(1 2 3 4 5)

# Array operations
echo ${FRUITS[0]}                      # Access element
echo ${FRUITS[@]}                      # All elements
echo ${#FRUITS[@]}                     # Array length
FRUITS+=("Mango")                      # Append element
FRUITS[1]="Grape"                      # Modify element

# Associative arrays (bash 4+)
declare -A CAPITALS
CAPITALS[UK]="London"
CAPITALS[France]="Paris"
echo ${CAPITALS[UK]}
echo ${!CAPITALS[@]}                   # All keys
echo ${CAPITALS[@]}                    # All values
```

---

## 🎛️ User Input

```bash
# Read input
read NAME
read -p "Enter your name: " NAME       # With prompt
read -sp "Enter password: " PASSWORD   # Silent input
read -t 5 -p "Quick! Enter name: " NAME  # Timeout
read -n 1 -p "Press any key..." KEY    # Single character

# Read multiple values
read -p "Enter first and last name: " FIRST LAST

# Read from file
while read LINE; do
    echo $LINE
done < file.txt
```

---

## ➕ Arithmetic Operations

```bash
# Using $(( ))
NUM=$((5 + 3))                         # Addition
NUM=$((10 - 2))                        # Subtraction
NUM=$((4 * 3))                         # Multiplication
NUM=$((20 / 5))                        # Division
NUM=$((10 % 3))                        # Modulo
NUM=$((2 ** 8))                        # Exponentiation

# Using let
let "NUM = 5 + 3"
let "NUM++"                            # Increment
let "NUM--"                            # Decrement

# Using expr
NUM=$(expr 5 + 3)
NUM=$(expr $NUM \* 2)                  # Escape *

# Floating point (using bc)
RESULT=$(echo "scale=2; 10 / 3" | bc)
```

---

## ✅ Conditional Statements

```bash
# If statement
if [ condition ]; then
    # commands
fi

if [ condition ]; then
    # commands
else
    # commands
fi

if [ condition1 ]; then
    # commands
elif [ condition2 ]; then
    # commands
else
    # commands
fi

# One-liner
[ condition ] && echo "true" || echo "false"

# Test conditions
[ -f file.txt ]                        # File exists
[ -d directory ]                       # Directory exists
[ -r file.txt ]                        # File is readable
[ -w file.txt ]                        # File is writable
[ -x file.txt ]                        # File is executable
[ -s file.txt ]                        # File is not empty
[ file1 -nt file2 ]                    # file1 newer than file2
[ file1 -ot file2 ]                    # file1 older than file2

# String comparisons
[ "$str1" = "$str2" ]                  # Equal
[ "$str1" != "$str2" ]                 # Not equal
[ -z "$str" ]                          # Empty string
[ -n "$str" ]                          # Not empty string

# Numeric comparisons
[ $num1 -eq $num2 ]                    # Equal
[ $num1 -ne $num2 ]                    # Not equal
[ $num1 -lt $num2 ]                    # Less than
[ $num1 -le $num2 ]                    # Less or equal
[ $num1 -gt $num2 ]                    # Greater than
[ $num1 -ge $num2 ]                    # Greater or equal

# Logical operators
[ condition1 ] && [ condition2 ]       # AND
[ condition1 ] || [ condition2 ]       # OR
[ ! condition ]                        # NOT
[ condition1 -a condition2 ]           # AND (inside test)
[ condition1 -o condition2 ]           # OR (inside test)

# Modern [[ ]] syntax (bash)
[[ $str =~ regex ]]                    # Regex match
[[ $str == pattern* ]]                 # Pattern match
```

---

## 🔁 Loops

```bash
# For loop
for i in 1 2 3 4 5; do
    echo $i
done

for file in *.txt; do
    echo $file
done

for ((i=1; i<=10; i++)); do
    echo $i
done

# While loop
COUNT=0
while [ $COUNT -lt 10 ]; do
    echo $COUNT
    ((COUNT++))
done

# Until loop
COUNT=0
until [ $COUNT -ge 10 ]; do
    echo $COUNT
    ((COUNT++))
done

# Read file line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt

# Infinite loop
while true; do
    echo "Running..."
    sleep 1
done

# Break and continue
for i in {1..10}; do
    [ $i -eq 5 ] && continue           # Skip 5
    [ $i -eq 8 ] && break              # Stop at 8
    echo $i
done
```

---

## 🎯 Case Statements

```bash
case $VARIABLE in
    pattern1)
        # commands
        ;;
    pattern2|pattern3)
        # commands
        ;;
    *)
        # default
        ;;
esac

# Example
case $1 in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

---

## 📞 Functions

```bash
# Function declaration
function_name() {
    # commands
}

function function_name {
    # commands
}

# Function with parameters
greet() {
    echo "Hello, $1!"
    echo "You are $2 years old."
}
greet "John" 30

# Return value
add() {
    local result=$(($1 + $2))
    echo $result
}
SUM=$(add 5 3)

# Return status
check_file() {
    [ -f "$1" ] && return 0 || return 1
}
check_file "test.txt" && echo "Exists" || echo "Not found"

# Local variables
my_function() {
    local LOCAL_VAR="local"
    GLOBAL_VAR="global"
}
```

---

## 🔤 String Manipulation

```bash
# String length
STRING="Hello World"
echo ${#STRING}                        # 11

# Substring
echo ${STRING:0:5}                     # Hello
echo ${STRING:6}                       # World

# Replace
echo ${STRING/World/Bash}              # Replace first
echo ${STRING//o/0}                    # Replace all

# Remove prefix/suffix
FILE="example.tar.gz"
echo ${FILE%.gz}                       # example.tar
echo ${FILE%.*}                        # example.tar
echo ${FILE%%.*}                       # example
echo ${FILE#*.}                        # tar.gz
echo ${FILE##*.}                       # gz

# Upper/Lower case
STRING="Hello World"
echo ${STRING^^}                       # HELLO WORLD (bash 4+)
echo ${STRING,,}                       # hello world (bash 4+)
echo $STRING | tr '[:lower:]' '[:upper:]'  # HELLO WORLD
echo $STRING | tr '[:upper:]' '[:lower:]'  # hello world

# Default values
echo ${VAR:-default}                   # Use default if VAR empty
echo ${VAR:=default}                   # Set and use default
echo ${VAR:+alternate}                 # Use alternate if VAR set
```

---

## 📁 File Operations

```bash
# Read file
cat file.txt
cat < file.txt
while read line; do echo $line; done < file.txt

# Write to file
echo "text" > file.txt                 # Overwrite
echo "text" >> file.txt                # Append

# Create file
touch file.txt
> file.txt                             # Empty file

# Delete file
rm file.txt
rm -f file.txt                         # Force delete

# Copy/Move
cp source.txt dest.txt
mv source.txt dest.txt

# File info
ls -l file.txt
stat file.txt
file file.txt                          # File type

# Check if file exists
if [ -f file.txt ]; then
    echo "File exists"
fi
```

---

## 🗂️ Directory Operations

```bash
# Current directory
pwd

# Change directory
cd /path/to/dir
cd ~                                   # Home directory
cd -                                   # Previous directory

# Create directory
mkdir mydir
mkdir -p parent/child/grandchild       # Create parents

# Remove directory
rmdir mydir                            # Empty directory
rm -r mydir                            # Recursive delete
rm -rf mydir                           # Force recursive delete

# List directory
ls
ls -la                                 # Long format, hidden files
ls -lh                                 # Human readable sizes
ls -lt                                 # Sort by time
```

---

## 🔄 Input/Output Redirection

```bash
# Output redirection
command > file                         # Stdout to file (overwrite)
command >> file                        # Stdout to file (append)
command 2> file                        # Stderr to file
command &> file                        # Both stdout and stderr
command > file 2>&1                    # Both (compatible)

# Input redirection
command < file                         # Input from file

# Here document
cat << EOF
Line 1
Line 2
EOF

# Here string
grep "pattern" <<< "search this string"

# Pipe
command1 | command2                    # Output of cmd1 to input of cmd2
command1 |& command2                   # Both stdout and stderr

# Tee (write to file and stdout)
command | tee file.txt
command | tee -a file.txt              # Append
```

---

## ⚙️ Process Management

```bash
# Background process
command &
nohup command &                        # Ignore hangup signal

# Job control
jobs                                   # List jobs
fg %1                                  # Bring job 1 to foreground
bg %1                                  # Resume job 1 in background
kill %1                                # Kill job 1

# Process info
ps aux                                 # All processes
ps -ef                                 # All processes (different format)
pgrep process_name                     # Find process by name
pidof process_name                     # Get PID

# Kill process
kill PID                               # Terminate
kill -9 PID                            # Force kill
killall process_name                   # Kill by name
pkill process_name                     # Kill by name pattern

# Wait for process
wait PID
wait                                   # Wait for all background jobs
```

---

## 🛠️ Useful Commands

```bash
# Command substitution
FILES=$(ls)
DATE=`date`

# Pipeline
cat file.txt | grep "pattern" | sort | uniq

# Logical operators
command1 && command2                   # Run cmd2 if cmd1 succeeds
command1 || command2                   # Run cmd2 if cmd1 fails
command1 ; command2                    # Run both regardless

# Grouping
(command1; command2)                   # Run in subshell
{ command1; command2; }                # Run in current shell

# Test command
test -f file.txt
[ -f file.txt ]                        # Same as test

# Printf (formatted output)
printf "Name: %s, Age: %d\n" "John" 30

# Sleep
sleep 5                                # Sleep 5 seconds
sleep 1m                               # Sleep 1 minute
sleep 1h                               # Sleep 1 hour
```

---

## 🐛 Debugging

```bash
# Debug mode
bash -x script.sh                      # Print commands
set -x                                 # Enable debug mode
set +x                                 # Disable debug mode

# Verbose mode
bash -v script.sh                      # Print lines as read
set -v                                 # Enable verbose
set +v                                 # Disable verbose

# Exit on error
set -e                                 # Exit on error
set -u                                 # Exit on undefined variable
set -o pipefail                        # Exit on pipe failure

# Combined
set -euo pipefail                      # Strict mode

# Trap errors
trap 'echo "Error on line $LINENO"' ERR
```

---

## 💡 Best Practices

```bash
# Always quote variables
echo "$VAR"                            # Good
echo $VAR                              # Bad (word splitting)

# Use [[ ]] instead of [ ]
[[ $var = value ]]                     # Good (bash)
[ $var = value ]                       # OK (POSIX)

# Check command exists
command -v git >/dev/null 2>&1 || { echo "git required"; exit 1; }

# Use functions
do_something() {
    local var="value"
    # commands
}

# Error handling
if ! command; then
    echo "Command failed"
    exit 1
fi

# Use meaningful variable names
USER_NAME="john"                       # Good
un="john"                              # Bad

# Add comments
# This function does X
function_name() {
    # implementation
}

# Set script options
set -euo pipefail                      # Fail on errors
```

---

## 📋 Complete Script Template

```bash
#!/bin/bash
#
# Script Name: script_name.sh
# Description: Script description here
# Author: Your Name
# Date: 2024-01-01
#

set -euo pipefail                      # Exit on error

# Constants
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"

# Variables
DEBUG=false
VERBOSE=false

# Functions
usage() {
    cat << EOF
Usage: $SCRIPT_NAME [OPTIONS]

Options:
    -h, --help      Show this help message
    -v, --verbose   Verbose output
    -d, --debug     Debug mode
EOF
    exit 0
}

log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*"
}

error() {
    echo "[ERROR] $*" >&2
    exit 1
}

# Parse arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            ;;
        -v|--verbose)
            VERBOSE=true
            shift
            ;;
        -d|--debug)
            DEBUG=true
            set -x
            shift
            ;;
        *)
            error "Unknown option: $1"
            ;;
    esac
done

# Main script
main() {
    log "Starting script"
    
    # Your code here
    
    log "Script completed"
}

# Run main function
main "$@"
```
