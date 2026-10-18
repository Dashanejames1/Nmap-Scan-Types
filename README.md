# Nmap-Scan-Types

**Author:** Dashane James  
**Lab Environment:** VMware Workstation | Kali Linux | Metasploitable 2 
**Purpose:** The goal of this repository was to use Nmap to recognize the similarities/differences in major scan types(SYN scan, UDP Scan, full port scan) and to recognize when is the appropriate times to utilize each scan type.
**Status:** 🔵 Completed

---

## 📋 Overview

This repository documents a series of nmap scan results performed in an isolated VMware home lab. I used several different scan types to target my metasploitable work station to identify which ports are open and closed while documenting the differences in each scan type. The goal was to build hands-on proficiency with nmap and identying scan types as aligned with CySA+ exam objectives."
0
---

## 🧪 Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VMware Workstation (Host-Only Network) |
| Attacker Machine | Kali Linux 2026.1 — `192.168.79.129` |
| Target Machine | [Metasploitable 2] — `192.168.79.130` |
| Network Type | Host-Only (isolated, no internet exposure) |
| Host OS | Windows 11 — ASUS Vivobook 14 |

> ⚠️ **Note:** All activity was performed in a controlled, isolated lab environment against deliberately vulnerable machines. No unauthorized access to live networks was performed.

---

## 🛠️ Tools Used

- **Nmap** — Used to run and compare SYN, UDP, and full TCP connect scans against the target

---

## 📁 Repository Structure

```
CySA-Plus-Lab-Portfolio/
├── README.md                                   # Portfolio overview and index
│
├── recon-network-discovery/
│   ├── nmap-host-discovery-lab-baseline.md
│   ├── nmap-scan-types-syn-tcp-udp.md
│   ├── service-version-os-fingerprinting.md
│   ├── wireshark-capture-and-analysis.md
│   ├── tcpdump-cli-packet-capture.md
│   ├── nmap-scripting-engine-nse.md
│   └── recon-assessment-report.md
│
├── vulnerability-scanning-exploitation/
│   ├── openvas-vulnerability-scan.md
│   ├── scan-results-cvss-scoring.md
│   ├── nikto-web-application-scanning.md
│   ├── metasploit-first-exploitation.md
│   ├── post-exploitation-enumeration.md
│   ├── mitre-attack-kill-chain-mapping.md
│   └── full-vulnerability-assessment.md
│
├── siem-log-analysis-incident-response/
│   ├── splunk-install-and-log-ingestion.md
│   ├── spl-splunk-search-language.md
│   ├── attack-generation-and-detection.md
│   ├── elk-stack-components.md
│   ├── incident-response-simulation-nist.md
│   ├── snort-ids-detection-rules.md
│   └── incident-response-report.md
│
├── scenarios-and-analyst-skills/
│   ├── full-kill-chain-red-and-blue-team.md
│   ├── vulnerability-management-deep-review.md
│   ├── cloud-security-aws-concepts.md
│   ├── zero-trust-architecture-identity.md
│   └── soar-soc-workflow-alert-triage.md
│
└── assets/
    └── screenshots/ ??????????????????????????
```

---

## 🔬 Tasks / Assessments Performed

### 1. Run a SYN scan against metasploitable ip adress and document results.

I ran a SYN scan using Nmap against my metasploitable machine to identify the open ports. The SYN scan sends a SYN packet to all ports on the target host to see which are open. If the port is open, the target responds with a SYN-ACK. Instead of completing the handshake with a final ACK, Nmap sends an RST (a TCP "reset" packet) to termiante the connection. This makes it a "half-open" scan — faster and stealthier than a full TCP connect scan, since it never establishes a full connection or gets logged the same way.

```bash
# Command used
sudo nmap -sS -Pn --disable-arp-ping -n -F 192.168.79.0/24


# Output
Command identified 18 open ports on the target machine:

report for 192.168.79.130
Host is up (0.0063s latency).
Not shown: 82 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
513/tcp  open  login
514/tcp  open  shell
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
8009/tcp open  ajp13

<img width="339" height="233" alt="Screenshot 2026-07-17 154853" src="https://github.com/user-attachments/assets/e8aa4079-1508-44ae-9880-4ada9d53971f" />

**Finding:** 
Command identified 18 open ports on the target machine, the majority of these ports have been identified to be vulnerabilities or possible vulnerabilities.**
**-sS is the flag to perform a SYN Scan**
**RST is a TCP packet with the "Reset" flag turned on, used to instantly terminate a network connection to tear down the connection.**
** -Pn is a flag used in Nmap that tells the scanner to skip the initial host discovery phase and treat all target IP addresses as online. Many modern firewalls block standard ping requests (like ICMP or specific TCP probes). Without -Pn, Nmap might assume a target is offline and refuse to scan it.**
**-F stands for Fast Scan and reduces the number of ports Nmap tests. (Using -F restricts the scan to only the top 100 most common ports for both TCP and UDP rather than Nmap's default 1000 port scan)**


### 2. Run a UDP scan of the top 20 ports against metasploitable ip adress and document results.

I ran a UDP scan using Nmap against my metasploitable machine to identify the top 20 open UDP ports.  I identfied that this is an important scan because standard TCP scans only check TCP ports while UDP requires a separate scan type entirely. Since services like DNS, SNMP, and TFTP commonly run on UDP, skipping this step can leave vulnerabilities undiscovered in an assessment.

```bash
# Commands used

sudo nmap -sU -Pn --disable-arp-ping -n --top- ports 20 192.168.79.130
```

# Output 
The output gave me the status of the top 20 UDP ports on the target machine. 3 ports were closed (msrpc,netbios-ssn, and isakmp) and the rest are open or open|filtered. 

<img width="319" height="229" alt="Screenshot 2026-07-16 221006" src="https://github.com/user-attachments/assets/7a790a29-e1bd-4f69-ac5a-9510b3bb816d" />


**Findings:**

**-sU flag is used to scan for open UDP ports.**
**UDP scans find services that TCP scans miss.**
**--top- ports 20 : displays only the top 20 most common ports.**
**UDP scans dont use a handshake when communicating with the target, instead UDP communicates by firing independent packets of data directly to a target port without any prior setup or connection verification. The receiving system either passes the data straight to a listening application or silently discards it if no service is active.**
**open|filtered means Nmap couldn't definitively tell whether the port is open or blocked by a firewall — it's showing you both possibilities because it doesn't have enough information to pick one.**
**If it gets an ICMP "port unreachable" error → the port is closed**
**.If it gets no response at all → Nmap can't tell if that's because the port is open and the service is just silently ignoring the packet, or because a firewall dropped the packet without sending any error back**


---

### 3. Running a full port scan (-p) and comparing the results.

I ran a full port TCP connect scan to document the results and to compare with a "fast" scan to see the difference in speed. The purpose of this is to identify when a full port scan is more suitable or a fast scan or "top 20" scan is more appropriate.

I ran a full TCP connect scan against Metasploitable to identify every open port across all 65,535 TCP ports, rather than relying on the "top" ports list used by -F. This scan took approximately 5.36 seconds compared to .26 seconds for the fast scan, and 1.35 seconds for the top 20 ports UDP scan. This demonstrates why a full scan is more appropriate during a thorough assessment, while a fast/top-ports scan is better suited for quick reconnaissance or time-constrained situations where speed matters more than completeness.

```bash
# Command used

sudo nmap -sT -Pn --disable-arp-ping -n -p- 192.168.79.130 -oN ~/full_scan.txt

# Output .

<img width="319" height="232" alt="Screenshot 2026-07-16 222242" src="https://github.com/user-attachments/assets/cfd17a71-c494-4467-b0d8-1f3a0ae9aa21" />

```

**Findings:** 

open|filtered means Nmap couldn't definitively tell whether the port is open or blocked by a firewall — it's showing you both possibilities because it doesn't have enough information to pick one.
.
---


### 4. The difference between SYN, Connect, and UDP scan mechanics.

SYN (-sS) is the default fast and stealthy scan (half-open, never completes the handshake), but it crafts raw packets so it needs root. When you can’t run as root, Nmap falls back to the Connect scan (-sT): it uses the OS’s normal connection call so any user can run it, but the full connection it opens is more likely to be logged. UDP (-sU) is the slow, ambiguous option, but the only way to find UDP-only services like DNS and SNMP.


## 📊 Key Findings Summary

| Port/Service | Tool Used | Risk Level | Notes |
|---|---|---|---|
| 21/tcp FTP | Nmap (SYN scan) | 🔴 Critical | Open — later confirmed to run vulnerable vsftpd 2.3.4 in a related repository |
| 23/tcp Telnet | Nmap (SYN scan) | 🔴 Critical | Open — transmits data in plaintext |
| 445/tcp SMB | Nmap (SYN scan) | 🟠 High | Open — legacy SMB service |
| 3306/tcp MySQL | Nmap (SYN scan) | 🟠 High | Open — database exposed on network |
| 5900/tcp VNC | Nmap (SYN scan) | 🔴 Critical | Open — remote desktop access, often weakly authenticated |
| UDP (top 20) | Nmap (`-sU`) | 🟡 Medium | Several ports returned `open|filtered` — inconclusive results requiring further investigation |

**Risk Levels:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low

---

## 🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| SYN scan (half-open port scanning) | Reconnaissance | T1595.001 | Scanning IP Blocks |
| UDP scan of top 20 ports | Reconnaissance | T1595.002 | Vulnerability Scanning |
| Full TCP connect scan (all 65,535 ports) | Reconnaissance | T1046 | Network Service Discovery |

---

## 🛡️ Defensive Recommendations

Based on findings, the following remediations would be recommended in a real environment:

1. **Numerous high-risk ports open (Critical)** — Disable or restrict unnecessary services (Telnet, VNC, rlogin/rsh) that have no modern secure use case.
2. **UDP services returning inconclusive results** — Follow up `open|filtered` UDP findings with targeted service probing to confirm actual state, since firewalls can mask true port status.
3. **Full port range not scanned by default** — Incorporate a full-port scan (`-p-`) into routine assessments, since fast scans only check the most common ports and can miss less-common but exploitable services.

---

## 🔑 Technical Notes


# SYN scan (half-open, faster/stealthier than a full connect scan)
sudo nmap -sS -Pn --disable-arp-ping -n -F 192.168.79.130

# UDP scan of the top 20 ports
sudo nmap -sU -Pn --disable-arp-ping -n --top-ports 20 192.168.79.130

# Full TCP connect scan across all 65,535 ports
sudo nmap -sT -Pn --disable-arp-ping -n -p- 192.168.79.130 -oN ~/full_scan.txt

# Workaround: --top-ports requires no space or hyphen inside it —
# "--top- ports 20" is invalid syntax; the correct form is "--top-ports 20"
# as a single unbroken flag.

# Note: -sS (SYN scan) requires root/raw socket privileges (hence sudo),
# while -sT (TCP connect scan) does not strictly require root, though
# sudo was used here for consistency across all scans.

> "Always add the -n flag to Nmap scans in this VMware environment to prevent DNS resolution hangs."
> 
> -sP and -sT contradict eachother (Can't be used together because -sP means just do a ping/host-discovery sweep, skip ports entirely but -sT tells Nmap to do a full TCP connect port scan. These commands contradict eachother.)


```

---

## 📌 About This Project

This repository is part of my broader cybersecurity portfolio, focused on understanding the practical tradeoffs between different Nmap scan types (speed, stealth, and completeness) and knowing when each is the right tool for a given assessment scenario.


---

## 👤 Author

**Dashane James**  
Senior Field Service Technician → Cybersecurity Analyst  
📍 Yonkers, NY  
🎓 B.S. Information Technology — SUNY Canton  
🏆 CompTIA Security+ | CySA+ (In Progress)  
🔗 [GitHub](https://github.com/Dashanejames1) | [Zero Trust Cyber Security Brand](https://www.instagram.com/zerotrust_cybersecurity)

---

*This repository is part of an active portfolio demonstrating hands-on cybersecurity skills. All lab work performed in isolated environments for educational purposes.*
