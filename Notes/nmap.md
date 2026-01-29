# NMAP Commands

## Nmap Practical Command Guide

This document provides **useful, real‑world Nmap commands** and explains **when and why to use them**. It is written with **bug bounty, pentesting, and AppSec reconnaissance** in mind.

---

## Basic Scan (Start Here)

```bash
nmap 10.65.173.191
```

**What it does:**

* Performs a default TCP scan on the top 1,000 ports
* Uses a SYN scan if run as root

**Use when:**

* You want a fast first look at what’s exposed

---

## Service & Version Detection

```bash
nmap -sV 10.65.173.191
```

**What it does:**

* Identifies services and versions running on open ports

**Why it matters:**

* Version numbers = vulnerability mapping (CVE hunting)

---

## Default Script Scan (Safe & Useful)

```bash
nmap -sC 10.65.173.191
```

**What it does:**

* Runs Nmap’s default NSE scripts
* Includes banner grabbing, auth checks, and basic vuln detection

**Bug bounty tip:**

* Low noise, often allowed in scope

---

## Aggressive Scan (All‑in‑One)

```bash
nmap -A 10.65.173.191
```

**Includes:**

* OS detection
* Version detection
* Default scripts
* Traceroute

⚠️ **Noisy** — always check scope.

---

## Vulnerability Scanning (NSE)

```bash
nmap --script vuln 10.65.173.191
```

**What it does:**

* Runs known vulnerability detection scripts
* Checks for common CVEs and misconfigurations

**Good for:**

* Initial vulnerability discovery
* Reporting low‑hanging fruit

---

## Targeted Vuln Scan (Recommended)

```bash
nmap -sV --script vuln 10.65.173.191
```

**Why this is better:**

* Scripts get better results when versions are known

---

## Full TCP Port Scan

```bash
nmap -p- 10.65.173.191
```

**What it does:**

* Scans all 65,535 TCP ports

**Use when:**

* Target might be hiding services on high ports

---

## Fast Scan (Large Scopes)

```bash
nmap -F 10.65.173.191
```

**What it does:**

* Scans the top 100 ports only

**Use when:**

* Scanning many hosts
* Doing recon at scale

---

## UDP Scan (Slow but Valuable)

```bash
nmap -sU 10.65.173.191
```

**Common findings:**

* DNS (53)
* SNMP (161)
* NTP (123)

⚠️ UDP scans are **slow and noisy**.

---

## OS Detection

```bash
nmap -O 10.65.173.191
```

**What it does:**

* Attempts to fingerprint the OS

**Accuracy improves** when multiple ports are open.

---

## Scan Specific Ports

```bash
nmap -p 80,443,8080 10.65.173.191
```

```bash
nmap -p 1-1024 10.65.173.191
```

**Use when:**

* You know which services you care about

---

## Web‑Focused NSE Scripts

```bash
nmap -p 80,443 --script http-enum,http-title,http-headers 10.65.173.191
```

**Great for:**

* Finding admin panels
* Identifying frameworks
* Detecting misconfigurations

---

## Output Formats (Always Save Results)

```bash
nmap -oN scan.txt 10.65.173.191
```

```bash
nmap -oA scan 10.65.173.191
```

**Creates:**

* `.nmap` (normal)
* `.gnmap`
* `.xml`

---

## Your Original Command (Improved)

```bash
nmap -sV -sC --script vuln -oN blue.nmap 10.65.173.191
```

**What this gives you:**

* Service versions
* Default scripts
* Vulnerability checks
* Saved output for reporting

✅ **Solid all‑around recon command**

---

## Pro Tips (Security Engineer Mode)

* Identify **scope & rules** before scanning
* Start light → escalate
* Pair Nmap with:

  * `httpx`
  * `ffuf`
  * `nikto`
  * manual testing
* Nmap tells you *where to look*, not *everything*

---

If you want next:

* A **bug bounty‑safe Nmap profile**
* NSE scripts worth memorizing
* Mapping Nmap findings → CVEs → exploit paths
* A one‑page **AppSec recon checklist**

Just say the word 🔍
