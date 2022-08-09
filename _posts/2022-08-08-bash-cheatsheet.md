---
layout: post
title:  "Bash Cheatsheet"
date:   2022-08-08 09:28:49 +0700
categories: bash
---

# Basics 

**Shebang:**
```bash 
#!/bin/bash 
```

**Executable Bit Set:**
```bash 
chmod +x my_script.sh 
```

# Best Practices
- Always use a shebang 
- Always exit script with an exit status
- Use local variables in functions 

# Job Control
- What to do with a command
- **&** - Tells linux to start process in background so you can do something else

```bash
./start_my_long_script.sh & 
do other things....
```

# Useful Commands 

**SSHPASS:**
- Used to be able to enter password

```bash 
sshpass -p "PASSWORD" ssh -o StrictHostKeyChecking=no username@host
```

**MKDIR:**

```bash 
# -p flag creates any other necessary directories
mkdir -p /var/log/system/apps/app.log
```

**Convert to lower case:**
```bash
SLIDES=${SLIDES,,}
```

**Replace Spaces with underscore:**
```bash
SLIDES=${SLIDES// /_}
```

# Variables Input 

**Static:**
- Names should be all upper case
- Don't use any spaces 

```bash 
HOSTNAME="rtr-bfa1.example.net"
```

**Dynamic (Output from command):**
```bash 
HOSTNAME=$(hostname)
```

# Variables Output 

```bash 
NAME="Anton"

# Option 1
echo "my name is $ANTON"

# Option 2 (useful if you have data right next to var)
echo "my name is ${ANTON}!"
```

# User Input
```bash 
read -p "Enter a router hostname: " HOSTNAME
echo "SSHing into ${HOSTNAME}"
```

# Arrays 
```bash 
FILES=(router1 router2 router3)

for FILE in "${FILES[@]}"
do
    echo ${FILE}
    touch $FILE
done
```

# Exit Codes 
- **0** == Success
- Every command returns an exist status 0-255
- **$?** Contains returncode of previously executed command

```bash 
HOST="8.8.8.8"
ping -c 1 $HOST
RETURN_CODE="$?"

if [ "$RETURN_CODE" -eq "0" ]
then 
  echo "$HOST Reachable"
else 
  echo "$HOST Unreachable"
fi
```

#### Exit Code from Shell Script 
- By default last command to be run will be the exit number 
- Can be overridden - This also exits the script

```bash 
if [ "$RETURN_CODE" -ne "0" ] ; then
  echo "$HOST Unreachable"
  exit 1
fi
exit 0
```

# Command Output 
- By default output of command will be echoed 

```bash
# Suppress output to Null
ping -c 1 8.8.8.8 >/dev/null

# As above but also suppresses exit code 
ping -c 1 8.8.8.8 > /dev/null 2>&1
```

# Tests 

### If Statements
- Variables should be enclosed in quotes
```bash
HOSTNAME=$(hostname)

if [ "$HOSTNAME" = "COMPUTER1" ]
then
  command1
  command2
fi
```

### Else 
```bash
if [ condition-is-true ]
then
  command1
else
  command2
fi
```

### Elif 
```bash
if [ condition-is-true ]
then
  command1
elif [ condition-is-true ]
then
  command2
fi
```

### For Loop 
```bash
for LINE in FILE
do
  echo "VAR: $LINE"
done 
```

**Samples:**
- Move all jpg files and add date to file name
```bash 
PICTURES=$(ls *jpg)
DATE=$(date +%F)

for PICTURE in PICTURES
do 
  echo "Renaming ${PICTURE} to ${DATE}-${PICTURE}"
  mv ${PICTURE} ${DATE}-${PICTURE}
done
```

### Conditionals 

**Check if file exists:**
```bash 
[ -e /etc/prom/config.cfg ]
```

**File Operator Conditionals:**

| Condition | Description                               |
| --------- | ----------------------------------------- |
| -d FILE   | True if file is a directory               |
| -e FILE   | True if file exists                       |
| -f FILE   | True if file exists and is a regular file |
| -r FILE   | True if file is readable by you           |
| -s FILE   | True if file exists and is not empty      |
| -w FILE   | True if the file is writable by you       |
| -x FILE   | True if file is executable by you         |
|           |                                           |

**String Conditionals:**

| Condition          | Description                 |
| ------------------ | --------------------------- |
| -z STRING          | True if string is empty     |
| -n STRING          | True if string is not empty |
| STRING1 = STRING2  | True if strings are equal   |
| STRING1 != STRING2 | True if not equals          |
|                    |                             |

**Arthmetic Operators:**

| Condition     | Description                       |
| ------------- | --------------------------------- |
| arg1 -eq arg2 | True if arg1 is equal to arg2     |
| arg1 -ne arg2 | True if arg1 is not equal         |
|               |                                   |
| arg1 -lt arg2 | True if arg1 is less than arg2    |
| arg1 -le arg2 | True if arg1 is less or equal     |
|               |                                   |
| arg1 -gt arg2 | True if arg1 is greater than arg2 |
| arg1 -ge arg2 | True if arg1 is greater or equal  |


# Script Arguments/Parameters 
- **$0** - script.sh (name of the script)
- **$1** - Parameter1
- **$2** - Parameter2

**Sample Code:**

```bash
echo "Executing script: $0"
echo "SSHing into: $1"
```

**Loop through all Parameters:**
```bash

for FOLDER in $@ 
do 
    echo "Moving folder: $FOLDER to /tmp/${FOLDER}"
    mv $FOLDER /tmp/${FOLDER}
done 
```

# Logical Operators 

### AND = \&\&
- Second command only executes if first command return 0
```bash 
mkdir /tmp/bak && cp test.txt /tmp/bak/

ping -c1 $HOST >/dev/null && echo "$HOST Reachable"
```

### OR = ||

```bash 
cp test.txt /tmp/bak/ || cp test /tmp/

ping -c1 $HOST >/dev/null || echo "$HOST Unreachable"
```


# OneLiners 
**;** - Semicolon can be used to execute multiple commands on one line 
```bash 
cp test.txt /tmp/bak ; cp test2.txt /tmp/bak
```

# Functions 
- Variables created in function are by default available outside once the function has been called and the variable created. 

```bash

# Option 1 
function multiply() { 
    echo "Function"
}

multiply

# Option 2
multiply() { 
    echo "Function"
}

multiply
```

### Arguments
- $1 - Argument1 
- $2 - Argument2
- $@ - All Arguments
```bash 
function move_folders() {
    for FOLDER in $@ 
    do
        mv $FOLDER /bak/${FOLDER}
}

move_folders folder1 folder2 folder3
```

### Local Variables 
```bash 
local HOST="LOCALHOST"
```

### Exit Status
- Only 0-255 
- By default the value of the last command run 
```bash 

# Access exit code
my_function 
echo $?

# Modify Exit COde
function my_function() {
    return 0
}

```
### Sample Script 
- Backup File 

```bash 
function backup_file () {
    if [ -f $1 ]
    then
        BACK="/tmp/$basename ${1}.$(date + %F).$$"
        echo "Backing up ${1} to ${BACK}"
        cp $1 $BACK
    fi
}

backup_file /etc/hosts
if [ $? -eq 0 ]
then
    echo "Backup succeeded!"
fi
```

# Wildcards 
- * - Matches zero or more characters 
- ? - Matches exactly one character
- [ad] - Matches exactly one of the characters inside the brackets
- [!abc] - Matches and excludes one of the characters in the brackets
- [a-z] - Matches a range of characters
- [1-5] - Matches a range of numbers
- "\"- Escape character
- ?? - Matches all files that are 2 character long

```bash 
mv *.jpg /tmp/bak/
mv 2022-22-??.txt /tmp/bak/
mv ca[nt]* /tmp/bak/ # (can, cat, candy, catch, etc)
mv [!sr]* /tmp/bak/ # Excludes any file that begins with s or r

[[:alpha:]] - Matches lower and upper case letters
[[:alnum:]] - Matches digits and numbers 
[[:space:]] - Matches space/newline 
[[:upper:]] - Matches all upper cases 
\?          - Matches question mark
```

**Sample Code:**
```bash
for HOST in gbg-*.host
do 
    OUTPUT=$(ssh $HOST "show hostname")
    echo "$HOST - $OUTPUT"
```

# Logging
- Ends up in /var/log/syslog or /var/log/messages

### Facilities:
- Kern (Kernel)
- User (If unsure, use this)
- mail
- daemon
- auth
- local0 - Use for custom logs (custom scripts)
- local...
- local7 - Use for custom logs (custom scripts)

### Severities:
- emerg 
- alert
- crit
- err
- warning
- notice
- info
- debug

### Sample Code:
```bash
# By default using notice severity and user severity 
logger "Message"

# Customize:
logger -p local0.info "Message"

logger -t my_script -p- local0.info "Message"
```


# Case Statements 
- Replaces multiple if statements. 
- Can be used for system startup scripts for example. 
- Are case sensitive

```bash 
ACTION="$1"
case "$ACTION" in 
    start)
        docker-compose up -d
        /usr/sbin/sshd
        ;; 
    stop)
        docker-compose down
        kill $(cat /var/run/sshd.pid)
        ;;
    *)
        echo "Usage: $0 start|stop" ; exit 1
        ;;
esac

# Allowing
case "$1" in 
    start|START|Start)
        action...

# Accepts "y" "Y" "yes" "Yes" "YES" "yES"
case "$ANSWER" in 
    [yY] | [Yy] [eE] [sS])
```

# While Loops 
- **break** - Exits loop
- **continue** - Starts over at the top of the loop
- Use-Cases:
    - While exit code of a command is NOT 0, continue 

```bash
INDEX=1
while [ $INDEX -lt 10 ]
do 
    echo "Creating project-${INDEX}"
    mkdir /usr/local/project-${INDEX}
    ((INDEX++))
done
```

### Loop Forever (While True)
```bash
while true 
do 
    command X 
    sleep 1
done
```

```
while ping -c 1 app1 >/dev/null
do 
    "App still up..."
    sleep 5 
done 

echo "app down, continuing..."
```

### Reading File Line by Line 
- If you use a for loop to read a file line by line, it will read word for word. Use while loop for this. 
- Will read /etc/fstab file 

**Option 1:**
```bash
LINE_NUM=1 
while read LINE 
do 
    echo "${LINE_NUM}: ${LINE}"
    ((LINE_NUM++))
done < /etc/fstab 

```

**Option 2:** - Reads output of a command 
```bash
grep xfs /etc/fstab | while read LINE 
do 
    echo "xfs: ${LINE}"
done
```

# Debugging 
x-trace: **-x** flag prints commands as they execute
**-e** Exits script on error
**-v** Prints the code line by line 
```bash 
# Option 1
#!/bin/bash -xe

# Option 2 
set -x 
Some code...
Some Code...
set +x
```

**Troubleshooting Tips:**
```bash
#!/bin/bash 
DEBUG="echo"
$DEBUG ls 
# If debug is false, ls is executed normally. 
```