# Module 4: Information Gathering

## Overview

This module covers the critical first phase of ethical hacking - information gathering. Learn passive and active reconnaissance techniques to discover target systems, services, and potential vulnerabilities.

---

## 4.1 Passive Reconnaissance

### Understanding Passive Recon

Passive reconnaissance involves gathering information without directly interacting with the target:

```
Passive Reconnaissance Methods
├── WHOIS Lookups
├── DNS Queries
├── Public Records
├── Social Media Mining
├── Search Engine Research
└── Archived Data
```

### WHOIS Lookups

```bash
# Basic WHOIS lookup
whois example.com

# Extract specific information
whois example.com | grep -E "Registrar|Registrant|Name Server"

# Python equivalent
import whois

domain = whois.whois("example.com")
print(domain.domain_name)
print(domain.registrar)
print(domain.name_servers)
```

### DNS Enumeration

```bash
# DNS record types
dig example.com A      # IPv4 address
dig example.com AAAA   # IPv6 address
dig example.com MX     # Mail servers
dig example.com NS     # Name servers
dig example.com TXT    # Text records
dig example.com CNAME  # Canonical names

# Zone transfer attempt
dig axfr example.com @ns1.example.com

# Reverse DNS lookup
dig -x 192.168.1.1
```

### Python DNS Enumeration Tool

```python
import dns.resolver
import dns.zone
import dns.query

class DNSEnumerator:
    def __init__(self, domain):
        self.domain = domain
        self.records = {}
    
    def enumerate_records(self):
        """Enumerate DNS records"""
        record_types = ['A', 'AAAA', 'MX', 'NS', 'TXT', 'CNAME', 'SOA']
        
        for rtype in record_types:
            try:
                answers = dns.resolver.resolve(self.domain, rtype)
                self.records[rtype] = [str(rdata) for rdata in answers]
            except dns.resolver.NoAnswer:
                pass
            except dns.resolver.NXDOMAIN:
                print(f"Domain {self.domain} does not exist")
                return None
        
        return self.records
    
    def enumerate_subdomains(self, wordlist):
        """Brute force subdomains"""
        found = []
        
        with open(wordlist) as f:
            subdomains = f.read().splitlines()
        
        for sub in subdomains:
            try:
                domain = f"{sub}.{self.domain}"
                dns.resolver.resolve(domain, 'A')
                found.append(domain)
                print(f"[+] Found: {domain}")
            except:
                pass
        
        return found
```

---

## 4.2 Active Reconnaissance

### Active vs Passive

| Aspect | Passive | Active |
|--------|---------|--------|
| Detection Risk | Low | High |
| Information Depth | Surface | Detailed |
| Interaction | Indirect | Direct |
| Speed | Slow | Fast |

### Port Scanning with Nmap

```bash
# Basic scans
nmap 192.168.1.1              # Single host
nmap 192.168.1.1-254          # Range
nmap -sV 192.168.1.1          # Version detection
nmap -O 192.168.1.1           # OS detection
nmap -A 192.168.1.1           # Aggressive scan

# Scan types
nmap -sS 192.168.1.1          # SYN scan (stealth)
nmap -sT 192.168.1.1          # TCP connect
nmap -sU 192.168.1.1          # UDP scan
nmap -sF 192.168.1.1          # FIN scan

# Output options
nmap -oA output 192.168.1.1    # All formats
nmap -oN output.nmap 192.168.1.1
nmap -oX output.xml 192.168.1.1
```

### Advanced Nmap Scripts

```bash
# Vulnerability scanning
nmap --script vuln 192.168.1.1

# Default scripts
nmap -sC 192.168.1.1

# Specific script categories
nmap --script discovery 192.168.1.1
nmap --script exploit 192.168.1.1
nmap --script auth 192.168.1.1
nmap --script brute 192.168.1.1

# Example: SSL analysis
nmap --script ssl-enum-ciphers -p 443 example.com

# Example: HTTP methods
nmap --script http-methods example.com
```

### Building a Port Scanner

```python
import socket
import concurrent.futures

class PortScanner:
    def __init__(self, target, ports=None):
        self.target = target
        self.ports = ports or self.common_ports()
        self.open_ports = []
    
    def common_ports(self):
        """Common ports list"""
        return [
            21, 22, 23, 25, 53, 80, 110, 111, 135, 139,
            143, 443, 445, 993, 995, 1723, 3306, 3389,
            5900, 8080, 8443
        ]
    
    def scan_port(self, port):
        """Scan single port"""
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(1)
            result = sock.connect_ex((self.target, port))
            sock.close()
            return result == 0
        except:
            return False
    
    def scan(self):
        """Scan all ports"""
        print(f"Scanning {self.target}...")
        
        with concurrent.futures.ThreadPoolExecutor(max_workers=100) as executor:
            futures = {executor.submit(self.scan_port, port): port 
                     for port in self.ports}
            
            for future in concurrent.futures.as_completed(futures):
                port = futures[future]
                if future.result():
                    service = self.get_service(port)
                    print(f"[+] Port {port} open ({service})")
                    self.open_ports.append(port)
        
        return self.open_ports
    
    def get_service(self, port):
        """Get service name"""
        try:
            return socket.getservbyport(port)
        except:
            return "unknown"
```

---

## 4.3 OSINT Tools

### theHarvester

```bash
# Basic usage
theHarvester -d example.com -b all

# Specific sources
theHarvester -d example.com -b google
theHarvester -d example.com -b bing
theHarvester -d example.com -b linkedin

# Save results
theHarvester -d example.com -b all -f results.xml
```

### Recon-ng

```bash
# Start recon-ng
recon-ng

# Marketplace commands
marketplace install all
marketplace install github/github_users

# Workspace
workspaces create pentest
workspaces load pentest

# Add domain
db insert domains example.com

# Run modules
modules load recon/domains-contacts/whois_whois_domain
modules load recon/domains-hosts/hunt_subdomains
```

### Maltego

```
Maltego Transforms:
├── Company Stucture
├── DNS Enumeration
├── Social Media
├── File Shares
├── Exploits
└── LinkedIn
```

---

## 4.4 Social Engineering

### Information Gathering via Social Media

```python
class SocialMediaGatherer:
    """Gather information from social media"""
    
    def __init__(self):
        self.platforms = ['linkedin', 'twitter', 'facebook']
    
    def gather_employee_info(self, company):
        """Gather employee information"""
        
        # LinkedIn enumeration
        # Use LinkedIn API or scraping
        # Extract names, titles, departments
        
        # Twitter intelligence
        # Track employees, mentions
        
        return {
            'employees': [],
            'technologies': [],
            'locations': []
        }
    
    def profile_target(self, name):
        """Create target profile"""
        
        return {
            'name': name,
            'social_accounts': [],
            'email_patterns': [],
            'possible_passwords': []
        }
```

### Email Pattern Discovery

```python
class EmailGatherer:
    """Gather and analyze email patterns"""
    
    def __init__(self):
        self.patterns = []
    
    def discover_patterns(self, emails):
        """Discover email patterns"""
        
        # Common patterns
        patterns = [
            "{first}.{last}@{domain}",
            "{first}{last}@{domain}",
            "{f}{last}@{domain}",
            "{first}_{last}@{domain}",
        ]
        
        # Analyze existing emails to find pattern
        # Generate likely emails
        
        return patterns
    
    def validate_emails(self, emails):
        """Validate email existence"""
        
        # Use SMTP VRFY or similar
        # Check for deliverability
        
        return []
```

---

## 4.5 Google Hacking (Dorking)

### Google Dorks

```bash
# File types
site:example.com filetype:pdf
site:example.com filetype:doc OR filetype:xls

# Directories
site:example.com intitle:"index of"

# Sensitive files
site:example.com "password"
site:example.com "username"
site:example.com "confidential"

# Login pages
site:example.com inurl:login
site:example.com inurl:admin
site:example.com inurl:wp-admin

# Vulnerabilities
site:example.com inurl:php?id=
site:example.com inurl:.env
site:example.com "sql syntax"
```

### Dork Automation

```python
class GoogleDorker:
    """Automate Google dorking"""
    
    def __init__(self):
        self.dorks = {
            'login_pages': 'site:{domain} inurl:login',
            'admin_pages': 'site:{domain} inurl:admin',
            'sensitive_docs': 'site:{domain} filetype:pdf',
            'passwords': 'site:{domain} "password"',
            'sql_errors': 'site:{domain} "sql syntax"',
        }
    
    def run_dorks(self, domain):
        """Run all dorks against domain"""
        
        results = {}
        
        for name, dork_template in self.dorks.items():
            dork = dork_template.format(domain=domain)
            # Would use Google API here
            results[name] = self.search_google(dork)
        
        return results
```

---

## 4.6 Scanning Tools Comparison

### Tool Overview

| Tool | Purpose | Pros | Cons |
|------|---------|------|------|
| Nmap | Port scanning | Flexible, powerful | Learning curve |
| Masscan | Fast scanning | Very fast | Limited options |
| Unicornscan | Async scanning | Stealthy | Complex |
| Angry IP | GUI scanning | Easy to use | Limited features |
| Netcat | Manual testing | Universal | Basic |

---

## 4.7 Hands-On Lab

### Lab 4.1: Build Recon Tool

```python
#!/usr/bin/env python3
"""Simple recon tool"""

import sys
import socket
import concurrent.futures

class SimpleRecon:
    def __init__(self, target):
        self.target = target
    
    def resolve_domain(self):
        """Resolve domain to IP"""
        try:
            return socket.gethostbyname(self.target)
        except:
            return None
    
    def scan_common_ports(self):
        """Scan common ports"""
        ports = [21, 22, 23, 25, 80, 443, 3306, 3389, 8080]
        open_ports = []
        
        for port in ports:
            if self.is_open(port):
                open_ports.append(port)
        
        return open_ports
    
    def is_open(self, port):
        """Check if port is open"""
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(1)
            return sock.connect_ex((self.target, port)) == 0
        except:
            return False

if __name__ == "__main__":
    if len(sys.argv) > 1:
        target = sys.argv[1]
        recon = SimpleRecon(target)
        
        ip = recon.resolve_domain()
        if ip:
            print(f"IP: {ip}")
            ports = recon.scan_common_ports()
            print(f"Open ports: {ports}")
```

---

## Module 4 Summary

### Key Takeaways

1. **Passive reconnaissance** is safer and often effective
2. **Active scanning** provides detailed information
3. **OSINT tools** automate data gathering
4. **Social engineering** reveals human vulnerabilities
5. **Google dorks** find exposed information

### Tools Covered

| Tool | Use |
|------|-----|
| whois | Domain information |
| dig/nslookup | DNS queries |
| nmap | Port scanning |
| theHarvester | Email harvesting |
| recon-ng | OSINT framework |

### Next Steps

Proceed to **Module 5: Scanning & Enumeration** for deeper vulnerability discovery.

---

## Quick Reference

```bash
# Quick recon commands
whois target.com
dig target.com ANY
nmap -sV -sC -oA scan target.com
theHarvester -d target.com -b all
```
