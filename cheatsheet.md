# 🔍 Ultimate Nmap Enumeration Guide  
**by:** [@blindma1den](https://github.com/blindma1den)  

> A practical, field-tested guide for network enumeration with Nmap — designed for real-world penetration testing and OSCP-style workflows.  
> **Use responsibly:** Only scan systems you own or have explicit permission to test.

---

## 🧭 Table of Contents
1. [Introduction](#introduction)
2. [Information Gathering](#information-gathering)
3. [Reconnaissance: Passive vs Active](#reconnaissance-passive-vs-active)
4. [How to Use This Guide](#how-to-use-this-guide)
5. [⚙️ General Commands](#️-general-commands)
6. [🐧 Linux Techniques](#-linux-techniques)
7. [🪟 Windows Notes](#-windows-notes)
8. [🌐 Web Enumeration](#-web-enumeration)
9. [🗂 Other Services](#-other-services)
10. [🏰 Active Directory Enumeration](#-active-directory-enumeration)
11. [📜 NSE Script Categories](#-nse-script-categories)
12. [🧩 Playbook / Workflow](#-playbook--workflow)
13. [🧠 Analysis & Next Steps](#-analysis--next-steps)
14. [⚖️ Best Practices & Ethics](#️-best-practices--ethics)
15. [🧱 Suggested Repo Structure](#-suggested-repo-structure)
16. [📚 Resources & References](#-resources--references)

---

## 🧩 Introduction
**Nmap (Network Mapper)** is one of the most essential tools for cybersecurity professionals.  
It’s fast, scriptable, and incredibly flexible — capable of scanning networks, discovering hosts, identifying services, and running custom scripts for deeper analysis.

You can use it for:
- Network discovery 🕵️‍♂️  
- Port and service mapping  
- OS fingerprinting  
- Vulnerability scanning with NSE (Nmap Scripting Engine)  
- And automation for full-scale recon workflows  

---

## 💡 Information Gathering
Information gathering is the art of collecting data that reveals how your target is structured.  
It’s the **foundation of every penetration test** — from IP discovery to detailed service enumeration.

Good info gathering:
- Builds a map of the network  
- Reduces unnecessary noise  
- Saves time and effort later on  

---

## 🎯 Reconnaissance: Passive vs Active
**Passive Reconnaissance** → Gather data *without* interacting with the target.  
Examples: WHOIS lookups, public DNS records, GitHub searches, SSL transparency logs.  
💬 *It’s stealthy and risk-free.*

**Active Reconnaissance** → Send packets, requests, or queries *directly* to the target.  
Examples: Port scans, SMB enumeration, HTTP banner grabbing.  
⚡ *It’s fast, accurate, but detectable.*

➡️ In short:  
Start *passive* to avoid detection, then go *active* when you’re cleared to scan.

---

## 🧰 How to Use This Guide
Each section includes:
- Clear explanations  
- Copy-paste-ready examples  
- Tips to avoid noisy scans or breaking production  

Sections include:
- ⚙️ General usage  
- 🐧 Linux  
- 🪟 Windows  
- 🌐 Web enumeration  
- 🏰 Active Directory  
- 🗂 Other common services  

---

# ⚙️ General Commands

### 🚀 Basic Template
```bash
nmap [options] <target>
```

### 🔎 Host Discovery (Ping Sweep)
```bash
nmap -sn 10.10.14.0/24
```
➡️ Quickly find live hosts without scanning ports.

### ⚡ Quick Recon (Fast Scan)
```bash
nmap -sC -sV -T4 -F --open -oA recon_quick 10.129.67.21
```
- `-sC` → default safe scripts  
- `-sV` → version detection  
- `-T4` → faster timing  
- `-F` → top 100 ports  
- `--open` → show only open ports  
- `-oA` → export in all formats (`.nmap`, `.xml`, `.gnmap`)

### 🧨 Full TCP Port Sweep (1–65535)
```bash
sudo nmap -sS -p- -sV -T4 --min-rate 200 --open -oA full_tcp_scan 10.129.67.21
```

### 🧱 Save Outputs
```bash
nmap -sV -oN results.txt 10.129.67.21
nmap -sV -oA project_scan 10.129.67.21
```

### 🔒 Skip Host Discovery
```bash
sudo nmap -Pn -p- -sV 10.129.67.21
```

### 🧩 Timing & Rate Control
```bash
sudo nmap -sS -p- --min-rate 100 -T3 10.129.67.21
```
> Adjust for stability. Use `-T2` or `-T3` on fragile networks.

### 🧠 NSE Scripts
```bash
nmap -sC -sV 10.129.67.21
sudo nmap --script vuln -sV 10.129.67.21
nmap --script smb-enum-shares,smb-os-discovery -p 139,445 10.129.67.21
```

---

# 🐧 Linux Techniques

### 🔸 SYN Scan (Root Required)
```bash
sudo nmap -sS -p- -T4 -oA linux_syn 10.129.67.21
```

### 🔹 OS Detection
```bash
sudo nmap -O -sV --osscan-guess -oN os_detect.txt 10.129.67.21
```

### 🔸 UDP Scanning
```bash
sudo nmap -sU --top-ports 200 -sV -T3 -oN udp_scan.txt 10.129.67.21
```

### 🔹 Batch Scan from List
```bash
nmap -iL targets.txt -oA batch_results
```

### 🔸 Filter Only Open Ports
```bash
sudo nmap -sS -p- --open -oG - 10.129.67.21 | grep '/open/' > open_ports.txt
```

---

# 🪟 Windows Notes

### 🧰 TCP Connect Scan
```powershell
nmap -sT -p- -T4 -oN windows_scan.txt 10.129.67.21
```

### 🔐 Authenticated SMB Enumeration
```powershell
nmap --script smb-enum-shares.nse --script-args 'smbuser=DOMAIN\user,smbpass=Passw0rd' -p 445 10.129.67.21
```

> 🪟 Tip: Combine with PowerShell AD cmdlets or WSL for extended compatibility.

---

# 🌐 Web Enumeration

### 🌎 Basic HTTP Enumeration
```bash
nmap -p 80,443 --script http-enum,http-title,http-robots.txt -sV -T4 -oN web_basic.txt 10.129.67.21
```

### ⚠️ Vulnerability-Focused HTTP Scripts
```bash
nmap -p 80,443 --script "http-vuln*,http-sql-injection,http-shellshock" -sV -oN web_vuln.txt 10.129.67.21
```

### 🔑 HTTP Auth/Brute (Be Careful)
```bash
nmap -p 80 --script http-brute --script-args userdb=/path/users.txt,passdb=/path/pass.txt 10.129.67.21
```

### 🧩 Combine with Web Tools
Use:
- `gobuster` or `dirb` for directories  
- `ffuf` for fuzzing  
- `Burp Suite` for manual web exploration  

---

# 🗂 Other Services

### 💾 SMB Enumeration
```bash
sudo nmap -p 139,445 --script "smb-enum-shares,smb-enum-users,smb-os-discovery" -sV -T4 -oN smb_enum.txt 10.129.67.21
```

### 🌐 DNS
```bash
nmap -p 53 --script "dns-recursion,dns-zone-transfer" -sV 10.129.67.21
```

### 🗄 Database Enumeration
```bash
nmap -p 3306 --script mysql-info,mysql-users -sV 10.129.67.21
nmap -p 5432 --script pgsql-info -sV 10.129.67.21
```

### ✉️ Mail Services
```bash
nmap -p 25,587 --script smtp-enum-users,smtp-vuln-cve2010-4344 -sV 10.129.67.21
```

### 🛰 SNMP
```bash
sudo nmap -sU -p 161 --script snmp-info,snmp-brute --script-args snmpcommunity=public 10.129.67.21
```

---

# 🏰 Active Directory Enumeration

### 🔹 Core Ports
```bash
nmap -p 88,135,139,389,445,464,3268,3269 -sV --open -oN ad_ports.txt 10.129.67.0/24
```

### 🔹 SMB Enumeration
```bash
sudo nmap -p 139,445 --script "smb-enum-shares,smb-enum-users,smb-os-discovery,smb-security-mode" -sV -T4 -oN smb_enum_nse.txt 10.129.67.21
```

### 🔹 LDAP Enumeration
```bash
nmap -p 389 --script ldap-rootdse -oN ldap_rootdse.txt 10.129.67.21
nmap -p 389 --script ldap-search --script-args 'ldap.base="dc=example,dc=local"' -oN ldap_search.txt 10.129.67.21
```

### 🔹 Kerberos Checks
```bash
nmap -p 88,464 --script krb5-enum-users --script-args krb5-enum-users.username.list=/path/userlist.txt -oN krb_enum.txt 10.129.67.21
```

### 🧰 Companion Tools
- `enum4linux -a <IP>` — classic SMB + AD info  
- `smbclient -L //<IP> -N` — list shares  
- `rpcclient -U "" <IP>` — query users/groups  
- `smbmap -H <IP>` — list shares & permissions  
- `crackmapexec smb <network>` — quick AD sweeps  
- `ldapsearch`, `PowerView`, `BloodHound` — deep domain mapping  

---

# 📜 NSE Script Categories

| Category | Description | Safe? |
|-----------|-------------|-------|
| `default` | Safe, general info (run via `-sC`) | ✅ |
| `discovery` | Network/service info gathering | ✅ |
| `auth` | Authentication checks (may require creds) | ⚠️ |
| `vuln` | Vulnerability detection | ⚠️ |
| `safe` | Non-intrusive | ✅ |
| `intrusive` | May crash or disrupt | 🚫 |

### 🎯 Common Scripts
- **SMB:** `smb-enum-shares`, `smb-os-discovery`, `smb-vuln*`  
- **HTTP:** `http-enum`, `http-title`, `http-robots.txt`  
- **LDAP:** `ldap-rootdse`, `ldap-search`  
- **SSL:** `ssl-cert`, `ssl-enum-ciphers`  

Check any script first:
```bash
nmap --script-help <script-name>
```

---

# 🧩 Playbook / Workflow

1. **Passive OSINT**
   ```bash
   amass enum -d example.com
   ```
2. **Host Discovery**
   ```bash
   nmap -sn 10.10.14.0/24 -oN hosts_alive.txt
   ```
3. **Quick Recon**
   ```bash
   nmap -sC -sV -T4 -F --open -oA quick_recon <host>
   ```
4. **Full Port Scan**
   ```bash
   sudo nmap -sS -p- -sV -T4 --min-rate 200 --open -oA all_ports <host>
   ```
5. **Service-Specific NSE Scans**
6. **Manual Enumeration**
   (HTTP → gobuster / SMB → enum4linux / LDAP → ldapsearch)
7. **Document Everything**
   ```bash
   ndiff old_scan.xml new_scan.xml
   ```

---

# 🧠 Analysis & Next Steps

| Service | Port | What to Look For | Next Step |
|----------|------|------------------|------------|
| **HTTP / HTTPS** | 80,443 | Check titles, robots.txt, headers | Use gobuster, dirb, nikto, Burp |
| **SSH** | 22 | Banner info, allowed auth | Try manual SSH login, check weak creds |
| **FTP** | 21 | Anonymous login allowed? | Try `ftp anonymous@target` |
| **SMB** | 139,445 | Shares, OS info | enum4linux, smbclient, smbmap |
| **LDAP** | 389,636 | RootDSE, users | ldapsearch (anonymous or creds) |
| **Kerberos** | 88 | Realm info, users | kerbrute, crackmapexec |
| **MySQL / PostgreSQL** | 3306 / 5432 | Version & access | Connect via mysql / psql clients |
| **SNMP** | 161 | Public string? | snmpwalk, onesixtyone |
| **SMTP** | 25,587 | Users, banners | smtp-user-enum |

> Always pivot from Nmap findings into deeper enumeration tools.

---

# ⚖️ Best Practices & Ethics
✅ Always get **written authorization**  
⚙️ Start with **non-intrusive** scans  
🐢 Adjust timing & rate (`-T`, `--min-rate`)  
📜 Review scripts before running them  
🧠 Keep Nmap updated  
🚫 Never scan networks you don’t own

---

# 🧱 Suggested Repo Structure
```
nmap-enum-guide/
├─ README.md               # main guide (this one)
├─ CHEATSHEET.md           # condensed quick reference
├─ examples/
│  ├─ quick_recon.sh
│  ├─ full_tcp_scan.sh
│  └─ ad_enum.sh
├─ scripts/
│  ├─ parse_gnmap.py
│  └─ xml_to_csv.py
└─ resources/
   ├─ nse_script_notes.md
   └─ references.md
```

---

# 📚 Resources & References
- [Official Nmap Documentation](https://nmap.org/book/man.html)  
- [NSE Script Index](https://nmap.org/nsedoc/)  
- [HackTricks Nmap Cheatsheet](https://book.hacktricks.xyz/)  
- [GTFOBins](https://gtfobins.github.io/)  
- [TryHackMe](https://tryhackme.com/) / [HackTheBox](https://www.hackthebox.com/)  

---

🖋 **by:** [@blindma1den](https://github.com/blindma1den)  
💬 _Built for learners, red teamers, and everyone curious about enumeration topic

---
