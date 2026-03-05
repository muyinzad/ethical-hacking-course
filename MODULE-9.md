# Module 9: Cryptography

## Overview

This module covers cryptography fundamentals - encryption algorithms, hashing, digital signatures, and practical applications. Understand how to evaluate and break weak cryptographic implementations.

---

## 9.1 Encryption Fundamentals

### Types of Encryption

```
Encryption Types
────────────────

┌─────────────────────┐
│   Symmetric        │
│   Encryption       │
├─────────────────────┤
│  Single key        │
│  - AES             │
│  - DES             │
│  - 3DES            │
│  - RC4             │
└─────────────────────┘

┌─────────────────────┐
│  Asymmetric        │
│  Encryption        │
├─────────────────────┤
│  Key pair          │
│  - RSA             │
│  - DSA             │
│  - ECDSA           │
│  - Diffie-Hellman  │
└─────────────────────┘
```

### Symmetric Encryption

```python
from cryptography.fernet import Fernet

# Generate key
key = Fernet.generate_key()
cipher = Fernet(key)

# Encrypt
message = b"Secret message"
encrypted = cipher.encrypt(message)

# Decrypt
decrypted = cipher.decrypt(encrypted)
print(decrypted)  # b"Secret message"
```

### AES Example

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

# Generate random key
key = get_random_bytes(16)  # 128-bit

# Create cipher
cipher = AES.new(key, AES.MODE_EAX)

# Encrypt
plaintext = b"Secret data"
ciphertext, tag = cipher.encrypt_and_digest(plaintext)

# Decrypt
cipher = AES.new(key, AES.MODE_EAX, nonce=cipher.nonce)
plaintext = cipher.decrypt_and_verify(ciphertext, tag)
```

---

## 9.2 Asymmetric Encryption

### RSA Encryption

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

# Generate key pair
key = RSA.generate(2048)
private_key = key.export_key()
public_key = key.publickey().export_key()

# Save keys
with open("private.pem", "wb") as f:
    f.write(private_key)
with open("public.pem", "wb") as f:
    f.write(public_key)

# Encrypt with public key
key = RSA.import_key(open("public.pem").read())
cipher = PKCS1_OAEP.new(key)
ciphertext = cipher.encrypt(b"Message")

# Decrypt with private key
key = RSA.import_key(open("private.pem").read())
cipher = PKCS1_OAEP.new(key)
message = cipher.decrypt(ciphertext)
```

### Key Exchange (Diffie-Hellman)

```python
# Alice generates key
alice_private = 12345678
alice_public = pow(G, alice_private, P)

# Bob generates key
bob_private = 87654321
bob_public = pow(G, bob_private, P)

# Exchange public keys
# Alice computes
alice_shared = pow(bob_public, alice_private, P)

# Bob computes
bob_shared = pow(alice_public, bob_private, P)

# Both have same shared secret
```

---

## 9.3 Hashing Algorithms

### Hash Functions

```python
import hashlib

# MD5 (broken)
md5 = hashlib.md5(b"data").hexdigest()

# SHA-1 (deprecated)
sha1 = hashlib.sha1(b"data").hexdigest()

# SHA-256 (recommended)
sha256 = hashlib.sha256(b"data").hexdigest()

# SHA-512
sha512 = hashlib.sha512(b"data").hexdigest()

# bcrypt (for passwords)
import bcrypt
hashed = bcrypt.hashpw(b"password", bcrypt.gensalt())
```

### Hash Comparison

```python
def verify_password(password, hashed):
    """Verify password against hash"""
    return bcrypt.checkpw(password, hashed)

# Rainbow tables
# Precomputed hash tables for reverse lookup
# Defense: Use salts
```

### Salting

```python
import os
import hashlib

def hash_password(password):
    """Hash with random salt"""
    salt = os.urandom(16)
    key = hashlib.pbkdf2_hmac('sha256', password.encode(), salt, 100000)
    return salt + key

def verify_password(password, stored):
    """Verify with stored salt+hash"""
    salt = stored[:16]
    stored_key = stored[16:]
    key = hashlib.pbkdf2_hmac('sha256', password.encode(), salt, 100000)
    return key == stored_key
```

---

## 9.4 Digital Signatures

### Digital Signature Process

```
Signing Process
──────────────

┌──────────┐     ┌────────────┐     ┌───────────┐
│  Message │────▶│   Hash     │────▶│ Sign with │
│          │     │ (SHA-256) │     │  Private  │
└──────────┘     └────────────┘     └─────┬─────┘
                                          │
                                          ▼
┌──────────┐     ┌────────────┐     ┌───────────┐
│  Verify  │◀────│   Hash     │◀────│  Public   │
│  Message │     │ (SHA-256) │     │   Key     │
└──────────┘     └────────────┘     └───────────┘
```

### Creating Signatures

```python
from Crypto.PublicKey import RSA
from Crypto.Signature import pkcs1_15
from Crypto.Hash import SHA256

# Generate keys
key = RSA.generate(2048)

# Sign message
message = b"Important message"
hash_obj = SHA256.new(message)
signature = pkcs1_15.new(key).sign(hash_obj)

# Verify signature
try:
    pkcs1_15.new(key.publickey()).verify(hash_obj, signature)
    print("Signature valid!")
except:
    print("Invalid signature!")
```

### Certificates

```bash
# Generate private key
openssl genrsa -out key.pem 2048

# Generate CSR
openssl req -new -key key.pem -out request.csr

# Self-signed certificate
openssl req -x509 -days 365 -key key.pem -in request.csr -out cert.pem

# View certificate
openssl x509 -in cert.pem -text -noout
```

---

## 9.5 SSL/TLS

### TLS Handshake

```
TLS Handshake
─────────────

Client                    Server
  │                        │
  │──── ClientHello ──────▶│
  │                        │
  │◀─── ServerHello ──────│
  │◀─── Certificate ──────│
  │                        │
  │──── Key Exchange ─────▶│
  │                        │
  │◀─── Finished ─────────│
  │                        │
  │════ Encrypted Data ─══▶│
  │                        │
```

### Testing SSL/TLS

```bash
# Check SSL/TLS versions
nmap --script ssl-enum-ciphers -p 443 target.com

# Test specific version
openssl s_client -connect target.com:443 -tls1_2
openssl s_client -connect target.com:443 -tls1_1
openssl s_client -connect target.com:443 -ssl3

# Check certificate
echo | openssl s_client -connect target.com:443 2>/dev/null | openssl x509 -noout -text

# Test weak ciphers
testssl target.com
```

### SSL Vulnerabilities

```bash
# POODLE (SSLv3)
openssl s_client -ssl3 -connect target.com:443

# Heartbleed
nmap -p 443 --script ssl-heartbleed target.com

# BEAST
# CRIME
# FREAK
nmap --script ssl-known-key target.com
```

---

## 9.6 Cryptanalysis Basics

### Common Attacks

```python
# Brute Force
# Try all possible keys

# Dictionary Attack
# Use wordlist of possible keys

# Rainbow Tables
# Precomputed hash chains

# Birthday Attack
# Hash collisions

# Side-Channel Attacks
# Timing analysis
# Power analysis
```

### Hash Collision

```python
# MD5 collision example (simplified)
# Two different files with same MD5

# Using fastcoll
# fastcoll -o file1.bin file2.bin
# md5sum file1.bin file2.bin  # Same hash!
```

### Breaking Weak Encryption

```python
class CryptoBreaker:
    """Demonstrate weak crypto attacks"""
    
    def __init__(self):
        self.wordlist = []
    
    def load_wordlist(self, path):
        """Load password wordlist"""
        with open(path) as f:
            self.wordlist = f.read().splitlines()
    
    def crack_md5(self, target_hash):
        """Brute force MD5"""
        
        for password in self.wordlist:
            hash_obj = hashlib.md5(password.encode())
            if hash_obj.hexdigest() == target_hash:
                return password
        
        return None
    
    def detect_weak_cipher(self, cipher_text):
        """Detect weak cipher usage"""
        
        # Check for ECB mode (repeating patterns)
        # Check for no IV
        # Check for weak keys
        
        return []
```

---

## 9.7 Practical Cryptography

### Secure Password Storage

```python
import hashlib
import secrets

def create_hash(password):
    """Create secure password hash"""
    
    # Use PBKDF2
    salt = secrets.token_bytes(32)
    key = hashlib.pbkdf2_hmac(
        'sha256',
        password.encode(),
        salt,
        100000
    )
    
    return salt.hex() + ':' + key.hex()

def verify_password(password, stored):
    """Verify password"""
    salt_hex, key_hex = stored.split(':')
    salt = bytes.fromhex(salt_hex)
    key = bytes.fromhex(key_hex)
    
    new_key = hashlib.pbkdf2_hmac(
        'sha256',
        password.encode(),
        salt,
        100000
    )
    
    return new_key == key
```

### File Encryption

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

def encrypt_file(input_file, output_file, password):
    """Encrypt file with AES"""
    
    # Derive key from password
    key = hashlib.scrypt(password.encode(), salt=b'salt', n=16384, r=8, p=1)
    
    # Generate IV
    iv = get_random_bytes(16)
    
    # Encrypt
    cipher = AES.new(key, AES.MODE_CBC, iv)
    
    with open(input_file, 'rb') as f:
        data = f.read()
    
    # Pad data
    padding = 16 - len(data) % 16
    data += bytes([padding] * padding)
    
    encrypted = iv + cipher.encrypt(data)
    
    with open(output_file, 'wb') as f:
        f.write(encrypted)

def decrypt_file(input_file, output_file, password):
    """Decrypt file"""
    # Reverse the process
    pass
```

---

## 9.8 Hands-On Lab

### Lab 9.1: Crypto Analysis

```python
#!/usr/bin/env python3
"""Analyze cryptographic implementations"""

import hashlib

class CryptoAnalyzer:
    def __init__(self):
        self.results = {}
    
    def analyze_hash(self, hash_value):
        """Identify hash type"""
        
        length = len(hash_value)
        
        if length == 32:
            return "MD5 or MD4"
        elif length == 40:
            return "SHA-1"
        elif length == 56:
            return "SHA-224 or SHA3-224"
        elif length == 64:
            return "SHA-256 or SHA3-256"
        elif length == 96:
            return "SHA-384 or SHA3-384"
        elif length == 128:
            return "SHA-512 or SHA3-512"
        
        return "Unknown"
    
    def check_password_strength(self, password):
        """Check password strength"""
        
        score = 0
        
        if len(password) >= 8:
            score += 1
        if len(password) >= 12:
            score += 1
        if any(c.isupper() for c in password):
            score += 1
        if any(c.islower() for c in password):
            score += 1
        if any(c.isdigit() for c in password):
            score += 1
        if any(c in "!@#$%^&*" for c in password):
            score += 1
        
        return {
            'score': score,
            'strength': 'Weak' if score < 3 else 'Medium' if score < 5 else 'Strong'
        }

if __name__ == "__main__":
    analyzer = CryptoAnalyzer()
    
    # Test hash identification
    print(analyzer.analyze_hash("5d41402abc4b2a76b9719d911017c592"))
    
    # Test password strength
    print(analyzer.check_password_strength("password123"))
    print(analyzer.check_password_strength("P@ssw0rd!2024"))
```

---

## Module 9 Summary

### Key Takeaways

1. **Symmetric encryption** uses single key (AES)
2. **Asymmetric encryption** uses key pair (RSA)
3. **Hashing** creates fixed-length fingerprints
4. **Digital signatures** verify authenticity
5. **TLS** secures communications
6. **Weak crypto** can be broken

### Algorithm Comparison

| Algorithm | Type | Key Size | Use Case |
|-----------|------|----------|----------|
| AES | Symmetric | 128/256 | File encryption |
| RSA | Asymmetric | 2048+ | Key exchange |
| SHA-256 | Hash | N/A | Integrity |
| bcrypt | Hash | N/A | Passwords |

### Next Steps

Proceed to **Module 10: Penetration Testing** for complete testing methodology.

---

## Quick Reference

```bash
# Hash string
echo -n "text" | md5sum

# Generate SSL/TLS cert
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365

# Test SSL
openssl s_client -connect target.com:443

# Check cipher suite
nmap --script ssl-enum-ciphers -p 443 target.com
```
