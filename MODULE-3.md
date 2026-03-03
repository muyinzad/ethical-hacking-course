# 🛡️ Ethical Hacking Course - Module 3
## Linux for Hackers

---

## 3.1 Linux Basics

### Why Hackers Use Linux?
- Open source
- Powerful command line
- Pre-installed hacking tools
- Customizable
- Most servers run Linux

### Popular Distributions

| Distribution | Purpose | GUI |
|--------------|---------|-----|
| Kali Linux | Penetration testing | Yes |
| Parrot OS | Security & privacy | Yes |
| BlackArch | Arch-based hacking | Yes |
| Ubuntu Server | General use | No |

---

## 3.2 Essential Commands

### File Operations

```bash
# Navigation
ls -la          # List all files
cd /path        # Change directory
pwd             # Print working directory
mkdir folder    # Create directory
rm -rf folder   # Delete recursively

# Files
cat file        # View file
head file       # First 10 lines
tail file       # Last 10 lines
cp source dest  # Copy
mv source dest  # Move
rm file         # Delete

# Permissions
chmod 755 file      # rwxr-xr-x
chmod +x file       # Add execute
chown user:group file   # Change owner
```

---

### Network Commands

```bash
# Configuration
ifconfig        # Show interfaces
ip addr         # Modern interface config
iwconfig        # Wireless config

# Scanning
nmap -sV target     # Version scan
netstat -tulpn     # Listening ports
ss -tulpn          # Modern netstat

# DNS
nslookup domain
dig domain
host domain

# Download
wget url
curl -O url
```

---

### Process & System

```bash
# Processes
ps aux              # All processes
top                 # Process monitor
kill pid            # Kill process
systemctl start service

# System
uname -a            # System info
whoami              # Current user
sudo su             # Root access
history             # Command history
```

---

## 3.3 File Permissions

### Understanding Permissions

```
drwxr-xr-x
└─┬─┘└┬┘└┬┘└┬┘
  │   │  │  └── Execute (1)
  │   │  └───── Write (2)
  │   └──────── Read (4)
  └──────────── Directory flag

Numeric:
0 = ---
1 = --x
2 = -w-
3 = -wx
4 = r--
5 = r-x
6 = rw-
7 = rwx
```

---

## 3.4 Bash Scripting Basics

### Simple Script

```bash
#!/bin/bash

echo "Starting scan..."

# Scan targets
nmap -sV 192.168.1.0/24 > scan_results.txt

echo "Scan complete!"
cat scan_results.txt
```

### Variables

```bash
#!/bin/bash

NAME="John"
echo "Hello, $NAME"

# Command output
DATE=$(date)
echo "Today is $DATE"
```

### Conditionals

```bash
#!/bin/bash

if [ -f "file.txt" ]; then
    echo "File exists"
else
    echo "File not found"
fi
```

---

## 3.5 Package Management

### Debian/Ubuntu/Kali

```bash
# Update
apt update
apt upgrade

# Install
apt install package_name

# Remove
apt remove package_name

# Search
apt search keyword
```

### Arch Linux

```bash
pacman -Syu          # Update
pacman -S package    # Install
pacman -Rs package   # Remove
```

---

## 3.6 Essential Tools

### Pre-installed in Kali

| Tool | Purpose |
|------|---------|
| Nmap | Port scanning |
| Metasploit | Exploitation |
| Wireshark | Packet analysis |
| Burp Suite | Web testing |
| John | Password cracking |
| Aircrack-ng | WiFi hacking |
| Sqlmap | SQL injection |
| Nikto | Web scanning |

---

## Key Takeaways

✅ Linux is essential for hacking
✅ Master the command line
✅ Learn bash scripting
✅ Know your tools
✅ Practice in labs!

---

*Module 3 Complete! 🛡️*
