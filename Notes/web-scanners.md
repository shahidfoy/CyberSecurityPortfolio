# Web Scanners — Cheat Sheet

## Quick links & wordlists

* Feroxbuster: [https://github.com/epi052/feroxbuster](https://github.com/epi052/feroxbuster)
* Common wordlists (SecLists): [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists) (e.g. `Discovery/Web-Content/common.txt`)

> Keep wordlists local and point tools to them for repeatable scans.

---

## skipfish

Lightweight, fast web crawler & scanner (good for discovery & fuzzing).

### Install

```bash
# Debian/Ubuntu (example)
sudo apt install skipfish
```

### Common usage

```bash
sudo skipfish -o /home/you/skipfish-output/ https://target.example.com
```

### Useful flags / notes

* `-o <dir>` — output directory (required)
* `-S` / `-T` — tuning options for scan intensity / timeouts (see manpage)
* Use elevated privileges if binding raw sockets is needed (some builds)
* Output includes HTML reports in the output directory — open `index.html` to review findings

---

## dirb

Simple directory/file brute-forcer using wordlists. Great for quick checks.

### Install

```bash
sudo apt install dirb
```

### Examples

```bash
# default wordlist
dirb https://target.example.com/

# specify wordlist
dirb https://target.example.com/ /usr/share/wordlists/dirb/common.txt
```

### Useful flags / notes

* Syntax: `dirb <url> [wordlist] [options]`
* Run against HTTP/HTTPS appropriately
* If target has virtual hosts, set `Host:` header or scan using hostname (not just IP)
* Watch out for rate limits / WAFs — slow down scans if you see blocks

---

## feroxbuster

Fast, simple recursive content discovery (modern, multi-threaded)

### Install

Follow repo instructions; many distros offer binaries or `cargo install feroxbuster`.

### Examples

```bash
# basic
feroxbuster -u https://target-url.com -w /path/to/wordlist.txt

# limit depth and set threads
feroxbuster -u https://target-url.com -w /path/to/wordlist.txt --depth 2 -t 50

# with common SecLists wordlist
feroxbuster -u https://0a6a0014...web-security-academy.net/ -w ~/wordlists/SecLists/Discovery/Web-Content/common.txt
```

## ffuf

Fuzz Faster U Fool

```bash
ffuf -w /usr/share/wordlists/dirb/common.txt -u https://example.com/FUZZ

# filter out file size
ffuf -w /usr/share/wordlists/dirb/common.txt -u https://example.com/FUZZ -fs 75002
```

### Common flags

* `-u`, `--url` — target URL
* `-w`, `--wordlist` — wordlist path
* `--depth` or `-d` — recursion depth (how deep to follow discovered dirs)
* `-t`, `--threads` — number of worker threads
* `-x`, `--extensions` — file extensions to append (e.g. `php,html,txt`)
* `-r`/`--redirects` — follow redirects (check tool version)
* `-s` — filter by HTTP status codes to display

> Note: exact flag names can vary by version — run `feroxbuster --help` to confirm.

---

## Wordlist tips

* Start with smaller lists (e.g., `common.txt`) then bump to larger lists if needed.
* Combine lists (e.g., virtual-host, backup filenames, extensions) for deeper discovery.
* Use targeted lists for frameworks (WordPress, Joomla) when applicable.

---

## Performance & reliability tips

* Respect the target: use reasonable threads and delays against production systems.
* If blocked by WAF, try:

  * lowering thread count
  * randomizing user-agent
  * using `--delay` if supported
* Scan during allowed testing windows and prefer `staging` environments when available.
* Capture traffic with a proxy (Burp/ZAP) to review requests/responses and tune scans.

---

## Interpreting output (quick)

* 200 / 301 / 302 — likely valid resources; check response length and content
* 401 / 403 — discovered but access-controlled
* 404 / 410 — not found (but sometimes custom 404s leak info)
* Large responses or unusual content-types can indicate config files, source leaks, or backups

---

## Legal & safety

* Only scan systems you own or have explicit permission to test.
* Automated scans can be noisy and may trigger monitoring/alerts — coordinate with stakeholders.

---
