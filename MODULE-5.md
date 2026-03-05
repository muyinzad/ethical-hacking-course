# Module 5: Scanning & Enumeration

## Overview

This module covers scanning and enumeration techniques - the critical phase after reconnaissance. Learn to identify live hosts, open ports, services, and potential vulnerabilities.

---

## 5.1 Network Scanning Techniques

### TCP/IP Fundamentals Review

```
TCP Three-Way Handshake
───────────────────────

Client          Server
  │               │
  ├─ SYN ────────▶│
  │               │
  │◀─ SYN/ACK ───┤
  │               │
  ├─ ACK ────────▶│
  │               │
  │   ESTABLISHED │
  │               │
```

### Port Scanning Methods

```bash
# SYN Scan (requires root)
sudo nmap -sS target.com

# TCP Connect Scan
nmap -sT target.com

# UDP Scan
sudo nmap -sU target.com

# Xmas Scan
sudo nmap -sX target.com

# FIN Scan
sudo nmap -sF target.com

# NULL Scan
sudo nmap -sN target.com
```

### Nmap Advanced Techniques

```bash
# Timing templates
nmap -T0 target.com  # Paranoid
nmap -T1 target.com  # Sneaky
nmap -T2 target.com  # Polite
nmap -T3 target.com  # Normal
nmap -T4 target.com  # Aggressive
nmap -T5 target.com  # Insane

# Fragmentation
nmap -f target.com

# Decoy scanning
nmap -D decoy1,decoy2,ME target.com

# Source port manipulation
nmap -g 53 target.com

# Service version detection
nmap -sV target.com

# OS detection
nmap -O target.com

# Script scanning
nmap --script default target.com
```

---

## 5.2 Service Enumeration

### HTTP/HTTPS Enumeration

```bash
# Directory enumeration
dirb http://target.com/
gobuster dir -u http://target.com/ -w /usr/share/wordlists/dirb/common.txt

# Nikto web scanner
nikto -h http://target.com

# WhatWeb (fingerprinting)
whatweb http://target.com

# Wappalyzer (browser extension concept)
```

### HTTP Enum Script

```python
import requests

class HTTPEnumerator:
    def __init__(self, target_url):
        self.target = target_url
        self.findings = []
    
    def enumerate_directories(self, wordlist):
        """Enumerate directories"""
        
        with open(wordlist) as f:
            words = f.read().splitlines()
        
        for word in words:
            url = f"{self.target}/{word}"
            try:
                r = requests.get(url, timeout=5)
                if r.status_code != 404:
                    print(f"[{r.status_code}] {url}")
                    self.findings.append(url)
            except:
                pass
    
    def enumerate_files(self, extensions):
        """Enumerate files with extensions"""
        
        common_files = [
            'admin', 'login', 'config', 'backup',
            'phpinfo', 'shell', 'upload'
        ]
        
        for file in common_files:
            for ext in extensions:
                url = f"{self.target}/{file}.{ext}"
                # Check existence
```

### SMB Enumeration

```bash
# Nmap SMB scripts
nmap -p 445 --script smb-enum-shares target.com
nmap -p 445 --script smb-enum-users target.com
nmap -p 445 --script smb-vuln* target.com

# enum4linux
enum4linux -a target.com

# smbclient
smbclient -L //target.com
smbclient //target.com/share -U username
```

### FTP Enumeration

```bash
# Anonymous login check
ftp target.com
# Try: anonymous/anonymous

# Nmap FTP scripts
nmap -p 21 --script ftp-anon target.com
nmap -p 21 --script ftp-brute target.com

# vsftpd exploit check
nmap -p 21 --script ftp-vsftpd-backdoor target.com
```

---

## 5.3 Vulnerability Scanning

### Vulnerability Scanners

```bash
# OpenVAS
openvas-start
openvasmd --create-user admin
omp -u admin -w <password> -h localhost -p 9390 -T target.com

# Nessus (commercial)
# Download from nessus.org
/opt/nessus/sbin/nessusd start

# Nexpose (commercial)
# Download from rapid7.com

# OpenVAS via Greenbone
greenbone-nvt-sync
```

### Nmap Vulnerability Scripts

```bash
# Scan for vulnerabilities
nmap --script vuln target.com

# Specific vulnerability checks
nmap --script http-vuln* target.com
nmap --script smb-vuln* target.com
nmap --script ftp-vuln* target.com
nmap --script ssh-vuln* target.com

# Update scripts
nmap --script-updatedb
```

### Custom Vulnerability Scanner

```python
class VulnerabilityScanner:
    def __init__(self, target):
        self.target = target
        self.findings = []
    
    def scan(self):
        """Run comprehensive scan"""
        
        self.check_heartbleed()
        self.check_poodle()
        self.check_sshell()
        self.check_default_creds()
        
        return self.findings
    
    def check_heartbleed(self):
        """Check for Heartbleed"""
        # Implementation using socket
        pass
    
    def check_default_creds(self):
        """Check default credentials"""
        
        defaults = {
            'admin': ['admin', 'password', '1234'],
            'root': ['root', 'toor', ''],
            'user': ['user', 'password']
        }
        
        for user, passwords in defaults.items():
            for pwd in passwords:
                if self.try_login(user, pwd):
                    self.findings.append({
                        'vuln': 'Default Credentials',
                        'detail': f"{user}:{pwd}"
                    })
```

---

## 5.4 Network Mapping

### Network Discovery

```bash
# Discover live hosts
nmap -sn 192.168.1.0/24

# ARP discovery
arp-scan -l

# Netdiscover
netdiscover -i eth0 -r 192.168.1.0/24

# Angry IP Scanner
# GUI tool for network scanning
```

### Network Mapping Tools

```bash
# Network topology discovery
nmap - traceroute

# Visual mapping
nmap -oX scan.xml target.com
# Import to Zenmap or EyeWitness

# EyeWitness (screenshot web servers)
eyeWitness -f targets.txt --single http://target.com
```

---

## 5.5 Enumeration Best Practices

### Enumeration Checklist

```
□ DNS Enumeration
  ├─ A, AAAA, MX, NS, TXT records
  ├─ Zone transfers
  └─ Reverse DNS

□ Port Scanning
  ├─ TCP full scan
  ├─ UDP top ports
  └─ Service identification

□ Service Enumeration
  ├─ HTTP/HTTPS
  ├─ SMB
  ├─ FTP
  ├─ SSH
  └─ Database services

□ Vulnerability Assessment
  ├─ Version detection
  ├─ Default configurations
  └─ Known CVEs
```

---

## 5.6 Hands-On Lab

### Lab 5.1: Complete Network Scan

```python
#!/usr/bin/env python3
"""Complete network scanner"""

import socket
import concurrent.futures

class NetworkScanner:
    def __init__(self, target_range):
        self.target_range = target_range
        self.live_hosts = []
        self.open_ports = {}
    
    def scan_network(self):
        """Scan network for live hosts"""
        
        # Parse range (simplified)
        base_ip = self.target_range.rsplit('.', 1)[0]
        
        print(f"Scanning {self.target_range}...")
        
        with concurrent.futures.ThreadPoolExecutor(max_workers=50) as executor:
            futures = [executor.submit(self.ping_host, f"{base_ip}.{i}") 
                      for i in range(1, 255)]
            
            for future in concurrent.futures.as_completed(futures):
                if future.result():
                    self.live_hosts.append(future.result())
        
        return self.live_hosts
    
    def ping_host(self, ip):
        """Check if host is alive"""
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(1)
            result = sock.connect_ex((ip, 80))
            sock.close()
            if result == 0:
                print(f"[+] Live: {ip}")
                return ip
        except:
            pass
        return None
    
    def scan_ports(self, host, ports=None):
        """Scan ports on host"""
        
        if ports is None:
            ports = [21, 22, 23, 25, 80, 443, 3306, 3389, 8080]
        
        open_ports = []
        
        for port in ports:
            try:
                sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
                sock.settimeout(1)
                result = sock.connect_ex((host, port))
                if result == 0:
                    service = socket.getservbyport(port)
                    print(f"  [{port}] {service}")
                    open_ports.append(port)
                sock.close()
            except:
                pass
        
        return open_ports

if __name__ == "__main__":
    scanner = NetworkScanner("192.168.1.0")
    hosts = scanner.scan_network()
    print(f"\nLive hosts: {hosts}")
```

---

## Module 5 Summary

### Key Takeaways

1. **Multiple scan types** enable different detection levels
2. **Service enumeration** reveals detailed information
3. **Vulnerability scanners** automate CVE identification
4. **Network mapping** visualizes attack surface

### Tools Summary

| Tool | Purpose |
|------|---------|
| Nmap | Port scanning, OS detection |
| Nikto | Web vulnerability scanning |
| enum4linux | SMB enumeration |
| OpenVAS | Full vulnerability scanner |

### Next Steps

Proceed to **Module 6: System Hacking** to learn exploitation techniques.

---

## Quick Reference

```bash
# Quick scan
nmap -sV -sC -oA scan target.com

# Full vulnerability scan
nmap --script vuln target.com

# Stealth scan
nmap -sS -T2 target.com
```
