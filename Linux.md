# Complete Linux Guide for DevOps Engineers (A-Z)

**Version:** 2.0  
**Last Updated:** December 2025  
**Target Audience:** DevOps Engineers, System Administrators, Interview Preparation

---

## Table of Contents

1. [Introduction to Linux](#introduction-to-linux)
2. [Linux Architecture](#linux-architecture)
3. [File System Hierarchy](#file-system-hierarchy)
4. [Basic Commands](#basic-commands)
5. [File and Directory Management](#file-and-directory-management)
6. [Text Processing](#text-processing)
7. [User and Group Management](#user-and-group-management)
8. [Permissions and Ownership](#permissions-and-ownership)
9. [Process Management](#process-management)
10. [System Monitoring](#system-monitoring)
11. [Networking](#networking)
12. [Package Management](#package-management)
13. [Shell Scripting](#shell-scripting)
14. [System Services](#system-services)
15. [Log Management](#log-management)
16. [Disk Management](#disk-management)
17. [Backup and Recovery](#backup-and-recovery)
18. [Security](#security)
19. [Performance Tuning](#performance-tuning)
20. [Troubleshooting](#troubleshooting)
21. [Interview Questions](#interview-questions)

---

## Introduction to Linux

### What is Linux?
Linux is an open-source, Unix-like operating system kernel created by Linus Torvalds in 1991. It forms the foundation of various distributions (distros) like Ubuntu, CentOS, Red Hat, Debian, and Arch Linux.

### Why Linux for DevOps?
- **Open Source:** Free and customizable
- **Stability:** Runs for years without rebooting
- **Security:** Strong permission model and active community
- **Automation:** Excellent scripting capabilities
- **Server Dominance:** Powers 96.3% of the world's top 1 million servers
- **Container Support:** Native Docker and Kubernetes integration

### Popular Linux Distributions for DevOps

| Distribution | Use Case | Package Manager |
|--------------|----------|-----------------|
| Ubuntu | General purpose, cloud | apt/dpkg |
| CentOS/RHEL | Enterprise servers | yum/dnf |
| Debian | Stability-focused | apt/dpkg |
| Amazon Linux | AWS optimized | yum |
| Alpine | Containers (minimal) | apk |
| Fedora | Cutting-edge features | dnf |

---

## Linux Architecture

### Kernel
The core of the operating system that manages:
- Hardware resources (CPU, memory, I/O)
- Process scheduling
- Memory management
- Device drivers
- System calls

### Shell
Command-line interpreter that processes user commands:
- **Bash** (Bourne Again Shell) - Most common
- **Zsh** (Z Shell) - Feature-rich
- **Fish** (Friendly Interactive Shell)
- **Sh** (Bourne Shell) - Original

### System Libraries
Provide functions for applications to interact with the kernel (glibc, libc).

### System Utilities
Essential programs for system operations (ls, cp, mv, grep, etc.).

### Layers Visualization
```
┌─────────────────────────────────┐
│      Applications/Users         │
├─────────────────────────────────┤
│      Shell (Bash/Zsh)          │
├─────────────────────────────────┤
│   System Libraries (glibc)      │
├─────────────────────────────────┤
│      System Utilities           │
├─────────────────────────────────┤
│      Linux Kernel               │
├─────────────────────────────────┤
│      Hardware (CPU/RAM/Disk)    │
└─────────────────────────────────┘
```

---

## File System Hierarchy

### Standard Directory Structure

```
/                    # Root directory
├── bin/            # Essential user binaries (ls, cat, cp)
├── boot/           # Boot loader files (kernel, initrd)
├── dev/            # Device files (sda, tty, null)
├── etc/            # System configuration files
├── home/           # User home directories
├── lib/            # Shared libraries
├── media/          # Removable media mount points
├── mnt/            # Temporary mount points
├── opt/            # Optional software packages
├── proc/           # Process and kernel information (virtual)
├── root/           # Root user home directory
├── run/            # Runtime data (PID files)
├── sbin/           # System binaries (fsck, reboot)
├── srv/            # Service data (web, ftp)
├── sys/            # System information (virtual)
├── tmp/            # Temporary files
├── usr/            # User programs and data
│   ├── bin/       # User binaries
│   ├── lib/       # User libraries
│   ├── local/     # Locally installed software
│   └── share/     # Shared data
└── var/            # Variable data (logs, caches)
    ├── log/       # Log files
    ├── cache/     # Application cache
    └── spool/     # Print queues, mail
```

### Important Directories Explained

**When to use `/tmp` vs `/var/tmp`:**
- `/tmp`: Cleared on reboot, use for temporary session data
- `/var/tmp`: Persists across reboots, use for temporary files that need to survive restarts

**When to use `/opt` vs `/usr/local`:**
- `/opt`: Third-party packaged software (e.g., `/opt/google/chrome`)
- `/usr/local`: Locally compiled/installed software

**Virtual File Systems:**
- `/proc`: Kernel and process information (e.g., `/proc/cpuinfo`, `/proc/meminfo`)
- `/sys`: Hardware and driver information
- `/dev`: Device files (block devices, character devices)

---

## Basic Commands

### Navigation Commands

```bash
# Print working directory
pwd
# Use: Know your current location in the file system

# Change directory
cd /path/to/directory
cd ~                    # Go to home directory
cd -                    # Go to previous directory
cd ..                   # Go up one level
cd ../..                # Go up two levels
# Use: Navigate through file system

# List files and directories
ls                      # Basic listing
ls -l                   # Long format (permissions, owner, size, date)
ls -la                  # Include hidden files
ls -lh                  # Human-readable sizes
ls -lt                  # Sort by modification time
ls -lS                  # Sort by size
ls -R                   # Recursive listing
# Use: View directory contents with various details

# List with specific patterns
ls *.txt                # All .txt files
ls file[1-3].txt       # file1.txt, file2.txt, file3.txt
ls file?.txt           # file1.txt, filea.txt (single character)
# Use: Filter files by pattern
```

### File Information Commands

```bash
# File type information
file filename.txt
file *                  # Check all files in directory
# Use: Determine file type (text, binary, executable, etc.)

# Disk usage
du -sh /path/to/dir    # Summary of directory size
du -h --max-depth=1    # Size of subdirectories (one level)
du -ah /path           # All files with sizes
# Use: Check disk space usage

# File statistics
stat filename.txt
# Use: Detailed file information (inode, permissions, timestamps)

# Word count
wc filename.txt        # Lines, words, characters
wc -l filename.txt     # Count lines only
wc -w filename.txt     # Count words only
wc -c filename.txt     # Count bytes
# Use: Count lines/words in files (useful for logs)
```

### System Information Commands

```bash
# System information
uname -a               # All system information
uname -r               # Kernel version
uname -m               # Machine hardware name
# Use: Check OS and kernel details

# Distribution information
cat /etc/os-release
lsb_release -a
# Use: Identify Linux distribution and version

# Hostname
hostname               # Display hostname
hostname -I            # Display IP addresses
hostnamectl            # Detailed host information
# Use: Get system identification

# Uptime
uptime                 # System uptime and load average
# Use: Check how long system has been running

# Current date and time
date                   # Current date/time
date +%Y-%m-%d         # Custom format: 2025-12-29
date +%s               # Unix timestamp
# Use: Timestamp operations, log analysis

# Calendar
cal                    # Current month
cal 2025               # Entire year
cal 12 2025            # Specific month
# Use: Quick date reference

# Who is logged in
who                    # Logged in users
w                      # Detailed user activity
whoami                 # Current user
id                     # User and group IDs
# Use: User session monitoring
```

---

## File and Directory Management

### Creating Files and Directories

```bash
# Create empty file
touch filename.txt
touch file{1..10}.txt  # Create file1.txt to file10.txt
# Use: Create files, update timestamps

# Create directory
mkdir dirname
mkdir -p parent/child/grandchild  # Create nested directories
mkdir -m 755 dirname              # Create with specific permissions
# Use: Organize file structure

# Create multiple directories
mkdir dir1 dir2 dir3
mkdir -p project/{src,bin,docs,tests}
# Use: Batch directory creation
```

### Copying Files and Directories

```bash
# Copy file
cp source.txt destination.txt
cp source.txt /path/to/destination/
# Use: Duplicate files

# Copy with options
cp -r source_dir/ dest_dir/       # Recursive (directories)
cp -p source.txt dest.txt         # Preserve attributes
cp -u source.txt dest.txt         # Update only if newer
cp -v source.txt dest.txt         # Verbose output
cp -i source.txt dest.txt         # Interactive (prompt before overwrite)
# Use: Advanced copy operations

# Copy multiple files
cp file1.txt file2.txt /destination/
cp *.txt /backup/
# Use: Batch file operations

# Backup with timestamp
cp file.txt file.txt.$(date +%Y%m%d)
# Use: Create dated backups
```

### Moving and Renaming

```bash
# Move file
mv source.txt /destination/
# Use: Relocate files

# Rename file
mv oldname.txt newname.txt
# Use: Change file names

# Move with options
mv -i source.txt dest.txt         # Interactive
mv -u source.txt dest.txt         # Update only if newer
mv -v source.txt dest.txt         # Verbose
# Use: Safe move operations

# Move multiple files
mv *.log /var/log/archive/
# Use: Batch move operations
```

### Deleting Files and Directories

```bash
# Remove file
rm filename.txt
# Use: Delete files

# Remove with options
rm -f filename.txt                # Force (no prompt)
rm -i filename.txt                # Interactive (confirm)
rm -v filename.txt                # Verbose
# Use: Controlled deletion

# Remove directory
rm -r dirname/                    # Recursive
rm -rf dirname/                   # Force recursive (DANGEROUS!)
# Use: Delete directories
# WARNING: rm -rf / can destroy entire system!

# Safe deletion practices
rm -i *.txt                       # Always use -i for wildcards
# Use: Prevent accidental deletion

# Remove empty directories
rmdir dirname/
# Use: Clean up empty directories only
```

### Finding Files

```bash
# Find by name
find /path -name "filename.txt"
find /path -iname "filename.txt"  # Case-insensitive
find . -name "*.log"              # All .log files
# Use: Locate files by name

# Find by type
find /path -type f                # Files only
find /path -type d                # Directories only
find /path -type l                # Symbolic links
# Use: Filter by file type

# Find by size
find /path -size +100M            # Larger than 100MB
find /path -size -10k             # Smaller than 10KB
find /path -size 50M              # Exactly 50MB
# Use: Identify large files

# Find by time
find /path -mtime -7              # Modified in last 7 days
find /path -mtime +30             # Modified more than 30 days ago
find /path -atime -1              # Accessed in last 24 hours
# Use: Time-based file searches

# Find by permissions
find /path -perm 777              # Exact permissions
find /path -perm -644             # At least these permissions
# Use: Security audits

# Find and execute
find /path -name "*.log" -delete  # Delete all .log files
find /path -name "*.txt" -exec chmod 644 {} \;
find /var/log -name "*.log" -mtime +30 -exec gzip {} \;
# Use: Batch operations on found files

# Find with multiple conditions
find /var/log -name "*.log" -size +10M -mtime +7
# Use: Complex search criteria

# Locate (faster alternative, uses database)
locate filename.txt
updatedb                          # Update locate database
# Use: Quick file searches (requires mlocate package)
```

### Linking Files

```bash
# Hard link
ln source.txt hardlink.txt
# Use: Multiple names for same file (same inode)
# When to use: Backup files, prevent accidental deletion

# Symbolic (soft) link
ln -s /path/to/source.txt symlink.txt
ln -s /usr/local/app/current /opt/app
# Use: Shortcuts, version management
# When to use: Point to directories, cross-filesystem links

# Difference between hard and soft links:
# Hard link: Same inode, cannot link directories, same filesystem only
# Soft link: Different inode, can link directories, can cross filesystems

# View links
ls -li                            # Show inode numbers
readlink symlink.txt              # Show link target
# Use: Verify link configuration
```

---

## Text Processing

### Viewing File Contents

```bash
# Display entire file
cat filename.txt
cat file1.txt file2.txt           # Concatenate multiple files
cat -n filename.txt               # Show line numbers
# Use: View small files, combine files

# Display with paging
less filename.txt                 # Navigate with arrows, q to quit
more filename.txt                 # Basic paging
# Use: View large files
# Less navigation: Space (next page), b (previous), / (search), n (next match)

# Display first lines
head filename.txt                 # First 10 lines (default)
head -n 20 filename.txt          # First 20 lines
head -c 100 filename.txt         # First 100 bytes
# Use: Preview file beginning, check log headers

# Display last lines
tail filename.txt                 # Last 10 lines (default)
tail -n 50 filename.txt          # Last 50 lines
tail -f /var/log/syslog          # Follow file (real-time)
tail -F /var/log/app.log         # Follow with retry (if file rotated)
# Use: Monitor logs in real-time, check recent entries

# Display specific lines
sed -n '10,20p' filename.txt     # Lines 10 to 20
awk 'NR==15' filename.txt        # Line 15 only
# Use: Extract specific line ranges
```

### Searching Text

```bash
# Basic grep
grep "pattern" filename.txt
grep "error" /var/log/syslog
# Use: Find text in files

# Grep options
grep -i "pattern" file.txt        # Case-insensitive
grep -v "pattern" file.txt        # Invert match (exclude)
grep -r "pattern" /path/          # Recursive search
grep -n "pattern" file.txt        # Show line numbers
grep -c "pattern" file.txt        # Count matches
grep -l "pattern" *.txt           # List files with matches
grep -w "word" file.txt           # Match whole word only
grep -A 3 "pattern" file.txt      # Show 3 lines after match
grep -B 3 "pattern" file.txt      # Show 3 lines before match
grep -C 3 "pattern" file.txt      # Show 3 lines before and after
# Use: Advanced text searching

# Extended regex
grep -E "pattern1|pattern2" file.txt
egrep "pattern1|pattern2" file.txt
# Use: Complex pattern matching

# Practical grep examples
grep "ERROR" /var/log/app.log | tail -20
grep -r "TODO" --include="*.py" /project/
ps aux | grep nginx
netstat -tulpn | grep :80
# Use: Log analysis, code review, process monitoring

# Search and replace in output
grep "pattern" file.txt | sed 's/old/new/g'
# Use: Filter and transform text
```

### Text Manipulation

```bash
# Sort lines
sort filename.txt
sort -r filename.txt              # Reverse order
sort -n filename.txt              # Numeric sort
sort -k 2 filename.txt            # Sort by 2nd column
sort -u filename.txt              # Remove duplicates
# Use: Organize data, prepare for processing

# Remove duplicates
uniq filename.txt                 # Remove adjacent duplicates
sort filename.txt | uniq          # Remove all duplicates
uniq -c filename.txt              # Count occurrences
uniq -d filename.txt              # Show only duplicates
# Use: Data deduplication, frequency analysis

# Cut columns
cut -d ',' -f 1,3 file.csv       # Extract columns 1 and 3 (CSV)
cut -c 1-10 filename.txt         # Extract characters 1-10
cut -d ':' -f 1 /etc/passwd      # Extract usernames
# Use: Extract specific fields from structured data

# Paste columns
paste file1.txt file2.txt        # Merge files side by side
paste -d ',' file1.txt file2.txt # Use comma as delimiter
# Use: Combine data from multiple sources

# Translate characters
tr 'a-z' 'A-Z' < file.txt        # Convert to uppercase
tr -d '0-9' < file.txt           # Delete all digits
tr -s ' ' < file.txt             # Squeeze multiple spaces
# Use: Character-level transformations

# Word count
wc -l filename.txt               # Count lines
wc -w filename.txt               # Count words
wc -c filename.txt               # Count bytes
# Use: File statistics, log analysis
```

### Advanced Text Processing with sed

```bash
# Basic substitution
sed 's/old/new/' file.txt        # Replace first occurrence per line
sed 's/old/new/g' file.txt       # Replace all occurrences
sed 's/old/new/gi' file.txt      # Case-insensitive replacement
# Use: Text replacement

# In-place editing
sed -i 's/old/new/g' file.txt    # Modify file directly
sed -i.bak 's/old/new/g' file.txt # Create backup before modifying
# Use: Batch file modifications

# Delete lines
sed '5d' file.txt                # Delete line 5
sed '5,10d' file.txt             # Delete lines 5-10
sed '/pattern/d' file.txt        # Delete lines matching pattern
# Use: Remove unwanted lines

# Print specific lines
sed -n '10p' file.txt            # Print line 10
sed -n '10,20p' file.txt         # Print lines 10-20
sed -n '/pattern/p' file.txt     # Print matching lines
# Use: Extract specific content

# Multiple operations
sed -e 's/old/new/g' -e 's/foo/bar/g' file.txt
# Use: Chain transformations

# Practical sed examples
sed 's/^/# /' file.txt           # Add # at beginning of each line
sed 's/$/;/' file.txt            # Add ; at end of each line
sed 's/  */ /g' file.txt         # Replace multiple spaces with single
sed '/^$/d' file.txt             # Remove empty lines
# Use: Text formatting, code modification
```

### Advanced Text Processing with awk

```bash
# Print columns
awk '{print $1}' file.txt        # Print first column
awk '{print $1, $3}' file.txt    # Print columns 1 and 3
awk '{print $NF}' file.txt       # Print last column
# Use: Extract specific fields

# Field separator
awk -F ',' '{print $1}' file.csv # Use comma as separator
awk -F ':' '{print $1}' /etc/passwd # Extract usernames
# Use: Parse structured data

# Conditional processing
awk '$3 > 100' file.txt          # Print lines where column 3 > 100
awk '/pattern/ {print $1}' file.txt # Print column 1 if pattern matches
# Use: Filter data based on conditions

# Built-in variables
awk '{print NR, $0}' file.txt    # NR = line number
awk '{print NF}' file.txt        # NF = number of fields
awk 'END {print NR}' file.txt    # Total line count
# Use: Line numbering, field counting

# Calculations
awk '{sum += $1} END {print sum}' file.txt # Sum column 1
awk '{sum += $1} END {print sum/NR}' file.txt # Average
# Use: Statistical analysis

# Practical awk examples
ps aux | awk '{print $1, $11}'   # Print user and command
df -h | awk '$5 > 80 {print $0}' # Disks over 80% full
awk -F ':' '$3 >= 1000 {print $1}' /etc/passwd # Regular users
netstat -an | awk '/ESTABLISHED/ {print $5}' | cut -d: -f1 | sort | uniq -c
# Use: System monitoring, log analysis
```

---

## User and Group Management

### User Management

```bash
# Add user
useradd username
useradd -m username              # Create home directory
useradd -m -s /bin/bash username # Specify shell
useradd -m -G sudo,docker username # Add to groups
# Use: Create new user accounts

# Add user (interactive)
adduser username                 # Debian/Ubuntu (more user-friendly)
# Use: Guided user creation

# Modify user
usermod -aG groupname username   # Add user to group
usermod -s /bin/zsh username     # Change shell
usermod -l newname oldname       # Rename user
usermod -L username              # Lock user account
usermod -U username              # Unlock user account
# Use: Update user properties

# Delete user
userdel username                 # Delete user (keep home)
userdel -r username              # Delete user and home directory
# Use: Remove user accounts

# Change password
passwd username                  # Set password for user
passwd                           # Change own password
echo "username:newpassword" | chpasswd # Non-interactive
# Use: Password management

# Password expiry
chage -l username                # View password expiry info
chage -M 90 username             # Max 90 days before change required
chage -E 2025-12-31 username     # Account expires on date
# Use: Enforce password policies

# View user information
id username                      # User and group IDs
finger username                  # User details (if installed)
getent passwd username           # User database entry
# Use: User account verification
```

### Group Management

```bash
# Add group
groupadd groupname
groupadd -g 1500 groupname       # Specify GID
# Use: Create new groups

# Modify group
groupmod -n newname oldname      # Rename group
groupmod -g 1600 groupname       # Change GID
# Use: Update group properties

# Delete group
groupdel groupname
# Use: Remove groups

# Add user to group
usermod -aG groupname username
gpasswd -a username groupname
# Use: Group membership management

# Remove user from group
gpasswd -d username groupname
# Use: Revoke group access

# View groups
groups username                  # User's groups
getent group groupname           # Group members
cat /etc/group                   # All groups
# Use: Group membership verification
```

### User and Group Files

```bash
# Important files
/etc/passwd                      # User account information
/etc/shadow                      # Encrypted passwords
/etc/group                       # Group information
/etc/gshadow                     # Secure group information

# /etc/passwd format:
# username:x:UID:GID:comment:home:shell
# Example: john:x:1001:1001:John Doe:/home/john:/bin/bash

# /etc/shadow format:
# username:encrypted_password:last_change:min:max:warn:inactive:expire

# /etc/group format:
# groupname:x:GID:member1,member2
# Example: developers:x:1002:john,jane

# View files safely
cat /etc/passwd
sudo cat /etc/shadow             # Requires root
cat /etc/group
# Use: Audit user/group configuration
```

### Sudo Configuration

```bash
# Edit sudoers file (ALWAYS use visudo)
sudo visudo
# Use: Grant sudo privileges safely

# Sudoers syntax examples:
# username ALL=(ALL:ALL) ALL           # Full sudo access
# username ALL=(ALL) NOPASSWD: ALL     # No password required
# %groupname ALL=(ALL:ALL) ALL         # Group access
# username ALL=(ALL) /usr/bin/systemctl restart nginx # Specific command

# Add user to sudo group
usermod -aG sudo username        # Debian/Ubuntu
usermod -aG wheel username       # RHEL/CentOS
# Use: Grant administrative privileges

# Test sudo access
sudo -l                          # List sudo privileges
sudo -u username command         # Run command as another user
# Use: Verify sudo configuration

# Sudo logs
/var/log/auth.log               # Debian/Ubuntu
/var/log/secure                 # RHEL/CentOS
# Use: Audit sudo usage
```

---

## Permissions and Ownership

### Understanding Permissions

```
Permission Format: -rwxrwxrwx
                   │││││││││└─ Others execute
                   ││││││││└── Others write
                   │││││││└─── Others read
                   ││││││└──── Group execute
                   │││││└───── Group write
                   ││││└────── Group read
                   │││└─────── Owner execute
                   ││└──────── Owner write
                   │└───────── Owner read
                   └────────── File type (- file, d directory, l link)

Numeric Values:
r (read)    = 4
w (write)   = 2
x (execute) = 1

Common Permissions:
644 (rw-r--r--) = Owner read/write, Group read, Others read (files)
755 (rwxr-xr-x) = Owner all, Group read/execute, Others read/execute (directories)
777 (rwxrwxrwx) = All permissions (AVOID - security risk)
600 (rw-------) = Owner read/write only (sensitive files)
700 (rwx------) = Owner all permissions only (private directories)
```

### Changing Permissions

```bash
# Symbolic mode
chmod u+x file.txt               # Add execute for owner
chmod g-w file.txt               # Remove write for group
chmod o+r file.txt               # Add read for others
chmod a+x file.txt               # Add execute for all
chmod u=rwx,g=rx,o=r file.txt   # Set specific permissions
# Use: Granular permission changes

# Numeric mode
chmod 644 file.txt               # rw-r--r--
chmod 755 script.sh              # rwxr-xr-x
chmod 600 private.key            # rw-------
chmod 700 ~/.ssh                 # rwx------
# Use: Quick permission setting

# Recursive
chmod -R 755 /var/www/html       # Apply to directory and contents
# Use: Bulk permission changes

# Practical examples
chmod +x script.sh               # Make script executable
chmod 400 ~/.ssh/id_rsa          # Secure SSH private key
chmod 644 ~/.ssh/id_rsa.pub      # SSH public key
chmod 700 ~/.ssh                 # SSH directory
chmod 600 ~/.ssh/authorized_keys # Authorized keys file
# Use: Common permission scenarios
```

### Changing Ownership

```bash
# Change owner
chown username file.txt
chown username:groupname file.txt # Change owner and group
chown -R username:groupname /path # Recursive
# Use: Transfer file ownership

# Change group only
chgrp groupname file.txt
chgrp -R groupname /path
# Use: Update group ownership

# Practical examples
sudo chown www-data:www-data /var/www/html -R # Web server files
sudo chown -R username:username /home/username # Fix home directory
sudo chown root:root /etc/cron.d/backup       # Secure cron job
# Use: Common ownership scenarios
```

### Special Permissions

```bash
# SUID (Set User ID) - 4xxx
chmod 4755 /usr/bin/passwd       # Run as file owner
chmod u+s file
# Use: Allow users to run program with owner's privileges
# Example: passwd command runs as root to modify /etc/shadow

# SGID (Set Group ID) - 2xxx
chmod 2755 /shared/directory     # New files inherit group
chmod g+s directory
# Use: Shared directories where files should inherit group

# Sticky Bit - 1xxx
chmod 1777 /tmp                  # Only owner can delete own files
chmod +t directory
# Use: Shared directories (like /tmp) to prevent deletion by others

# View special permissions
ls -l /usr/bin/passwd            # Shows 's' in execute position
ls -ld /tmp                      # Shows 't' in others execute position
# Use: Verify special permissions

# Find files with special permissions
find / -perm -4000 2>/dev/null   # Find SUID files
find / -perm -2000 2>/dev/null   # Find SGID files
find / -perm -1000 2>/dev/null   # Find sticky bit files
# Use: Security audits
```

### Access Control Lists (ACL)

```bash
# View ACL
getfacl filename.txt
# Use: Check extended permissions

# Set ACL
setfacl -m u:username:rwx file.txt # Grant user permissions
setfacl -m g:groupname:rx file.txt # Grant group permissions
setfacl -m o::r file.txt            # Set others permissions
# Use: Fine-grained access control

# Remove ACL
setfacl -x u:username file.txt   # Remove user ACL
setfacl -b file.txt               # Remove all ACLs
# Use: Clean up ACL entries

# Default ACL (for directories)
setfacl -d -m u:username:rwx /path # New files inherit ACL
# Use: Automatic permissions for new files

# Recursive ACL
setfacl -R -m u:username:rwx /path
# Use: Bulk ACL changes

# Practical example
setfacl -R -m u:webuser:rwx /var/www/uploads
setfacl -R -m d:u:webuser:rwx /var/www/uploads # Default for new files
# Use: Web application file uploads
```

### File Attributes

```bash
# View attributes
lsattr filename.txt
# Use: Check file attributes

# Set immutable (cannot be modified or deleted)
chattr +i filename.txt
# Use: Protect critical files from accidental changes

# Remove immutable
chattr -i filename.txt
# Use: Allow modifications again

# Append only
chattr +a logfile.log
# Use: Log files that should only be appended

# Common attributes:
# i = immutable
# a = append only
# A = no atime updates
# d = no dump
# s = secure deletion

# Practical examples
sudo chattr +i /etc/resolv.conf  # Prevent DNS changes
sudo chattr +a /var/log/app.log  # Append-only log
# Use: Enhanced file protection
```

---

## Process Management

### Viewing Processes

```bash
# List processes
ps                               # Current shell processes
ps aux                           # All processes (BSD style)
ps -ef                           # All processes (UNIX style)
ps -u username                   # User's processes
# Use: View running processes

# Process tree
pstree                           # Visual process tree
pstree -p                        # Include PIDs
pstree username                  # User's process tree
# Use: Understand process relationships

# Top (interactive)
top                              # Real-time process monitoring
htop                             # Enhanced top (if installed)
# Use: Monitor system resources in real-time

# Top shortcuts:
# M = Sort by memory
# P = Sort by CPU
# k = Kill process
# r = Renice process
# q = Quit

# Filter processes
ps aux | grep nginx
pgrep nginx                      # Get PIDs by name
pidof nginx                      # Get PIDs of running program
# Use: Find specific processes
```

### Process Information

```bash
# Detailed process info
ps -p PID -f                     # Full format for specific PID
ps -p PID -o pid,ppid,cmd,%mem,%cpu # Custom columns
# Use: Process details

# Process status
cat /proc/PID/status             # Detailed status
cat /proc/PID/cmdline            # Command line
cat /proc/PID/environ            # Environment variables
# Use: Deep process inspection

# Process files
lsof -p PID                      # Files opened by process
lsof -c nginx                    # Files opened by nginx
lsof /path/to/file               # Processes using file
lsof -i :80                      # Processes using port 80
# Use: Troubleshoot file and network usage

# Process threads
ps -eLf | grep PID               # List threads
top -H -p PID                    # Monitor threads
# Use: Thread-level monitoring
```

### Managing Processes

```bash
# Start process in background
command &
nohup command &                  # Ignore hangup signal
nohup command > output.log 2>&1 & # Redirect output
# Use: Run long-running tasks

# Foreground/background
jobs                             # List background jobs
fg %1                            # Bring job 1 to foreground
bg %1                            # Resume job 1 in background
Ctrl+Z                           # Suspend current process
# Use: Job control

# Kill processes
kill PID                         # Terminate gracefully (SIGTERM)
kill -9 PID                      # Force kill (SIGKILL)
kill -15 PID                     # Same as kill PID
killall process_name             # Kill all by name
pkill -f pattern                 # Kill by pattern
# Use: Stop processes

# Common signals:
# SIGTERM (15) = Graceful termination (default)
# SIGKILL (9)  = Force kill (cannot be caught)
# SIGHUP (1)   = Reload configuration
# SIGINT (2)   = Interrupt (Ctrl+C)
# SIGSTOP (19) = Pause process

# Reload configuration
kill -HUP PID                    # Reload config (nginx, apache)
systemctl reload nginx           # Preferred method for services
# Use: Apply configuration changes without restart

# Process priority (nice values: -20 to 19, lower = higher priority)
nice -n 10 command               # Start with nice value 10
renice -n 5 -p PID               # Change priority of running process
# Use: CPU priority management

# Practical examples
ps aux | grep defunct            # Find zombie processes
kill -9 $(pgrep -f "stuck_process") # Kill stuck process
pkill -f "python.*script.py"     # Kill Python script
# Use: Process troubleshooting
```

### Process Monitoring

```bash
# CPU usage
top -bn1 | head -20              # Snapshot of top processes
ps aux --sort=-%cpu | head       # Top CPU consumers
# Use: Identify CPU-intensive processes

# Memory usage
ps aux --sort=-%mem | head       # Top memory consumers
pmap PID                         # Memory map of process
# Use: Identify memory-intensive processes

# I/O monitoring
iotop                            # I/O usage by process (requires root)
pidstat -d 1                     # Disk I/O statistics
# Use: Identify I/O bottlenecks

# Network monitoring
nethogs                          # Network usage by process
iftop                            # Network bandwidth usage
# Use: Identify network-intensive processes

# System calls
strace -p PID                    # Trace system calls
strace -c command                # Count system calls
# Use: Debug process behavior

# Performance profiling
perf top                         # Real-time performance profiling
perf record -p PID               # Record performance data
perf report                      # Analyze recorded data
# Use: Performance optimization
```

---

## System Monitoring

### System Resources

```bash
# CPU information
lscpu                            # CPU architecture
cat /proc/cpuinfo                # Detailed CPU info
nproc                            # Number of processing units
# Use: Check CPU specifications

# Memory information
free -h                          # Human-readable memory usage
free -m                          # Memory in MB
cat /proc/meminfo                # Detailed memory info
vmstat 1                         # Virtual memory statistics (1 sec interval)
# Use: Monitor memory usage

# Disk usage
df -h                            # Disk space usage
df -i                            # Inode usage
du -sh /path                     # Directory size
du -h --max-depth=1 /path        # Subdirectory sizes
# Use: Check disk space

# Disk I/O
iostat                           # I/O statistics
iostat -x 1                      # Extended stats (1 sec interval)
iotop                            # I/O usage by process
# Use: Monitor disk performance

# System load
uptime                           # Load average
w                                # Load and logged-in users
cat /proc/loadavg                # Load average file
# Use: Check system load
# Load average: 1-min, 5-min, 15-min averages
# Rule of thumb: Load < number of CPU cores is good
```

### Hardware Information

```bash
# General hardware
lshw                             # List all hardware
lshw -short                      # Brief hardware list
dmidecode                        # DMI/SMBIOS information
# Use: Hardware inventory

# PCI devices
lspci                            # PCI devices
lspci -v                         # Verbose
lspci | grep -i vga              # Graphics card
lspci | grep -i ethernet         # Network card
# Use: Identify PCI hardware

# USB devices
lsusb                            # USB devices
lsusb -v                         # Verbose
# Use: Identify USB hardware

# Block devices
lsblk                            # Block devices (disks)
lsblk -f                         # Include filesystem info
blkid                            # Block device attributes
# Use: Disk identification

# SCSI/SATA devices
lsscsi                           # SCSI devices
# Use: Storage device information

# Network interfaces
ip link show                     # Network interfaces
lshw -C network                  # Network hardware
# Use: Network hardware details
```

### System Logs

```bash
# System log locations
/var/log/syslog                  # System log (Debian/Ubuntu)
/var/log/messages                # System log (RHEL/CentOS)
/var/log/auth.log                # Authentication log (Debian/Ubuntu)
/var/log/secure                  # Authentication log (RHEL/CentOS)
/var/log/kern.log                # Kernel log
/var/log/dmesg                   # Boot messages
/var/log/cron                    # Cron job log

# View logs
tail -f /var/log/syslog          # Follow system log
journalctl                       # Systemd journal
journalctl -f                    # Follow journal
journalctl -u nginx              # Service-specific logs
journalctl --since "1 hour ago"  # Recent logs
journalctl --since "2025-12-29"  # Logs from date
journalctl -p err                # Error priority and above
# Use: Monitor system events

# Boot logs
dmesg                            # Kernel ring buffer
dmesg | grep -i error            # Boot errors
journalctl -b                    # Current boot logs
journalctl -b -1                 # Previous boot logs
# Use: Troubleshoot boot issues

# Application logs
/var/log/apache2/                # Apache logs
/var/log/nginx/                  # Nginx logs
/var/log/mysql/                  # MySQL logs
~/.pm2/logs/                     # PM2 logs
# Use: Application-specific troubleshooting
```

### Performance Monitoring Tools

```bash
# System monitoring
top                              # Process monitor
htop                             # Enhanced top
atop                             # Advanced system monitor
glances                          # Cross-platform monitor
# Use: Real-time system monitoring

# Network monitoring
netstat -tulpn                   # Network connections
ss -tulpn                        # Socket statistics (faster)
iftop                            # Bandwidth usage
nethogs                          # Per-process bandwidth
nload                            # Network load
# Use: Network performance monitoring

# Disk monitoring
iotop                            # I/O by process
iostat -x 1                      # Disk I/O statistics
# Use: Disk performance monitoring

# Comprehensive monitoring
sar                              # System activity report
sar -u 1 10                      # CPU usage (1 sec, 10 times)
sar -r 1 10                      # Memory usage
sar -d 1 10                      # Disk activity
# Use: Historical performance data

# Process accounting
lastcomm                         # Recently executed commands
sa                               # Process accounting summary
# Use: Track command execution history
```

---

## Networking

### Network Configuration

```bash
# View network interfaces
ip addr show                     # IP addresses
ip link show                     # Link status
ifconfig                         # Legacy command (if installed)
# Use: Check network configuration

# View specific interface
ip addr show eth0
ip link show eth0
# Use: Interface-specific details

# Enable/disable interface
ip link set eth0 up
ip link set eth0 down
sudo ifup eth0                   # Debian/Ubuntu
sudo ifdown eth0                 # Debian/Ubuntu
# Use: Interface management

# Add/remove IP address
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip addr del 192.168.1.100/24 dev eth0
# Use: Temporary IP configuration

# Routing table
ip route show
route -n                         # Legacy command
# Use: View routing configuration

# Add/remove route
sudo ip route add 192.168.2.0/24 via 192.168.1.1
sudo ip route del 192.168.2.0/24
sudo ip route add default via 192.168.1.1 # Default gateway
# Use: Route management

# DNS configuration
cat /etc/resolv.conf             # DNS servers
systemd-resolve --status         # Systemd DNS status
# Use: DNS troubleshooting
```

### Network Testing

```bash
# Ping
ping google.com                  # Test connectivity
ping -c 4 google.com             # Send 4 packets
ping -i 0.5 google.com           # 0.5 second interval
# Use: Basic connectivity test

# Traceroute
traceroute google.com            # Trace route to host
tracepath google.com             # Alternative (no root needed)
mtr google.com                   # Continuous traceroute
# Use: Diagnose routing issues

# DNS lookup
nslookup google.com              # DNS query
dig google.com                   # Detailed DNS query
dig @8.8.8.8 google.com          # Query specific DNS server
host google.com                  # Simple DNS lookup
# Use: DNS troubleshooting

# Network connectivity
telnet google.com 80             # Test TCP connection
nc -zv google.com 80             # Netcat port check
curl -I https://google.com       # HTTP header check
wget --spider https://google.com # Check URL availability
# Use: Service connectivity testing

# Bandwidth testing
iperf3 -s                        # Start server
iperf3 -c server_ip              # Run client test
speedtest-cli                    # Internet speed test
# Use: Measure network performance
```

### Network Connections

```bash
# Active connections
netstat -tulpn                   # All listening ports
netstat -an                      # All connections
ss -tulpn                        # Socket statistics (modern)
ss -s                            # Summary statistics
# Use: Monitor network connections

# Specific protocol
netstat -t                       # TCP connections
netstat -u                       # UDP connections
netstat -x                       # Unix sockets
# Use: Protocol-specific monitoring

# Connection states
netstat -an | grep ESTABLISHED   # Established connections
netstat -an | grep LISTEN        # Listening ports
netstat -an | grep TIME_WAIT     # Connections in TIME_WAIT
# Use: Connection state analysis

# Process using port
lsof -i :80                      # Process on port 80
fuser 80/tcp                     # Process using TCP port 80
# Use: Identify port usage

# Network statistics
netstat -s                       # Protocol statistics
ss -s                            # Socket summary
# Use: Network performance analysis

# Practical examples
netstat -tulpn | grep :22        # Check SSH port
ss -tulpn | grep nginx           # Nginx connections
lsof -i -P -n | grep LISTEN      # All listening ports
# Use: Common network checks
```

### Firewall Management

```bash
# UFW (Uncomplicated Firewall - Ubuntu/Debian)
sudo ufw status                  # Check status
sudo ufw enable                  # Enable firewall
sudo ufw disable                 # Disable firewall
sudo ufw allow 22                # Allow SSH
sudo ufw allow 80/tcp            # Allow HTTP
sudo ufw allow from 192.168.1.0/24 # Allow subnet
sudo ufw deny 23                 # Deny telnet
sudo ufw delete allow 80         # Remove rule
sudo ufw reset                   # Reset to defaults
# Use: Simple firewall management

# Firewalld (RHEL/CentOS)
sudo firewall-cmd --state        # Check status
sudo firewall-cmd --list-all     # List all rules
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload       # Apply changes
sudo firewall-cmd --remove-service=http --permanent
# Use: Enterprise firewall management

# iptables (Low-level)
sudo iptables -L                 # List rules
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT # Allow SSH
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT # Allow HTTP
sudo iptables -A INPUT -j DROP   # Drop all other input
sudo iptables-save > /etc/iptables/rules.v4 # Save rules
sudo iptables-restore < /etc/iptables/rules.v4 # Restore rules
# Use: Advanced firewall configuration

# Common firewall scenarios
sudo ufw allow from 192.168.1.100 to any port 22 # SSH from specific IP
sudo ufw allow 443/tcp           # HTTPS
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" accept' --permanent
# Use: Secure network access
```

### Network Tools

```bash
# Download files
wget https://example.com/file.zip # Download file
wget -c https://example.com/file.zip # Resume download
wget -r -np https://example.com/  # Recursive download
curl -O https://example.com/file.zip # Download with curl
curl -L -o file.zip https://example.com/file.zip # Follow redirects
# Use: File downloads

# HTTP requests
curl https://api.example.com     # GET request
curl -X POST https://api.example.com # POST request
curl -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com
# Use: API testing

# Network scanning
nmap localhost                   # Scan local ports
nmap 192.168.1.0/24              # Scan subnet
nmap -sV 192.168.1.1             # Service version detection
nmap -O 192.168.1.1              # OS detection
# Use: Network discovery and security auditing

# Packet capture
tcpdump -i eth0                  # Capture on eth0
tcpdump -i eth0 port 80          # Capture HTTP traffic
tcpdump -i eth0 -w capture.pcap  # Save to file
tcpdump -r capture.pcap          # Read from file
# Use: Network troubleshooting and analysis

# ARP
arp -a                           # View ARP cache
arp -d 192.168.1.100             # Delete ARP entry
# Use: Layer 2 troubleshooting
```

---

## Package Management

### APT (Debian/Ubuntu)

```bash
# Update package list
sudo apt update                  # Refresh package index
# Use: Before installing/upgrading packages

# Upgrade packages
sudo apt upgrade                 # Upgrade installed packages
sudo apt full-upgrade            # Upgrade with dependency changes
sudo apt dist-upgrade            # Distribution upgrade (older command)
# Use: Keep system updated

# Install packages
sudo apt install package_name
sudo apt install package1 package2 package3
sudo apt install -y package_name # Auto-confirm
# Use: Install software

# Remove packages
sudo apt remove package_name     # Remove package (keep config)
sudo apt purge package_name      # Remove package and config
sudo apt autoremove              # Remove unused dependencies
# Use: Uninstall software

# Search packages
apt search keyword
apt-cache search keyword         # Alternative
# Use: Find packages

# Package information
apt show package_name
apt-cache show package_name      # Alternative
# Use: View package details

# List packages
apt list --installed             # Installed packages
apt list --upgradable            # Upgradable packages
dpkg -l                          # All installed packages
# Use: Package inventory

# Package files
dpkg -L package_name             # List files in package
dpkg -S /path/to/file            # Find package owning file
# Use: File-package mapping

# Fix broken packages
sudo apt --fix-broken install
sudo dpkg --configure -a
# Use: Repair package system

# Clean cache
sudo apt clean                   # Remove all cached packages
sudo apt autoclean               # Remove old cached packages
# Use: Free disk space
```

### YUM/DNF (RHEL/CentOS/Fedora)

```bash
# Update package list
sudo yum check-update            # Check for updates (YUM)
sudo dnf check-update            # Check for updates (DNF)
# Use: Before upgrading

# Upgrade packages
sudo yum update                  # Upgrade all packages
sudo dnf upgrade                 # Upgrade all packages (DNF)
sudo yum update package_name     # Upgrade specific package
# Use: Keep system updated

# Install packages
sudo yum install package_name
sudo dnf install package_name
sudo yum install -y package_name # Auto-confirm
# Use: Install software

# Remove packages
sudo yum remove package_name
sudo dnf remove package_name
sudo yum autoremove              # Remove unused dependencies
# Use: Uninstall software

# Search packages
yum search keyword
dnf search keyword
# Use: Find packages

# Package information
yum info package_name
dnf info package_name
# Use: View package details

# List packages
yum list installed
dnf list installed
rpm -qa                          # All installed packages
# Use: Package inventory

# Package files
rpm -ql package_name             # List files in package
rpm -qf /path/to/file            # Find package owning file
# Use: File-package mapping

# Clean cache
sudo yum clean all
sudo dnf clean all
# Use: Free disk space

# Repository management
yum repolist                     # List enabled repos
dnf repolist
sudo yum-config-manager --add-repo URL # Add repository
# Use: Manage package sources
```

### Snap (Universal)

```bash
# Install snap
sudo apt install snapd           # Ubuntu/Debian
sudo yum install snapd           # RHEL/CentOS
# Use: Enable snap support

# Install package
sudo snap install package_name
sudo snap install --classic package_name # Classic confinement
# Use: Install snap packages

# List snaps
snap list
# Use: View installed snaps

# Update snaps
sudo snap refresh                # Update all
sudo snap refresh package_name   # Update specific
# Use: Keep snaps updated

# Remove snap
sudo snap remove package_name
# Use: Uninstall snaps

# Find snaps
snap find keyword
# Use: Search for snaps
```

### Flatpak (Universal)

```bash
# Install flatpak
sudo apt install flatpak         # Ubuntu/Debian
sudo yum install flatpak         # RHEL/CentOS
# Use: Enable flatpak support

# Add Flathub repository
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
# Use: Access flatpak applications

# Install application
flatpak install flathub app_name
# Use: Install flatpak apps

# List applications
flatpak list
# Use: View installed flatpaks

# Update applications
flatpak update
# Use: Keep flatpaks updated

# Remove application
flatpak uninstall app_name
# Use: Uninstall flatpaks

# Run application
flatpak run app_name
# Use: Launch flatpak apps
```

### Building from Source

```bash
# Install build tools
sudo apt install build-essential # Ubuntu/Debian
sudo yum groupinstall "Development Tools" # RHEL/CentOS
# Use: Prepare for compilation

# Typical build process
./configure                      # Configure build
make                             # Compile
sudo make install                # Install
# Use: Install from source

# Alternative: checkinstall
sudo apt install checkinstall
sudo checkinstall                # Creates .deb package
# Use: Create package from source

# Clean build
make clean                       # Remove build files
make distclean                   # Remove all generated files
# Use: Clean build directory
```

---

## Shell Scripting

### Bash Basics

```bash
#!/bin/bash
# Shebang - specifies interpreter

# Comments
# This is a single-line comment

# Variables
NAME="John"
AGE=30
readonly CONSTANT="Cannot change"  # Read-only variable

# Using variables
echo "Hello, $NAME"
echo "Hello, ${NAME}"              # Preferred for clarity

# Command substitution
CURRENT_DATE=$(date)
FILES=$(ls)

# Arithmetic
SUM=$((5 + 3))
RESULT=$((AGE * 2))

# User input
read -p "Enter your name: " USERNAME
read -sp "Enter password: " PASSWORD  # Silent input

# Arrays
FRUITS=("apple" "banana" "orange")
echo ${FRUITS[0]}                 # First element
echo ${FRUITS[@]}                 # All elements
echo ${#FRUITS[@]}                # Array length

# Associative arrays (Bash 4+)
declare -A PERSON
PERSON[name]="John"
PERSON[age]=30
echo ${PERSON[name]}
```

### Control Structures

```bash
# If statement
if [ "$AGE" -gt 18 ]; then
    echo "Adult"
elif [ "$AGE" -eq 18 ]; then
    echo "Just turned adult"
else
    echo "Minor"
fi

# Comparison operators:
# -eq : equal
# -ne : not equal
# -gt : greater than
# -lt : less than
# -ge : greater than or equal
# -le : less than or equal

# String comparison
if [ "$NAME" = "John" ]; then
    echo "Hello John"
fi

if [ -z "$NAME" ]; then          # Empty string
    echo "Name is empty"
fi

if [ -n "$NAME" ]; then          # Non-empty string
    echo "Name is not empty"
fi

# File tests
if [ -f "/path/file.txt" ]; then # File exists
    echo "File exists"
fi

if [ -d "/path/dir" ]; then      # Directory exists
    echo "Directory exists"
fi

if [ -r "/path/file.txt" ]; then # Readable
    echo "File is readable"
fi

if [ -w "/path/file.txt" ]; then # Writable
    echo "File is writable"
fi

if [ -x "/path/script.sh" ]; then # Executable
    echo "File is executable"
fi

# Logical operators
if [ "$AGE" -gt 18 ] && [ "$NAME" = "John" ]; then
    echo "Adult named John"
fi

if [ "$AGE" -lt 18 ] || [ "$NAME" = "Jane" ]; then
    echo "Minor or Jane"
fi

# Case statement
case "$1" in
    start)
        echo "Starting service"
        ;;
    stop)
        echo "Stopping service"
        ;;
    restart)
        echo "Restarting service"
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

### Loops

```bash
# For loop
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

# For loop with range
for i in {1..10}; do
    echo "Number: $i"
done

# For loop with step
for i in {0..100..10}; do
    echo "Number: $i"
done

# For loop over files
for file in *.txt; do
    echo "Processing $file"
done

# C-style for loop
for ((i=0; i<10; i++)); do
    echo "Count: $i"
done

# While loop
COUNT=0
while [ $COUNT -lt 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))
done

# Until loop
COUNT=0
until [ $COUNT -ge 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))
done

# Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < file.txt

# Break and continue
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        continue                  # Skip 5
    fi
    if [ $i -eq 8 ]; then
        break                     # Stop at 8
    fi
    echo $i
done
```

### Functions

```bash
# Basic function
greet() {
    echo "Hello, $1"
}
greet "John"

# Function with return value
add() {
    local result=$(($1 + $2))
    echo $result
}
SUM=$(add 5 3)
echo "Sum: $SUM"

# Function with return status
check_file() {
    if [ -f "$1" ]; then
        return 0                  # Success
    else
        return 1                  # Failure
    fi
}

if check_file "/etc/passwd"; then
    echo "File exists"
fi

# Local variables
my_function() {
    local LOCAL_VAR="local"
    GLOBAL_VAR="global"
}

# Function with multiple arguments
process() {
    echo "First arg: $1"
    echo "Second arg: $2"
    echo "All args: $@"
    echo "Number of args: $#"
}
process arg1 arg2 arg3
```

### Error Handling

```bash
# Exit on error
set -e                            # Exit on any error
set -u                            # Exit on undefined variable
set -o pipefail                   # Exit on pipe failure

# Trap errors
trap 'echo "Error on line $LINENO"' ERR

# Trap signals
trap 'cleanup; exit' INT TERM EXIT

cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/tempfile
}

# Check command success
if command; then
    echo "Success"
else
    echo "Failed"
    exit 1
fi

# Check exit status
command
if [ $? -eq 0 ]; then
    echo "Success"
fi

# Logical operators
command1 && command2              # Run command2 if command1 succeeds
command1 || command2              # Run command2 if command1 fails

# Redirect errors
command 2>/dev/null               # Suppress errors
command 2>&1                      # Redirect stderr to stdout
command &>/dev/null               # Suppress all output
```

### Practical Script Examples

```bash
# System backup script
#!/bin/bash
set -e

BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)
SOURCE="/home /etc /var/www"

echo "Starting backup at $(date)"

for dir in $SOURCE; do
    BACKUP_FILE="$BACKUP_DIR/$(basename $dir)-$DATE.tar.gz"
    tar -czf "$BACKUP_FILE" "$dir"
    echo "Backed up $dir to $BACKUP_FILE"
done

echo "Backup completed at $(date)"

# Log rotation script
#!/bin/bash
LOG_DIR="/var/log/myapp"
MAX_SIZE=100M
ARCHIVE_DIR="$LOG_DIR/archive"

mkdir -p "$ARCHIVE_DIR"

for log in "$LOG_DIR"/*.log; do
    SIZE=$(du -m "$log" | cut -f1)
    if [ $SIZE -gt 100 ]; then
        TIMESTAMP=$(date +%Y%m%d-%H%M%S)
        gzip -c "$log" > "$ARCHIVE_DIR/$(basename $log)-$TIMESTAMP.gz"
        > "$log"  # Truncate log
        echo "Rotated $log"
    fi
done

# Service health check
#!/bin/bash
SERVICE="nginx"
EMAIL="admin@example.com"

if ! systemctl is-active --quiet $SERVICE; then
    echo "$SERVICE is down, attempting restart"
    systemctl restart $SERVICE
    
    if systemctl is-active --quiet $SERVICE; then
        echo "$SERVICE restarted successfully" | mail -s "Service Recovered" $EMAIL
    else
        echo "$SERVICE failed to restart" | mail -s "Service Down" $EMAIL
    fi
fi

# Disk space monitor
#!/bin/bash
THRESHOLD=80
EMAIL="admin@example.com"

df -H | grep -vE '^Filesystem|tmpfs|cdrom' | awk '{print $5 " " $1}' | while read output; do
    USAGE=$(echo $output | awk '{print $1}' | sed 's/%//g')
    PARTITION=$(echo $output | awk '{print $2}')
    
    if [ $USAGE -ge $THRESHOLD ]; then
        echo "Disk $PARTITION is ${USAGE}% full" | mail -s "Disk Space Alert" $EMAIL
    fi
done
```

---

## System Services

### Systemd (Modern)

```bash
# Service management
sudo systemctl start service_name    # Start service
sudo systemctl stop service_name     # Stop service
sudo systemctl restart service_name  # Restart service
sudo systemctl reload service_name   # Reload config
sudo systemctl status service_name   # Check status
# Use: Control system services

# Enable/disable services
sudo systemctl enable service_name   # Start on boot
sudo systemctl disable service_name  # Don't start on boot
sudo systemctl is-enabled service_name # Check if enabled
# Use: Boot configuration

# List services
systemctl list-units --type=service  # All services
systemctl list-units --type=service --state=running # Running services
systemctl list-units --type=service --state=failed # Failed services
# Use: Service inventory

# View logs
journalctl -u service_name           # Service logs
journalctl -u service_name -f        # Follow logs
journalctl -u service_name --since "1 hour ago"
journalctl -u service_name --since "2025-12-29"
# Use: Service troubleshooting

# Service dependencies
systemctl list-dependencies service_name
# Use: Understand service relationships

# Reload systemd
sudo systemctl daemon-reload         # After editing unit files
# Use: Apply configuration changes
```

### Creating Systemd Service

```bash
# Create service file: /etc/systemd/system/myapp.service
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/start.sh
ExecStop=/opt/myapp/stop.sh
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target

# Service types:
# simple: Main process (default)
# forking: Forks and parent exits
# oneshot: Runs once and exits
# notify: Sends notification when ready

# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
# Use: Custom service deployment
```

### Init.d (Legacy)

```bash
# Service management (older systems)
sudo service nginx start
sudo service nginx stop
sudo service nginx restart
sudo service nginx status
# Use: Legacy service control

# Alternative syntax
sudo /etc/init.d/nginx start
sudo /etc/init.d/nginx stop
# Use: Direct init script execution

# Enable/disable (chkconfig - RHEL/CentOS)
sudo chkconfig nginx on
sudo chkconfig nginx off
sudo chkconfig --list
# Use: Legacy boot configuration

# Enable/disable (update-rc.d - Debian/Ubuntu)
sudo update-rc.d nginx defaults
sudo update-rc.d nginx remove
# Use: Legacy boot configuration
```

### Cron Jobs

```bash
# Edit crontab
crontab -e                        # Edit user crontab
sudo crontab -e                   # Edit root crontab
crontab -l                        # List crontab entries
crontab -r                        # Remove crontab
# Use: Schedule tasks

# Crontab syntax:
# * * * * * command
# │ │ │ │ │
# │ │ │ │ └─── Day of week (0-7, Sunday=0 or 7)
# │ │ │ └───── Month (1-12)
# │ │ └─────── Day of month (1-31)
# │ └───────── Hour (0-23)
# └─────────── Minute (0-59)

# Examples:
# 0 2 * * * /backup.sh           # Daily at 2 AM
# */15 * * * * /check.sh         # Every 15 minutes
# 0 0 * * 0 /weekly.sh           # Weekly on Sunday at midnight
# 0 0 1 * * /monthly.sh          # Monthly on 1st at midnight
# 0 9-17 * * 1-5 /workday.sh     # Weekdays 9 AM to 5 PM

# Special strings:
# @reboot /startup.sh            # Run at startup
# @daily /daily.sh               # Run daily
# @weekly /weekly.sh             # Run weekly
# @monthly /monthly.sh           # Run monthly
# @yearly /yearly.sh             # Run yearly

# System-wide cron
/etc/cron.daily/                  # Daily scripts
/etc/cron.weekly/                 # Weekly scripts
/etc/cron.monthly/                # Monthly scripts
/etc/cron.d/                      # Additional cron files
# Use: System maintenance tasks

# Cron logs
grep CRON /var/log/syslog         # Debian/Ubuntu
grep CRON /var/log/cron           # RHEL/CentOS
# Use: Verify cron execution
```

### At (One-time Tasks)

```bash
# Schedule one-time task
echo "command" | at now + 1 hour
echo "command" | at 10:00 AM
echo "command" | at 10:00 AM tomorrow
echo "command" | at 10:00 AM 12/31/2025
# Use: One-time scheduled tasks

# Interactive at
at 10:00 AM
> command1
> command2
> Ctrl+D
# Use: Multi-command scheduling

# List scheduled tasks
atq
# Use: View pending tasks

# Remove scheduled task
atrm job_number
# Use: Cancel scheduled tasks

# View task details
at -c job_number
# Use: Inspect scheduled task
```

---

## Log Management

### Log Locations

```bash
# System logs
/var/log/syslog                   # System log (Debian/Ubuntu)
/var/log/messages                 # System log (RHEL/CentOS)
/var/log/auth.log                 # Authentication (Debian/Ubuntu)
/var/log/secure                   # Authentication (RHEL/CentOS)
/var/log/kern.log                 # Kernel messages
/var/log/dmesg                    # Boot messages
/var/log/boot.log                 # Boot log

# Service logs
/var/log/apache2/                 # Apache
/var/log/nginx/                   # Nginx
/var/log/mysql/                   # MySQL
/var/log/postgresql/              # PostgreSQL

# Application logs
/var/log/cron                     # Cron jobs
/var/log/mail.log                 # Mail server
/var/log/cups/                    # Printing
```

### Viewing Logs

```bash
# Real-time monitoring
tail -f /var/log/syslog
tail -F /var/log/syslog           # Follow with retry
multitail /var/log/syslog /var/log/auth.log # Multiple logs
# Use: Live log monitoring

# Search logs
grep "error" /var/log/syslog
grep -i "failed" /var/log/auth.log # Case-insensitive
zgrep "error" /var/log/syslog.*.gz # Search compressed logs
# Use: Find specific events

# Time-based filtering
grep "Dec 29" /var/log/syslog
awk '/Dec 29 10:00/,/Dec 29 11:00/' /var/log/syslog
# Use: Analyze specific time periods

# Log statistics
grep "error" /var/log/syslog | wc -l # Count errors
grep "error" /var/log/syslog | awk '{print $5}' | sort | uniq -c
# Use: Log analysis
```

### Journalctl (Systemd)

```bash
# View all logs
journalctl
journalctl -f                     # Follow logs
journalctl -n 100                 # Last 100 entries
# Use: System log viewing

# Service-specific logs
journalctl -u nginx
journalctl -u nginx -f            # Follow service logs
# Use: Service troubleshooting

# Time-based filtering
journalctl --since "2025-12-29"
journalctl --since "1 hour ago"
journalctl --since "10:00" --until "11:00"
journalctl --since yesterday
# Use: Time-specific analysis

# Priority filtering
journalctl -p err                 # Error and above
journalctl -p warning             # Warning and above
# Priorities: emerg, alert, crit, err, warning, notice, info, debug
# Use: Filter by severity

# Boot logs
journalctl -b                     # Current boot
journalctl -b -1                  # Previous boot
journalctl --list-boots           # List all boots
# Use: Boot troubleshooting

# Kernel messages
journalctl -k                     # Kernel messages
journalctl -k -b                  # Kernel messages from current boot
# Use: Kernel troubleshooting

# Output format
journalctl -o json                # JSON format
journalctl -o json-pretty         # Pretty JSON
journalctl -o verbose             # Verbose output
# Use: Structured log analysis

# Disk usage
journalctl --disk-usage           # Show disk usage
sudo journalctl --vacuum-size=100M # Limit to 100MB
sudo journalctl --vacuum-time=30d # Keep 30 days
# Use: Manage log storage
```

### Log Rotation

```bash
# Logrotate configuration
/etc/logrotate.conf               # Main config
/etc/logrotate.d/                 # Service-specific configs

# Example logrotate config: /etc/logrotate.d/myapp
/var/log/myapp/*.log {
    daily                         # Rotate daily
    rotate 7                      # Keep 7 days
    compress                      # Compress old logs
    delaycompress                 # Compress after 1 rotation
    missingok                     # Don't error if missing
    notifempty                    # Don't rotate if empty
    create 0640 www-data www-data # Create new log with permissions
    sharedscripts                 # Run scripts once for all logs
    postrotate
        systemctl reload nginx > /dev/null
    endscript
}

# Rotation frequencies:
# daily, weekly, monthly, yearly

# Size-based rotation:
size 100M                         # Rotate when > 100MB

# Test logrotate
sudo logrotate -d /etc/logrotate.conf # Debug mode (dry run)
sudo logrotate -f /etc/logrotate.conf # Force rotation
# Use: Verify rotation configuration

# Manual rotation
sudo logrotate /etc/logrotate.d/myapp
# Use: Trigger rotation manually
```

### Rsyslog Configuration

```bash
# Rsyslog config
/etc/rsyslog.conf                 # Main config
/etc/rsyslog.d/                   # Additional configs

# Log to specific file
# /etc/rsyslog.d/myapp.conf
if $programname == 'myapp' then /var/log/myapp.log
& stop

# Log to remote server
*.* @192.168.1.100:514            # UDP
*.* @@192.168.1.100:514           # TCP

# Restart rsyslog
sudo systemctl restart rsyslog
# Use: Apply configuration changes

# Test logging
logger "Test message"
logger -p local0.info "Test message with facility"
# Use: Verify logging configuration
```

---

## Disk Management

### Disk Information

```bash
# List block devices
lsblk                             # Tree view
lsblk -f                          # Include filesystem
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT # Custom columns
# Use: View disk layout

# Disk partitions
sudo fdisk -l                     # List all partitions
sudo parted -l                    # Alternative (GPT support)
# Use: Partition information

# Filesystem information
df -h                             # Disk usage (human-readable)
df -i                             # Inode usage
df -T                             # Include filesystem type
# Use: Check disk space

# Disk usage by directory
du -sh /path                      # Directory size
du -h --max-depth=1 /path         # Subdirectory sizes
du -ah /path | sort -rh | head -20 # Top 20 largest
# Use: Identify space usage

# Inode usage
df -i                             # Inode statistics
find /path -xdev -printf '%h\n' | sort | uniq -c | sort -k 1 -n
# Use: Troubleshoot inode exhaustion
```

### Partitioning

```bash
# Fdisk (MBR)
sudo fdisk /dev/sdb
# Commands:
# n = new partition
# d = delete partition
# p = print partition table
# w = write changes
# q = quit without saving
# Use: Create/modify MBR partitions

# Parted (GPT)
sudo parted /dev/sdb
# Commands:
# mklabel gpt                     # Create GPT partition table
# mkpart primary 0% 50%           # Create partition
# print                           # Show partitions
# rm 1                            # Remove partition 1
# quit                            # Exit
# Use: Create/modify GPT partitions

# Create partition non-interactively
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart primary 0% 100%
# Use: Automated partitioning
```

### Filesystem Operations

```bash
# Create filesystem
sudo mkfs.ext4 /dev/sdb1          # ext4
sudo mkfs.xfs /dev/sdb1           # XFS
sudo mkfs.btrfs /dev/sdb1         # Btrfs
sudo mkfs.vfat /dev/sdb1          # FAT32
# Use: Format partitions

# Filesystem check
sudo fsck /dev/sdb1               # Check and repair
sudo fsck -y /dev/sdb1            # Auto-repair
sudo e2fsck -f /dev/sdb1          # Force check (ext4)
# Use: Repair filesystem errors
# WARNING: Unmount before fsck!

# Filesystem label
sudo e2label /dev/sdb1 "MyDisk"   # Set label (ext4)
sudo xfs_admin -L "MyDisk" /dev/sdb1 # Set label (XFS)
# Use: Label filesystems

# Filesystem UUID
sudo blkid /dev/sdb1              # Show UUID
sudo tune2fs -U random /dev/sdb1  # Generate new UUID (ext4)
# Use: Manage UUIDs
```

### Mounting

```bash
# Mount filesystem
sudo mount /dev/sdb1 /mnt
sudo mount -t ext4 /dev/sdb1 /mnt # Specify filesystem type
sudo mount -o ro /dev/sdb1 /mnt   # Read-only
# Use: Access filesystem

# Unmount
sudo umount /mnt
sudo umount /dev/sdb1
sudo umount -l /mnt               # Lazy unmount
sudo umount -f /mnt               # Force unmount
# Use: Safely disconnect filesystem

# List mounts
mount                             # All mounts
mount | grep /dev/sdb1            # Specific device
cat /proc/mounts                  # Kernel mount info
# Use: Verify mounts

# Persistent mounts (/etc/fstab)
# Format: device mountpoint fstype options dump pass
/dev/sdb1 /data ext4 defaults 0 2
UUID=xxx /data ext4 defaults 0 2  # Using UUID (preferred)

# Mount options:
# defaults: rw,suid,dev,exec,auto,nouser,async
# ro: read-only
# noexec: prevent execution
# nosuid: ignore SUID bits
# noatime: don't update access time (performance)

# Test fstab
sudo mount -a                     # Mount all in fstab
sudo findmnt --verify             # Verify fstab
# Use: Validate fstab configuration

# Remount
sudo mount -o remount,rw /mnt     # Remount as read-write
# Use: Change mount options without unmounting
```

### LVM (Logical Volume Manager)

```bash
# Physical Volume (PV)
sudo pvcreate /dev/sdb1           # Create PV
sudo pvdisplay                    # Show PVs
sudo pvs                          # Brief PV info
# Use: Initialize disks for LVM

# Volume Group (VG)
sudo vgcreate vg_data /dev/sdb1   # Create VG
sudo vgextend vg_data /dev/sdc1   # Add PV to VG
sudo vgdisplay                    # Show VGs
sudo vgs                          # Brief VG info
# Use: Group physical volumes

# Logical Volume (LV)
sudo lvcreate -L 10G -n lv_data vg_data # Create 10GB LV
sudo lvcreate -l 100%FREE -n lv_data vg_data # Use all space
sudo lvdisplay                    # Show LVs
sudo lvs                          # Brief LV info
# Use: Create logical volumes

# Resize LV
sudo lvextend -L +5G /dev/vg_data/lv_data # Add 5GB
sudo lvextend -l +100%FREE /dev/vg_data/lv_data # Use all free space
sudo resize2fs /dev/vg_data/lv_data # Resize ext4 filesystem
sudo xfs_growfs /mnt/data         # Resize XFS filesystem
# Use: Expand storage

# Remove LV
sudo lvremove /dev/vg_data/lv_data
# Use: Delete logical volume

# Snapshots
sudo lvcreate -L 1G -s -n lv_data_snap /dev/vg_data/lv_data
sudo lvremove /dev/vg_data/lv_data_snap # Remove snapshot
# Use: Create point-in-time backups
```

### RAID

```bash
# Create RAID
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
# RAID levels:
# 0: Striping (performance, no redundancy)
# 1: Mirroring (redundancy)
# 5: Striping with parity (redundancy, min 3 disks)
# 6: Double parity (redundancy, min 4 disks)
# 10: Mirrored stripes (performance + redundancy, min 4 disks)

# View RAID status
cat /proc/mdstat
sudo mdadm --detail /dev/md0
# Use: Monitor RAID health

# Add disk to RAID
sudo mdadm --add /dev/md0 /dev/sdd1
# Use: Replace failed disk

# Remove disk from RAID
sudo mdadm --fail /dev/md0 /dev/sdb1
sudo mdadm --remove /dev/md0 /dev/sdb1
# Use: Remove failed disk

# Save RAID configuration
sudo mdadm --detail --scan >> /etc/mdadm/mdadm.conf
sudo update-initramfs -u
# Use: Persist RAID configuration
```

### Swap Management

```bash
# View swap
free -h
swapon --show
cat /proc/swaps
# Use: Check swap usage

# Create swap file
sudo fallocate -l 2G /swapfile    # Create 2GB file
sudo dd if=/dev/zero of=/swapfile bs=1M count=2048 # Alternative
sudo chmod 600 /swapfile          # Secure permissions
sudo mkswap /swapfile             # Format as swap
sudo swapon /swapfile             # Enable swap
# Use: Add swap space

# Persistent swap (/etc/fstab)
/swapfile none swap sw 0 0

# Remove swap
sudo swapoff /swapfile
sudo rm /swapfile
# Use: Remove swap space

# Swap priority
sudo swapon -p 10 /swapfile       # Set priority (higher = preferred)
# Use: Control swap usage order

# Swappiness (0-100, lower = less swap)
cat /proc/sys/vm/swappiness       # View current
sudo sysctl vm.swappiness=10      # Temporary change
# /etc/sysctl.conf: vm.swappiness=10 # Persistent
# Use: Tune swap behavior
```

---

## Backup and Recovery

### Backup Strategies

```bash
# Full backup with tar
tar -czf backup-$(date +%Y%m%d).tar.gz /path/to/data
tar -czf backup.tar.gz --exclude='*.log' /path/to/data
# Use: Create compressed archives

# Incremental backup with tar
tar -czf full-backup.tar.gz -g snapshot.file /path/to/data
tar -czf incremental-backup.tar.gz -g snapshot.file /path/to/data
# Use: Backup only changed files

# Restore from tar
tar -xzf backup.tar.gz -C /restore/path
tar -xzf backup.tar.gz file.txt  # Extract specific file
# Use: Restore from archive

# Rsync backup
rsync -av /source/ /destination/
rsync -av --delete /source/ /destination/ # Mirror (delete removed files)
rsync -avz /source/ user@remote:/destination/ # Remote backup
rsync -av --exclude='*.log' /source/ /destination/ # Exclude files
# Use: Efficient incremental backups

# Rsync with progress
rsync -av --progress /source/ /destination/
# Use: Monitor backup progress

# Rsync over SSH
rsync -avz -e ssh /source/ user@remote:/destination/
# Use: Secure remote backups
```

### Database Backups

```bash
# MySQL/MariaDB backup
mysqldump -u root -p database_name > backup.sql
mysqldump -u root -p --all-databases > all_databases.sql
mysqldump -u root -p database_name table_name > table_backup.sql
# Use: Backup MySQL databases

# MySQL restore
mysql -u root -p database_name < backup.sql
mysql -u root -p < all_databases.sql
# Use: Restore MySQL databases

# PostgreSQL backup
pg_dump database_name > backup.sql
pg_dumpall > all_databases.sql
# Use: Backup PostgreSQL databases

# PostgreSQL restore
psql database_name < backup.sql
psql -f all_databases.sql
# Use: Restore PostgreSQL databases

# MongoDB backup
mongodump --db database_name --out /backup/
mongodump --out /backup/        # All databases
# Use: Backup MongoDB databases

# MongoDB restore
mongorestore --db database_name /backup/database_name/
mongorestore /backup/           # All databases
# Use: Restore MongoDB databases
```

### System Backup Tools

```bash
# Duplicity (encrypted backups)
duplicity /source file:///backup
duplicity /source rsync://user@remote//backup
duplicity restore file:///backup /restore
# Use: Encrypted incremental backups

# Restic (modern backup tool)
restic init --repo /backup
restic backup /source --repo /backup
restic snapshots --repo /backup
restic restore latest --repo /backup --target /restore
# Use: Deduplicating backups

# Borg (deduplicating backup)
borg init --encryption=repokey /backup
borg create /backup::archive-name /source
borg list /backup
borg extract /backup::archive-name
# Use: Efficient deduplicating backups

# Bacula (enterprise backup)
# Complex setup, requires server/client configuration
# Use: Enterprise-grade backup solution
```

### Disaster Recovery

```bash
# System image with dd
sudo dd if=/dev/sda of=/backup/sda.img bs=4M status=progress
sudo dd if=/dev/sda of=/backup/sda.img.gz bs=4M conv=sync,noerror | gzip -c
# Use: Complete disk image

# Restore from dd image
sudo dd if=/backup/sda.img of=/dev/sda bs=4M status=progress
# Use: Restore entire disk

# Clonezilla (disk cloning)
# Boot from Clonezilla live CD/USB
# Use: User-friendly disk cloning

# System rescue
# Boot from SystemRescue live CD/USB
# Use: Recover unbootable systems

# Backup MBR
sudo dd if=/dev/sda of=/backup/mbr.img bs=512 count=1
# Use: Backup boot sector

# Restore MBR
sudo dd if=/backup/mbr.img of=/dev/sda bs=512 count=1
# Use: Restore boot sector

# Backup partition table
sudo sfdisk -d /dev/sda > /backup/partition_table.txt
# Use: Save partition layout

# Restore partition table
sudo sfdisk /dev/sda < /backup/partition_table.txt
# Use: Restore partition layout
```

### Backup Automation

```bash
# Backup script example
#!/bin/bash
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)
SOURCE="/home /etc /var/www"

for dir in $SOURCE; do
    tar -czf "$BACKUP_DIR/$(basename $dir)-$DATE.tar.gz" "$dir"
done

# Keep only last 7 days
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +7 -delete

# Schedule with cron
# Daily at 2 AM
0 2 * * * /usr/local/bin/backup.sh

# Backup verification
#!/bin/bash
BACKUP_FILE="$1"

if tar -tzf "$BACKUP_FILE" > /dev/null 2>&1; then
    echo "Backup $BACKUP_FILE is valid"
else
    echo "Backup $BACKUP_FILE is corrupted"
    exit 1
fi

# Remote backup sync
#!/bin/bash
rsync -avz --delete /backup/ user@remote:/remote_backup/
# Use: Offsite backup replication
```

---

## Security

### User Security

```bash
# Password policies
sudo chage -M 90 username         # Max 90 days
sudo chage -m 7 username          # Min 7 days between changes
sudo chage -W 14 username         # Warn 14 days before expiry
sudo chage -I 30 username         # Lock after 30 days of inactivity
# Use: Enforce password policies

# Password complexity (/etc/pam.d/common-password)
password requisite pam_pwquality.so retry=3 minlen=12 difok=3
# Use: Require strong passwords

# Lock/unlock user
sudo passwd -l username           # Lock account
sudo passwd -u username           # Unlock account
sudo usermod -L username          # Lock (alternative)
sudo usermod -U username          # Unlock (alternative)
# Use: Disable/enable accounts

# Failed login attempts
faillog -u username               # View failed logins
faillog -r -u username            # Reset failed login count
# Use: Monitor login attempts

# Last login
last                              # Login history
last username                     # User-specific history
lastb                             # Failed login attempts
# Use: Audit user access

# Currently logged in
who                               # Logged in users
w                                 # Detailed user activity
users                             # Simple user list
# Use: Monitor active sessions
```

### SSH Security

```bash
# SSH key generation
ssh-keygen -t rsa -b 4096         # RSA 4096-bit
ssh-keygen -t ed25519             # Ed25519 (modern, secure)
# Use: Create SSH keys

# Copy SSH key to server
ssh-copy-id user@server
cat ~/.ssh/id_rsa.pub | ssh user@server "cat >> ~/.ssh/authorized_keys"
# Use: Enable key-based authentication

# SSH config (/etc/ssh/sshd_config)
PermitRootLogin no                # Disable root login
PasswordAuthentication no         # Disable password auth
PubkeyAuthentication yes          # Enable key auth
Port 2222                         # Change default port
MaxAuthTries 3                    # Limit auth attempts
ClientAliveInterval 300           # Keep-alive interval
ClientAliveCountMax 2             # Max keep-alive messages

# Restart SSH
sudo systemctl restart sshd
# Use: Apply SSH configuration

# SSH tunneling
ssh -L 8080:localhost:80 user@server # Local port forwarding
ssh -R 8080:localhost:80 user@server # Remote port forwarding
ssh -D 1080 user@server           # SOCKS proxy
# Use: Secure port forwarding

# SSH without password prompt
ssh -o StrictHostKeyChecking=no user@server
# Use: Automated scripts (use with caution)
```

### Firewall Security

```bash
# UFW rules
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow from 192.168.1.0/24 to any port 22
sudo ufw deny from 1.2.3.4
# Use: Basic firewall rules

# UFW application profiles
sudo ufw app list
sudo ufw allow 'Nginx Full'
sudo ufw allow 'OpenSSH'
# Use: Service-based rules

# iptables rules
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -j DROP
# Use: Advanced firewall rules

# Save iptables
sudo iptables-save > /etc/iptables/rules.v4
sudo ip6tables-save > /etc/iptables/rules.v6
# Use: Persist firewall rules

# Rate limiting (prevent brute force)
sudo ufw limit 22/tcp
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 4 -j DROP
# Use: Protect against brute force attacks
```

### File Security

```bash
# Find SUID/SGID files
find / -perm -4000 2>/dev/null    # SUID files
find / -perm -2000 2>/dev/null    # SGID files
# Use: Security audit for privileged files

# Find world-writable files
find / -perm -002 -type f 2>/dev/null
find / -perm -002 -type d 2>/dev/null
# Use: Identify insecure permissions

# Find files without owner
find / -nouser -o -nogroup 2>/dev/null
# Use: Identify orphaned files

# Secure sensitive files
sudo chmod 600 /etc/ssh/ssh_host_*_key # SSH private keys
sudo chmod 644 /etc/ssh/ssh_host_*_key.pub # SSH public keys
sudo chmod 600 ~/.ssh/id_rsa      # User SSH private key
sudo chmod 644 ~/.ssh/id_rsa.pub  # User SSH public key
sudo chmod 700 ~/.ssh             # SSH directory
sudo chmod 600 ~/.ssh/authorized_keys # Authorized keys
# Use: Secure SSH keys

# File integrity monitoring
sudo apt install aide             # Install AIDE
sudo aideinit                     # Initialize database
sudo aide --check                 # Check for changes
# Use: Detect unauthorized file changes

# Immutable files
sudo chattr +i /etc/resolv.conf   # Make immutable
sudo chattr -i /etc/resolv.conf   # Remove immutable
# Use: Protect critical files
```

### SELinux / AppArmor

```bash
# SELinux (RHEL/CentOS)
getenforce                        # Check SELinux status
sestatus                          # Detailed status
sudo setenforce 0                 # Disable temporarily
sudo setenforce 1                 # Enable temporarily

# Permanent SELinux config (/etc/selinux/config)
SELINUX=enforcing                 # Enforcing mode
SELINUX=permissive                # Permissive mode (log only)
SELINUX=disabled                  # Disabled

# SELinux contexts
ls -Z /path                       # View contexts
ps -eZ                            # Process contexts
chcon -t httpd_sys_content_t /var/www/html # Change context
restorecon -Rv /var/www/html      # Restore default contexts
# Use: SELinux troubleshooting

# SELinux booleans
getsebool -a                      # List all booleans
setsebool httpd_can_network_connect on # Enable boolean
setsebool -P httpd_can_network_connect on # Persistent
# Use: Configure SELinux policies

# AppArmor (Ubuntu/Debian)
sudo aa-status                    # Check AppArmor status
sudo aa-enforce /etc/apparmor.d/usr.bin.firefox # Enforce profile
sudo aa-complain /etc/apparmor.d/usr.bin.firefox # Complain mode
sudo aa-disable /etc/apparmor.d/usr.bin.firefox # Disable profile
# Use: AppArmor management

# AppArmor logs
sudo grep apparmor /var/log/syslog
# Use: AppArmor troubleshooting
```

### Security Scanning

```bash
# Port scanning
nmap localhost                    # Scan local ports
nmap -sV localhost                # Service version detection
nmap -p- localhost                # Scan all ports
# Use: Identify open ports

# Vulnerability scanning
sudo apt install lynis
sudo lynis audit system           # System audit
# Use: Security assessment

# Rootkit detection
sudo apt install rkhunter chkrootkit
sudo rkhunter --check
sudo chkrootkit
# Use: Detect rootkits

# Malware scanning
sudo apt install clamav
sudo freshclam                    # Update virus database
sudo clamscan -r /home            # Scan directory
# Use: Scan for malware

# Network intrusion detection
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo fail2ban-client status       # Check status
sudo fail2ban-client status sshd  # SSH jail status
# Use: Protect against brute force attacks
```

---

## Performance Tuning

### CPU Optimization

```bash
# CPU governor
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
# Governors: performance, powersave, ondemand, conservative
# Use: Control CPU frequency scaling

# CPU affinity
taskset -c 0,1 command            # Run on CPUs 0 and 1
taskset -cp 0,1 PID               # Set affinity for running process
# Use: Pin processes to specific CPUs

# Disable CPU cores
echo 0 | sudo tee /sys/devices/system/cpu/cpu1/online
echo 1 | sudo tee /sys/devices/system/cpu/cpu1/online
# Use: Power saving or testing

# CPU frequency
cpufreq-info                      # CPU frequency info
cpupower frequency-info           # Detailed frequency info
# Use: Monitor CPU frequency
```

### Memory Optimization

```bash
# Clear cache
sudo sync                         # Flush filesystem buffers
sudo sysctl -w vm.drop_caches=3   # Clear all caches
# 1 = pagecache, 2 = dentries and inodes, 3 = all
# Use: Free memory (rarely needed)

# Swappiness
cat /proc/sys/vm/swappiness
sudo sysctl vm.swappiness=10      # Lower = less swap usage
# /etc/sysctl.conf: vm.swappiness=10
# Use: Control swap behavior

# Huge pages
cat /proc/meminfo | grep Huge
sudo sysctl vm.nr_hugepages=128   # Allocate 128 huge pages
# Use: Improve memory performance for large applications

# OOM killer adjustment
cat /proc/PID/oom_score
echo -1000 | sudo tee /proc/PID/oom_score_adj # Protect from OOM killer
# Use: Prioritize critical processes

# Memory limits (cgroups)
sudo cgcreate -g memory:/mygroup
echo 1G | sudo tee /sys/fs/cgroup/memory/mygroup/memory.limit_in_bytes
sudo cgexec -g memory:mygroup command
# Use: Limit process memory usage
```

### Disk I/O Optimization

```bash
# I/O scheduler
cat /sys/block/sda/queue/scheduler
echo deadline | sudo tee /sys/block/sda/queue/scheduler
# Schedulers: noop, deadline, cfq, bfq
# Use: Optimize I/O performance

# Read-ahead
sudo blockdev --getra /dev/sda
sudo blockdev --setra 8192 /dev/sda # Set to 4MB (8192 * 512 bytes)
# Use: Improve sequential read performance

# Filesystem mount options
# /etc/fstab
/dev/sda1 /data ext4 noatime,nodiratime 0 2
# noatime: Don't update access time (performance)
# nodiratime: Don't update directory access time
# Use: Reduce disk writes

# Disable journaling (ext4)
sudo tune2fs -O ^has_journal /dev/sda1
# WARNING: Reduces data safety
# Use: Maximum performance (use with caution)

# SSD optimization
# /etc/fstab
/dev/sda1 / ext4 discard,noatime 0 1
# discard: Enable TRIM
# Use: Optimize SSD performance

# I/O priority
ionice -c 3 command               # Idle priority
ionice -c 2 -n 0 command          # Best-effort, highest priority
# Classes: 1=realtime, 2=best-effort, 3=idle
# Use: Control I/O priority
```

### Network Optimization

```bash
# Network buffer sizes
sudo sysctl -w net.core.rmem_max=16777216
sudo sysctl -w net.core.wmem_max=16777216
sudo sysctl -w net.ipv4.tcp_rmem="4096 87380 16777216"
sudo sysctl -w net.ipv4.tcp_wmem="4096 65536 16777216"
# Use: Improve network throughput

# TCP congestion control
cat /proc/sys/net/ipv4/tcp_congestion_control
sudo sysctl -w net.ipv4.tcp_congestion_control=bbr
# Algorithms: cubic, bbr, reno
# Use: Optimize TCP performance

# Connection tracking
sudo sysctl -w net.netfilter.nf_conntrack_max=1000000
# Use: Handle more concurrent connections

# TCP keepalive
sudo sysctl -w net.ipv4.tcp_keepalive_time=600
sudo sysctl -w net.ipv4.tcp_keepalive_intvl=60
sudo sysctl -w net.ipv4.tcp_keepalive_probes=3
# Use: Detect dead connections faster

# Disable IPv6 (if not used)
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
# Use: Reduce overhead

# Network interface tuning
sudo ethtool -G eth0 rx 4096 tx 4096 # Ring buffer size
sudo ethtool -K eth0 tso on gso on   # Offload features
# Use: Optimize NIC performance
```

### Kernel Parameters

```bash
# View all parameters
sysctl -a

# Common tuning parameters (/etc/sysctl.conf)
# File system
fs.file-max = 2097152              # Max open files
fs.inotify.max_user_watches = 524288 # Inotify watches

# Network
net.core.somaxconn = 65535         # Max connection queue
net.ipv4.ip_local_port_range = 1024 65535 # Port range
net.ipv4.tcp_max_syn_backlog = 8192 # SYN queue size
net.ipv4.tcp_tw_reuse = 1          # Reuse TIME_WAIT sockets

# Memory
vm.swappiness = 10                 # Swap usage
vm.dirty_ratio = 15                # Dirty page threshold
vm.dirty_background_ratio = 5      # Background flush threshold

# Apply changes
sudo sysctl -p
# Use: Persistent kernel tuning
```

---

## Troubleshooting

### System Won't Boot

```bash
# Boot into recovery mode
# Select "Advanced options" in GRUB
# Choose recovery mode

# Check filesystem
sudo fsck /dev/sda1
sudo fsck -y /dev/sda1            # Auto-repair

# Mount root filesystem
mount -o remount,rw /

# Check fstab
cat /etc/fstab
# Comment out problematic entries

# Reinstall GRUB
sudo grub-install /dev/sda
sudo update-grub

# Check kernel
ls /boot
# If kernel missing, reinstall from live CD

# Boot logs
journalctl -xb                    # Current boot
journalctl -xb -1                 # Previous boot
dmesg | less                      # Kernel messages
```

### High CPU Usage

```bash
# Identify CPU-intensive processes
top
htop
ps aux --sort=-%cpu | head -10

# Process details
ps -p PID -o pid,ppid,cmd,%mem,%cpu
lsof -p PID                       # Open files
strace -p PID                     # System calls

# CPU usage over time
sar -u 1 10                       # 10 samples, 1 second apart
mpstat 1                          # Per-CPU statistics

# Check for runaway processes
ps aux | awk '$3 > 50'            # Processes using > 50% CPU

# Kill process
kill PID
kill -9 PID                       # Force kill

# Renice process
renice -n 10 -p PID               # Lower priority
```

### High Memory Usage

```bash
# Memory usage
free -h
vmstat 1                          # Memory statistics
cat /proc/meminfo

# Identify memory-intensive processes
ps aux --sort=-%mem | head -10
top -o %MEM

# Process memory details
pmap PID                          # Memory map
cat /proc/PID/status | grep Vm    # Virtual memory info

# Check for memory leaks
valgrind --leak-check=full command

# Clear cache (if necessary)
sudo sync
sudo sysctl -w vm.drop_caches=3

# OOM killer logs
dmesg | grep -i "out of memory"
grep -i "killed process" /var/log/syslog

# Increase swap
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### Disk Space Issues

```bash
# Check disk usage
df -h
df -i                             # Inode usage

# Find large files
find / -type f -size +100M 2>/dev/null
du -ah / | sort -rh | head -20

# Find large directories
du -h --max-depth=1 / | sort -rh

# Check log files
du -sh /var/log/*
journalctl --disk-usage

# Clean package cache
sudo apt clean                    # Debian/Ubuntu
sudo yum clean all                # RHEL/CentOS

# Remove old kernels
dpkg -l | grep linux-image        # List kernels
sudo apt remove linux-image-X.X.X # Remove old kernel

# Clean journalctl
sudo journalctl --vacuum-size=100M
sudo journalctl --vacuum-time=7d

# Find deleted but open files
lsof | grep deleted
# Restart process to free space

# Inode exhaustion
df -i
find / -xdev -type f | cut -d "/" -f 2 | sort | uniq -c | sort -n
# Delete unnecessary small files
```

### Network Issues

```bash
# Check network interfaces
ip addr show
ip link show

# Test connectivity
ping -c 4 8.8.8.8                 # Google DNS
ping -c 4 google.com              # DNS resolution

# Check routing
ip route show
traceroute google.com

# DNS troubleshooting
cat /etc/resolv.conf
nslookup google.com
dig google.com
host google.com

# Check listening ports
netstat -tulpn
ss -tulpn

# Check firewall
sudo ufw status
sudo iptables -L

# Network statistics
netstat -s
ss -s

# Check for packet loss
ping -c 100 google.com | grep loss
mtr google.com                    # Continuous traceroute

# Interface statistics
ip -s link show eth0
ifconfig eth0

# Reset network
sudo systemctl restart NetworkManager
sudo systemctl restart networking

# Flush DNS cache
sudo systemd-resolve --flush-caches
sudo resolvectl flush-caches
```

### Service Not Starting

```bash
# Check service status
sudo systemctl status service_name
journalctl -u service_name -n 50

# Check service configuration
sudo systemctl cat service_name

# Test service manually
/usr/bin/service_binary --config /etc/service/config

# Check dependencies
systemctl list-dependencies service_name

# Check for port conflicts
sudo lsof -i :PORT
sudo netstat -tulpn | grep :PORT

# Check permissions
ls -l /usr/bin/service_binary
ls -l /etc/service/

# Check logs
tail -f /var/log/service.log
journalctl -u service_name -f

# Reload systemd
sudo systemctl daemon-reload

# Reset failed state
sudo systemctl reset-failed service_name

# Enable debug mode
sudo systemctl edit service_name
# Add: Environment="DEBUG=true"
```

### Permission Denied Errors

```bash
# Check file permissions
ls -l /path/to/file
stat /path/to/file

# Check ownership
ls -l /path/to/file

# Check parent directory permissions
ls -ld /path/to/

# Check SELinux context
ls -Z /path/to/file
getenforce

# Check AppArmor
sudo aa-status

# Check file attributes
lsattr /path/to/file

# Check process capabilities
getcap /path/to/binary

# Check sudo permissions
sudo -l

# Fix permissions
sudo chmod 644 /path/to/file
sudo chown user:group /path/to/file

# SELinux troubleshooting
sudo setenforce 0                 # Temporary disable
sudo restorecon -Rv /path/to/file # Restore context
```

### Slow System Performance

```bash
# System load
uptime
w

# CPU usage
top
htop
mpstat 1

# Memory usage
free -h
vmstat 1

# Disk I/O
iostat -x 1
iotop

# Network usage
iftop
nethogs

# Check for swap usage
free -h
swapon --show

# Identify bottleneck
dstat                             # Combined stats

# Check for zombie processes
ps aux | grep defunct

# Check for high load average
uptime
# Load > CPU cores indicates problem

# System logs
journalctl -p err -n 50
dmesg | tail -50

# Hardware issues
sudo smartctl -a /dev/sda         # Disk health
sensors                           # Temperature
```

---

## Interview Questions

### Basic Questions

**Q: What is Linux?**
A: Linux is an open-source, Unix-like operating system kernel created by Linus Torvalds in 1991. It's the foundation of various distributions (Ubuntu, CentOS, Debian, etc.) and is widely used in servers, embedded systems, and cloud infrastructure.

**Q: Difference between Linux and Unix?**
A: Unix is a proprietary OS developed in the 1970s. Linux is a Unix-like OS but is open-source and free. Linux was inspired by Unix but written from scratch. Unix variants include Solaris, AIX, HP-UX.

**Q: What is a kernel?**
A: The kernel is the core component of the OS that manages hardware resources, process scheduling, memory management, and provides system calls for applications to interact with hardware.

**Q: What is a shell?**
A: A shell is a command-line interpreter that processes user commands. Common shells include Bash, Zsh, Fish, and Sh. It provides an interface between the user and the kernel.

**Q: Difference between Bash and Shell?**
A: Shell is a generic term for command-line interpreters. Bash (Bourne Again Shell) is a specific type of shell and the most common one in Linux. Other shells include Zsh, Fish, Ksh.

**Q: What is a distribution?**
A: A Linux distribution (distro) is a complete operating system built on the Linux kernel, including system utilities, applications, and package management. Examples: Ubuntu, CentOS, Debian, Arch.

**Q: What is the difference between /bin and /usr/bin?**
A: /bin contains essential user binaries needed for system boot and single-user mode (ls, cat, cp). /usr/bin contains non-essential user binaries for normal system operation. Modern systems often symlink /bin to /usr/bin.

**Q: What is the purpose of /etc directory?**
A: /etc contains system-wide configuration files for applications and services (e.g., /etc/passwd, /etc/fstab, /etc/ssh/sshd_config).

**Q: What is /proc directory?**
A: /proc is a virtual filesystem that provides information about running processes and kernel parameters. Files like /proc/cpuinfo, /proc/meminfo contain system information.

**Q: What is /var directory used for?**
A: /var contains variable data that changes during system operation, including logs (/var/log), caches (/var/cache), spool files (/var/spool), and temporary files (/var/tmp).

### File System Questions

**Q: Explain Linux file permissions.**
A: Linux uses a 3-tier permission model: Owner, Group, Others. Each tier has Read (4), Write (2), Execute (1) permissions. Example: 755 = rwxr-xr-x (owner: all, group: read+execute, others: read+execute).

**Q: What is the difference between hard link and soft link?**
A: Hard link: Multiple names for the same inode (same file data), cannot link directories, must be on same filesystem. Soft link (symlink): Pointer to another file/directory, can link directories, can cross filesystems, breaks if target is deleted.

**Q: What is an inode?**
A: An inode is a data structure that stores metadata about a file (permissions, ownership, timestamps, size, location of data blocks) but not the filename or actual data.

**Q: How to find files modified in the last 7 days?**
A: `find /path -type f -mtime -7`

**Q: How to find files larger than 100MB?**
A: `find /path -type f -size +100M`

**Q: What is the difference between /tmp and /var/tmp?**
A: /tmp is cleared on reboot and used for temporary session data. /var/tmp persists across reboots and is used for temporary files that need to survive restarts.

**Q: How to check disk usage?**
A: `df -h` for filesystem usage, `du -sh /path` for directory size, `df -i` for inode usage.

**Q: What is RAID and what are common RAID levels?**
A: RAID (Redundant Array of Independent Disks) combines multiple disks for performance or redundancy. Common levels: RAID 0 (striping, performance), RAID 1 (mirroring, redundancy), RAID 5 (striping with parity, min 3 disks), RAID 10 (mirrored stripes, min 4 disks).

**Q: What is LVM?**
A: LVM (Logical Volume Manager) provides flexible disk management by abstracting physical storage into logical volumes. It allows dynamic resizing, snapshots, and spanning volumes across multiple disks.

**Q: Explain the boot process.**
A: 1) BIOS/UEFI initializes hardware, 2) Bootloader (GRUB) loads kernel, 3) Kernel initializes system and mounts root filesystem, 4) Init system (systemd) starts services, 5) System reaches target runlevel/target.

### Process Management Questions

**Q: What is a process?**
A: A process is an instance of a running program with its own memory space, resources, and execution state. Each process has a unique PID (Process ID).

**Q: Difference between process and thread?**
A: A process is an independent execution unit with its own memory space. A thread is a lightweight process that shares memory space with other threads in the same process. Threads are faster to create and switch between.

**Q: What is a zombie process?**
A: A zombie process is a terminated process that hasn't been reaped by its parent. It remains in the process table until the parent reads its exit status. Shows as `<defunct>` in `ps` output.

**Q: How to kill a zombie process?**
A: You cannot kill a zombie directly. Kill the parent process, which will cause init (PID 1) to adopt and reap the zombie: `kill -9 PPID`

**Q: What is a daemon?**
A: A daemon is a background process that runs continuously, typically started at boot, providing services (e.g., sshd, httpd, cron). Daemons usually have no controlling terminal.

**Q: Difference between kill and killall?**
A: `kill` terminates a process by PID: `kill PID`. `killall` terminates all processes by name: `killall process_name`.

**Q: What are different kill signals?**
A: SIGTERM (15): Graceful termination (default), SIGKILL (9): Force kill (cannot be caught), SIGHUP (1): Reload configuration, SIGINT (2): Interrupt (Ctrl+C), SIGSTOP (19): Pause process.

**Q: What is nice and renice?**
A: Nice sets priority for a new process (-20 to 19, lower = higher priority): `nice -n 10 command`. Renice changes priority of a running process: `renice -n 5 -p PID`.

**Q: How to run a process in the background?**
A: Append `&` to command: `command &`. Use `nohup` to ignore hangup signal: `nohup command &`.

**Q: What is the difference between & and nohup?**
A: `&` runs process in background but terminates when shell closes. `nohup` ignores hangup signal, allowing process to continue after shell closes.

### Networking Questions

**Q: How to check open ports?**
A: `netstat -tulpn` or `ss -tulpn` (modern alternative).

**Q: How to check if a port is listening?**
A: `lsof -i :PORT` or `netstat -tulpn | grep :PORT` or `ss -tulpn | grep :PORT`.

**Q: What is the difference between TCP and UDP?**
A: TCP is connection-oriented, reliable, ensures ordered delivery, has error checking (HTTP, SSH, FTP). UDP is connectionless, unreliable, faster, no delivery guarantee (DNS, DHCP, streaming).

**Q: How to test network connectivity?**
A: `ping` for basic connectivity, `traceroute` for route tracing, `telnet` or `nc` for port testing, `curl` or `wget` for HTTP testing.

**Q: What is DNS?**
A: DNS (Domain Name System) translates domain names to IP addresses. Files: /etc/resolv.conf (DNS servers), /etc/hosts (local hostname resolution).

**Q: How to troubleshoot DNS issues?**
A: Check /etc/resolv.conf, use `nslookup`, `dig`, or `host` to query DNS, test with different DNS servers: `dig @8.8.8.8 google.com`.

**Q: What is the difference between ifconfig and ip?**
A: `ifconfig` is legacy (net-tools package). `ip` is modern (iproute2 package) and more powerful. Use `ip addr` instead of `ifconfig`, `ip route` instead of `route`.

**Q: How to add a static route?**
A: `sudo ip route add 192.168.2.0/24 via 192.168.1.1`

**Q: What is a firewall? Name Linux firewalls.**
A: A firewall controls incoming/outgoing network traffic based on rules. Linux firewalls: iptables (low-level), UFW (Ubuntu, user-friendly), firewalld (RHEL/CentOS, zone-based).

**Q: How to allow SSH through UFW?**
A: `sudo ufw allow 22/tcp` or `sudo ufw allow ssh`.

### System Administration Questions

**Q: What is systemd?**
A: Systemd is a modern init system and service manager that replaces SysVinit. It manages system services, handles dependencies, supports parallel service startup, and provides logging (journald).

**Q: How to check service status?**
A: `sudo systemctl status service_name`

**Q: How to enable a service to start at boot?**
A: `sudo systemctl enable service_name`

**Q: What is the difference between systemctl start and systemctl enable?**
A: `start` starts the service immediately. `enable` configures the service to start automatically at boot. Use both for immediate start and boot persistence.

**Q: What is cron?**
A: Cron is a time-based job scheduler that runs commands at specified intervals. Configured via crontab files.

**Q: Explain crontab syntax.**
A: `* * * * * command` = minute (0-59), hour (0-23), day of month (1-31), month (1-12), day of week (0-7, Sunday=0 or 7).

**Q: How to schedule a job to run daily at 2 AM?**
A: `0 2 * * * /path/to/script.sh`

**Q: What is the difference between cron and at?**
A: Cron schedules recurring tasks. At schedules one-time tasks.

**Q: What is logrotate?**
A: Logrotate automatically rotates, compresses, and removes old log files to manage disk space. Configured in /etc/logrotate.conf and /etc/logrotate.d/.

**Q: How to view systemd logs?**
A: `journalctl` for all logs, `journalctl -u service_name` for specific service, `journalctl -f` to follow logs, `journalctl --since "1 hour ago"` for time-based filtering.

### Package Management Questions

**Q: What is the difference between apt and apt-get?**
A: `apt` is a newer, more user-friendly command combining features of `apt-get` and `apt-cache`. `apt-get` is the traditional command. For scripts, use `apt-get` for stability.

**Q: How to install a package?**
A: Debian/Ubuntu: `sudo apt install package_name`. RHEL/CentOS: `sudo yum install package_name` or `sudo dnf install package_name`.

**Q: How to search for a package?**
A: `apt search keyword` or `yum search keyword`.

**Q: How to list installed packages?**
A: `dpkg -l` (Debian/Ubuntu) or `rpm -qa` (RHEL/CentOS).

**Q: How to remove a package?**
A: `sudo apt remove package_name` (keep config) or `sudo apt purge package_name` (remove config).

**Q: What is the difference between remove and purge?**
A: `remove` uninstalls the package but keeps configuration files. `purge` uninstalls the package and removes all configuration files.

**Q: How to update all packages?**
A: Debian/Ubuntu: `sudo apt update && sudo apt upgrade`. RHEL/CentOS: `sudo yum update` or `sudo dnf upgrade`.

**Q: What is a repository?**
A: A repository is a storage location for software packages. Package managers download and install software from configured repositories.

**Q: How to add a repository?**
A: Debian/Ubuntu: Edit /etc/apt/sources.list or add file to /etc/apt/sources.list.d/. RHEL/CentOS: `sudo yum-config-manager --add-repo URL`.

**Q: What is the difference between yum and dnf?**
A: DNF is the next-generation version of YUM with better performance, improved dependency resolution, and cleaner API. DNF is default in Fedora 22+ and RHEL 8+.

### Security Questions

**Q: How to secure SSH?**
A: Disable root login (PermitRootLogin no), use key-based authentication (PasswordAuthentication no), change default port (Port 2222), limit auth attempts (MaxAuthTries 3), use fail2ban.

**Q: What is sudo?**
A: Sudo allows users to run commands with superuser privileges. Configured in /etc/sudoers (edit with `visudo`).

**Q: How to add a user to sudo group?**
A: Debian/Ubuntu: `sudo usermod -aG sudo username`. RHEL/CentOS: `sudo usermod -aG wheel username`.

**Q: What is SELinux?**
A: SELinux (Security-Enhanced Linux) is a mandatory access control (MAC) security mechanism. It enforces policies that restrict program access to files and resources beyond traditional permissions.

**Q: What are SELinux modes?**
A: Enforcing (policies enforced), Permissive (policies not enforced, only logged), Disabled (SELinux off).

**Q: What is AppArmor?**
A: AppArmor is a MAC security system similar to SELinux but simpler. It uses profiles to restrict program capabilities. Default in Ubuntu.

**Q: How to check for SUID files?**
A: `find / -perm -4000 -type f 2>/dev/null`

**Q: What is a SUID bit?**
A: SUID (Set User ID) allows a program to run with the permissions of the file owner rather than the user executing it. Example: /usr/bin/passwd runs as root.

**Q: How to set SUID bit?**
A: `chmod u+s file` or `chmod 4755 file`.

**Q: What is fail2ban?**
A: Fail2ban monitors log files for failed login attempts and bans IP addresses that show malicious behavior by updating firewall rules.

### Performance and Troubleshooting Questions

**Q: How to check system load?**
A: `uptime` or `w` shows load average (1-min, 5-min, 15-min). Load average represents the number of processes waiting for CPU time.

**Q: What is a good load average?**
A: Load average should be less than or equal to the number of CPU cores. Load > cores indicates CPU saturation.

**Q: How to identify high CPU usage?**
A: `top` or `htop` for real-time monitoring, `ps aux --sort=-%cpu | head` for top CPU consumers.

**Q: How to identify high memory usage?**
A: `free -h` for overall memory, `ps aux --sort=-%mem | head` for top memory consumers, `vmstat` for memory statistics.

**Q: What is swap?**
A: Swap is disk space used as virtual memory when physical RAM is full. It's slower than RAM but prevents out-of-memory errors.

**Q: How to check disk I/O?**
A: `iostat -x 1` for I/O statistics, `iotop` for per-process I/O usage.

**Q: System is slow, how to troubleshoot?**
A: Check load average (`uptime`), CPU usage (`top`), memory usage (`free -h`), disk I/O (`iostat`), network usage (`iftop`), check logs (`journalctl -p err`), identify bottleneck.

**Q: How to check which process is using a file?**
A: `lsof /path/to/file` or `fuser /path/to/file`.

**Q: How to check which process is using a port?**
A: `lsof -i :PORT` or `netstat -tulpn | grep :PORT`.

**Q: Disk is full, how to find large files?**
A: `du -ah / | sort -rh | head -20` for largest files/directories, `find / -type f -size +100M` for files over 100MB.

**Q: How to recover deleted files?**
A: If file is still open by a process: `lsof | grep deleted`, then copy from /proc/PID/fd/FD. Otherwise, use recovery tools like extundelete, testdisk, or restore from backup.

### Scripting Questions

**Q: What is a shebang?**
A: Shebang (`#!/bin/bash`) is the first line of a script that specifies the interpreter to execute the script.

**Q: How to make a script executable?**
A: `chmod +x script.sh`

**Q: How to pass arguments to a script?**
A: Arguments are accessed as `$1`, `$2`, etc. `$0` is the script name, `$@` is all arguments, `$#` is the number of arguments.

**Q: What is the difference between $@ and $*?**
A: `$@` expands to separate words ("$1" "$2" "$3"), `$*` expands to a single word ("$1 $2 $3"). Use `$@` for proper argument handling.

**Q: How to check if a file exists in a script?**
A: `if [ -f "/path/to/file" ]; then echo "File exists"; fi`

**Q: What is the difference between [ ] and [[ ]]?**
A: `[ ]` is POSIX-compliant, basic test command. `[[ ]]` is Bash-specific, more powerful with pattern matching, regex, and safer string handling.

**Q: How to redirect stdout and stderr?**
A: `command > output.log 2> error.log` (separate files), `command > output.log 2>&1` (both to same file), `command &> output.log` (shorthand).

**Q: What is the difference between ; and &&?**
A: `;` runs commands sequentially regardless of success. `&&` runs next command only if previous succeeds (AND logic).

**Q: What is the difference between && and ||?**
A: `&&` runs next command if previous succeeds (AND). `||` runs next command if previous fails (OR).

**Q: How to run a command in the background?**
A: Append `&`: `command &`. Use `nohup` to persist after logout: `nohup command &`.

### Advanced Questions

**Q: What is a container?**
A: A container is a lightweight, isolated environment that packages an application with its dependencies. Containers share the host kernel but have isolated filesystem, network, and process space.

**Q: Difference between container and VM?**
A: Containers share the host 
