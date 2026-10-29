---
title: Information Gathering & Open Source Intelligence (OSINT)
category: Reconnaissance and Social Engineering
tags:
  - osi-reconnaissance
  - osint
  - google-dorking
  - shodan
  - censys
  - dns-recon
---

# Information Gathering & Open Source Intelligence (OSINT)

## Overview & Foundational Principles

In cybersecurity, reconnaissance is the initial phase of any offensive engagement or penetration test. **Open Source Intelligence (OSINT)** is the discipline of identifying, harvesting, analyzing, and synthesizing publicly accessible data to generate actionable intelligence regarding target organizations, technology assets, infrastructure, and personnel.

Unlike intrusive exploitation techniques, OSINT predominantly leverages publicly indexed records, third-party aggregators, public DNS zones, social media profiles, and technical search engines without generating hostile alerts on the target's internal security appliances.

---

## The OSINT Operational Framework

### What is OSINT?

OSINT refers strictly to information obtained from publicly open, unclassified, and legally accessible resources. Common information sources include:
- World Wide Web endpoints and cached web archives.
- Search engine indexes and advanced query operators (Google Dorking).
- Social media networks and professional career platforms.
- Regional and national corporate registries and public filings.
- Internet-wide port scanners and telemetry aggregators (Shodan, Censys).
- Public domain and network telemetry (WHOIS, DNS zone records, Certificate Transparency logs).

### The Five-Stage OSINT Cycle

The intelligence gathering process follows an iterative 5-stage lifecycle:

```text
[1. Direction] ───► [2. Collection] ───► [3. Analysis] ───► [4. Evaluation] ───► [5. Dissemination]
      ▲                                                                                   │
      └──────────────────────── Continuous Feedback Loop ─────────────────────────────────┘
```

1. **Direction (Planning & Scoping):** Defines target scope, rules of engagement, specific intelligence requirements (PIRs), and operational constraints (e.g., passive discovery vs. semi-passive verification).
2. **Collection:** Harvesting raw, unstructured data from external open repositories (scraping registries, running search queries, pulling public code repositories).
3. **Analysis:** Correlating, cleaning, and filtering harvested artifacts. Identifies patterns, cross-references findings across multiple sources, and verifies data veracity.
4. **Evaluation:** Assessing intelligence quality, confidence level, and impact. Determines whether findings satisfy the initial engagement objectives or reveal gaps requiring further exploration.
5. **Dissemination:** Documenting and presenting intelligence in standardized reports for red teams, SOC analysts, or enterprise leadership.

### Core Reconnaissance Concepts

- **Open Source:** Information accessible to any observer without requiring privileged access, authentication breaches, or legal subversion.
- **Verification & Cross-Validation:** The practice of confirming a single data point across at least two independent sources before treating it as verified intelligence.
- **Data Mining & Correlation:** Applying programmatic processing to parse massive datasets (e.g., leaked password collections, corporate employee lists) to unearth hidden linkages.
- **HUMINT (Human Intelligence):** Intelligence derived from direct interpersonal interactions, social engineering, or witness engagement.
- **Metadata Analysis:** Inspecting hidden descriptive metadata embedded in file formats (EXIF data in images, revision histories in PDFs and Office documents) revealing software versions, usernames, and GPS coordinates.

### Ethical & Legal Boundaries

> [!IMPORTANT] Rules of Engagement & Privacy Compliance
> While OSINT leverages publicly accessible data, investigators must operate strictly within legal parameters:
> - Unauthorized intrusion, credential cracking, bypassing access controls, or deploying intrusive exploit payloads falls outside OSINT and constitutes computer crime.
> - Data handling must adhere to regulatory privacy standards (e.g., GDPR, CCPA).
> - Red-team engagements require clear client authorization and defined scopes of target entities.

---

## Advanced Search Engine Reconnaissance: Google Dorking

**Google Dorking** (also known as Google Hacking) leverages advanced search operators within commercial search engines to isolate configuration files, backup databases, unindexed admin portals, and exposed sensitive credentials.

![Google Dorking Overview](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/f5530885-2ad0-4a18-b3ae-0bda9a3a31a1/google-dork-9c8827644.webp)

### The Google Hacking Database (GHDB)

Curated originally by Johnny Long in the early 2000s and maintained today by Exploit-DB ([Google Hacking Database](https://www.exploit-db.com/google-hacking-database)), the **GHDB** is an open-source catalog of search queries categorized by security significance (e.g., files containing passwords, sensitive directories, vulnerable servers, web server detection).

![Google Hacking Database](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/f5530885-2ad0-4a18-b3ae-0bda9a3a31a1/dork-database-1e8c6dcf9.webp)

### Essential Google Search Operators

| # | Operator | Functional Syntax | Operational Purpose & Usage |
| :-: | :--- | :--- | :--- |
| `1` | `site:` | `site:example.com` | Restricts search exclusively to a specific domain or sub-domain. |
| `2` | `filetype:` | `filetype:pdf` | Restricts results to specific file extensions (`pdf`, `env`, `sql`, `txt`, `docx`). |
| `3` | `intitle:` | `intitle:"login"` | Searches for target terms within the HTML `<title>` tag. |
| `4` | `inurl:` | `inurl:admin` | Restricts search to terms appearing inside the URL path. |
| `5` | `cache:` | `cache:example.com` | Retrieves Google's cached snapshot of the target web page. |
| `6` | `link:` | `link:example.com` | Uncovers external websites containing hyperlinks to the specified target. |
| `7` | `related:` | `related:example.com` | Identifies web domains exhibiting similar content classifications. |
| `8` | `intext:` | `intext:"password"` | Searches for specific keywords within the visible page body text. |
| `9` | `allintitle:`| `allintitle:login admin` | Mandates that every specified keyword must appear inside the HTML title. |
| `10`| `allinurl:` | `allinurl:admin login` | Mandates that every specified keyword must appear within the URL path. |
| `11`| `allintext:`| `allintext:username password` | Mandates that all listed terms appear within the page body. |
| `12`| `define:` | `define:OSINT` | Returns formal definitions and dictionary summaries. |
| `13`| `""` | `"admin login"` | Forces exact phrase matching, preserving word order and punctuation. |
| `14`| `-` | `password -example` | Negation operator; excludes results containing the specified token. |
| `15`| `OR` / `\|` | `login OR signup` | Boolean logic returning results matching either search term. |
| `16`| `*` | `intitle:"admin *"` | Wildcard character representing any arbitrary word or character string. |
| `17`| `..` | `filetype:pdf 2020..2024` | Numerical range search between two integer bounds. |
| `18`| `info:` | `info:example.com` | Returns high-level Google metadata regarding a specific domain. |
| `19`| `maps:` | `maps:New York` | Displays geographic map records. |
| `20`| `stocks:` | `stocks:GOOG` | Retrieves financial stock tickers and trading data. |

### Practical Google Dork Query Catalog

The following 30 query patterns illustrate how offensive testers extract intelligence:

1. **Targeted Site Content Discovery:**
   ```text
   site:example.com "contact information"
   ```
2. **Confidential Document Exposure:**
   ```text
   filetype:pdf "confidential"
   ```
3. **Open Directory Indexes:**
   ```text
   intitle:"index of /"
   ```
4. **Administrative Portal Discovery:**
   ```text
   inurl:admin
   ```
5. **Viewing Historical Web Cache:**
   ```text
   cache:example.com
   ```
6. **Inbound Link Enumeration:**
   ```text
   link:example.com
   ```
7. **Domain Affinity Discovery:**
   ```text
   related:example.com
   ```
8. **Credential Harvesting via Body Text:**
   ```text
   intext:"password"
   ```
9. **Administrative Login Pages by Title:**
   ```text
   allintitle:login admin
   ```
10. **URL Path Administration Endpoints:**
    ```text
    allinurl:admin login
    ```
11. **Plaintext Credential Tables:**
    ```text
    allintext:username password
    ```
12. **Glossary & Definition Querying:**
    ```text
    define:phishing
    ```
13. **Strict Phrase Matching:**
    ```text
    "confidential document"
    ```
14. **Exclusionary Filtering:**
    ```text
    password -example
    ```
15. **Boolean Logical Disjunction:**
    ```text
    admin OR user
    ```
16. **Wildcard Title Discovery:**
    ```text
    intitle:"admin *"
    ```
17. **Date-Ranged File Retrieval:**
    ```text
    filetype:pdf 2020..2023
    ```
18. **Domain Overview Interrogation:**
    ```text
    info:google.com
    ```
19. **Directory Indexing Excluding Common Web Handlers:**
    ```text
    intitle:"Index of" -inurl:(jsp|pl|php|html|aspx|htm|cf|shtml)
    ```
20. **Exposed Live Camera Streaming Interfaces:**
    ```text
    inurl:"viewerframe?mode=motion"
    ```
21. **Disclosed Corporate Email Lists:**
    ```text
    filetype:txt @gmail.com OR @yahoo.com
    ```
22. **Legacy Operating System Default Installations:**
    ```text
    intitle:"Welcome to Windows 2000 Internet Services"
    ```
23. **Potentially Vulnerable Dynamic Query Strings (SQLi Surfaces):**
    ```text
    inurl:index.php?id=
    ```
24. **Exposed Plaintext Password Backups:**
    ```text
    intitle:"Index of" password.txt
    ```
25. **Direct Administrative Login Routes:**
    ```text
    inurl:admin/login
    ```
26. **Exposed phpMyAdmin Management Consoles:**
    ```text
    intext:"phpMyAdmin" "running on" inurl:"main.php"
    ```
27. **Open Public FTP Trees:**
    ```text
    intitle:"index of" inurl:ftp
    ```
28. **Exposed Configuration Files (YAML/ENV):**
    ```text
    intitle:"index of" config.yml
    ```
29. **Exposed `.git` Version Control Repositories:**
    ```text
    intitle:index of .git
    ```
30. **Default Apache Tomcat Management Panels:**
    ```text
    intitle:"Apache Tomcat" intext:"If you're seeing this, you've successfully installed Tomcat"
    ```

---

## Social Media & Image Forensic Analysis

Social media footprints and image files expose critical human and technical intelligence.

### Identity & Username Enumeration

Individuals often reuse identical handles across disparate networks. Adversaries leverage automated scanners to map an identity across services:

- **Instant Username Search:** Web-based tool evaluating username availability across hundreds of social platforms ([instantusername.com](https://instantusername.com/)).  
  ![Username Search](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/c1c0e269-5bcb-478e-8ec6-0dd3ae74ef3f/username-search-63590d868.webp)
- **Sherlock:** High-speed command-line OSINT utility querying username registration across hundreds of online platforms ([GitHub: sherlock-project/sherlock](https://github.com/sherlock-project/sherlock)).  
  ![Sherlock CLI](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/c1c0e269-5bcb-478e-8ec6-0dd3ae74ef3f/sherlock-5d4ec1470.webp)
- **RocketReach & LinkedIn:** Platforms for mapping corporate organizational hierarchies, departmental titles, email syntax patterns, and contact records ([RocketReach](https://rocketreach.co/)).  
  ![RocketReach](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/c1c0e269-5bcb-478e-8ec6-0dd3ae74ef3f/rocketreach-615f35b08.webp)

---

### Image Forensics & Reverse Search

Images disclose geographic locations, visual context, and editing histories.

#### Reverse Image Search Tools
- **Google Images ([images.google.com](https://images.google.com/)):** Uncovers source provenance, duplicate uploads, and online usage context.  
  ![Google Images](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/c1c0e269-5bcb-478e-8ec6-0dd3ae74ef3f/google-images-1-a8064f1cb.webp)
- **TinEye ([tineye.com](https://tineye.com/)):** Specializes in algorithmic image tracking, tracking historical upload dates, modifications, and higher-resolution originals.  
  ![TinEye](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/c1c0e269-5bcb-478e-8ec6-0dd3ae74ef3f/tineye-4506f3bbc.webp)
- **PimEyes ([pimeyes.com](https://pimeyes.com/)):** AI-powered biometric facial recognition search engine querying public faces across the internet to detect appearances across unlinked websites.  
  ![PimEyes Facial Recognition](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/c1c0e269-5bcb-478e-8ec6-0dd3ae74ef3f/pimeyes-1-104afae2b.webp)

---

### Digital Image Metadata Extraction: ExifTool

Camera hardware and mobile smartphones embed **Exchangeable Image File Format (EXIF)** metadata into images. EXIF fields record camera serials, firmware versions, exact timestamps, and high-precision GPS coordinates.

```bash
sudo apt-get install exiftool
exiftool IMG_8153.JPG
```

*Sample ExifTool Extraction Telemetry:*
```text
ExifTool Version Number         : 12.42
File Name                       : IMG_8153.JPG
File Size                       : 4.2 MB
File Type                       : JPEG
MIME Type                       : image/jpeg
Make                            : Apple
Camera Model Name               : iPhone 14 Pro
Software                        : 16.3.1
Date/Time Original              : 2023:04:12 17:15:58
Image Size                      : 4032x3024
GPS Latitude                    : 50 deg 50' 30.71" N
GPS Longitude                   : 4 deg 21' 14.96" E
GPS Position                    : 50 deg 50' 30.71" N, 4 deg 21' 14.96" E
```
*Security Value:* Extracting the exact `GPS Position` allows an investigator or threat actor to pinpoint the precise physical location where an employee captured a photo.

---

## Web Application & DNS Surface Reconnaissance

Mapping web infrastructure exposes underlying technology stacks, hosting providers, reverse proxies, and unlinked endpoints.

### WHOIS Protocol Lookups

WHOIS queries domain registration registries (RIRs like ARIN, RIPE, APNIC, and registrar registries) to extract domain ownership, administrative contacts, nameservers, registration dates, and autonomous system numbers (ASNs).

Online query portals include [ICANN Lookup](https://lookup.icann.org/en), [Whois.com](https://www.whois.com/whois/), and [DomainTools](https://whois.domaintools.com/).

```bash
whois google.com
```

*Sample Telemetry:*
```text
domain:       COM
organisation: VeriSign Global Registry Services
nserver:      A.GTLD-SERVERS.NET 192.5.6.30
whois:        whois.verisign-grs.com
status:       ACTIVE
created:      1985-01-01
changed:      2023-12-07
source:       IANA
```

---

### Historical Web Snapshots: The Wayback Machine

The **Internet Archive / Wayback Machine** ([web.archive.org](https://web.archive.org/)) maintains historical digital snapshots of web pages dating back to 1996.

![Wayback Machine Interface](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/6803a84f-ff74-4b07-b21a-e6c4b253e657/archive-1-8664d60d8.webp)

*Reconnaissance Applications:*
- Discovering retired subdomains, deprecated API routes, and legacy employee email directories.
- Inspecting older JavaScript bundles that previously leaked development API keys or commented passwords.
- Tracking corporate organizational changes and historical technical vendors.

---

### Technology Fingerprinting & HTTP Header Inspection

- **Browser Extensions:** Extensions like **Wappalyzer** ([wappalyzer.com](https://www.wappalyzer.com/)) and **WhatRuns** ([whatruns.com](https://www.whatruns.com/)) automatically profile frontend frameworks, CMS versions (WordPress, Drupal), analytics trackers, and web server software.  
  ![Wappalyzer Fingerprinting](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/6803a84f-ff74-4b07-b21a-e6c4b253e657/wappalyzer-f0068b2f5.webp)
- **Command-Line HTTP Header Probing (`curl`):**
  ```bash
  curl --head https://wordpress.org
  ```
  *Response:*
  ```http
  HTTP/2 200 
  server: nginx
  date: Sat, 18 May 2024 12:47:05 GMT
  content-type: text/html; charset=UTF-8
  strict-transport-security: max-age=3600
  x-frame-options: SAMEORIGIN
  ```
  Discloses that the endpoint runs behind an **Nginx** reverse proxy and enforces strict frame boundaries (`SAMEORIGIN`).

---

### Crawler Directives: `robots.txt` Analysis

Webmasters place `robots.txt` files in web server root directories to instruct search engine spiders on which paths to avoid indexing. Because developers often disallow sensitive paths, `robots.txt` frequently acts as a map of hidden administrative directories.

```bash
curl --get https://www.youtube.com/robots.txt
```
```text
User-agent: *
Disallow: /comment
Disallow: /login
Disallow: /qr
Disallow: /signup
Sitemap: https://www.youtube.com/sitemaps/sitemap.xml
```

---

### DNS Enumeration & Interrogation Tools

DNS enumeration maps target hostnames, mail exchangers, and administrative nameservers:

- **DNSdumpster ([dnsdumpster.com](https://dnsdumpster.com/)):** A web utility providing visual attack surface mapping and DNS record relationship graphs.
- **`host` Command:** Fast command-line lookup utility for IP addresses, IPv6 records, and mail exchangers (`MX`):
  ```bash
  host youtube.com
  ```
  *Output:*
  ```text
  youtube.com has address 142.250.187.110
  youtube.com has IPv6 address 2a00:1450:4017:810::200e
  youtube.com mail is handled by 0 smtp.google.com.
  ```
- **`dig` (Domain Information Groper):** Advanced DNS query utility:
  ```bash
  dig youtube.com A +noall +answer
  ```
  *Output:*
  ```text
  youtube.com.    300    IN    A    172.217.17.110
  ```

---

## Internet-Wide Scanning Engines: Shodan & Censys

Internet-wide search engines scan all $2^{32}$ public IPv4 addresses continuously, indexing banners, TLS certificates, and open ports.

### Shodan: The Search Engine for Connected Devices

**Shodan** ([shodan.io](https://www.shodan.io/)) indexes device banners, SCADA/ICS interfaces, IoT webcams, database listeners, and web servers.

![Shodan Interface](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/a00c9728-63f6-4d5e-8c68-9eb447f72a20/shodan-1-8a6947b6b.webp)

#### Top 10 Shodan Filter Queries

1. **Keyword / Daemon Discovery:**
   ```text
   apache
   ```
2. **Geographic Filtering by Two-Letter Country Code:**
   ```text
   country:DE
   ```
3. **Municipality Search:**
   ```text
   city:"San Francisco"
   ```
4. **Operating System Family:**
   ```text
   os:Windows
   ```
5. **Port Number Specification:**
   ```text
   port:22
   ```
6. **Service Protocol Filter:**
   ```text
   service:ssh
   ```
7. **Industrial Automation Hardware Filter:**
   ```text
   product:"Rockwell Automation"
   ```
8. **Organizational Autonomous System Filter:**
   ```text
   org:Google
   ```
9. **HTML Title Tag Parsing:**
   ```text
   http.title:"Login"
   ```
10. **Targeted CVE / Vulnerability Flag:**
    ```text
    vuln:heartbleed
    ```

---

### Censys Search Engine

**Censys** ([search.censys.io](https://search.censys.io/)) maintains a detailed, structured database of hosts, public certificates, and protocols across internet address space.

![Censys Search](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/a00c9728-63f6-4d5e-8c68-9eb447f72a20/censys-1-e65c8f6ba.webp)

#### Top 10 Censys Query Patterns

1. **Target Single IP or CIDR Subnet:**
   ```text
   ip:192.168.1.1
   ```
2. **TLS Certificate Association by Domain & HTTPS Port:**
   ```text
   domain:google.com and tags:443/https
   ```
3. **Service Version Enumeration:**
   ```text
   ip:192.168.1.1 and tags:version
   ```
4. **Country-Level Filtering:**
   ```text
   location.country_code:US
   ```
5. **Autonomous System Number (ASN) Mapping:**
   ```text
   autonomous_system.asn:AS12345
   ```
6. **Specific Cryptographic Cipher Suite Discovery:**
   ```text
   443.https.tls.cipher_suite: "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
   ```
7. **Protocol Interrogation:**
   ```text
   services.service_name: "ftp"
   ```
8. **Operating System Product String:**
   ```text
   services.software.product: "Windows"
   ```
9. **Unpatched Software Version Auditing:**
   ```text
   services.software.version: "OpenSSH_7.2p2"
   ```
10. **Web Daemon & Exact Version Identification:**
    ```text
    services.software.product: "nginx" and services.software.version: "1.14.0"
    ```

---

## Breach Intelligence, Deep Web, & Dark Web Monitoring

### Leaked Credential Repositories
- **Have I Been Pwned ([haveibeenpwned.com](https://haveibeenpwned.com/)):** Aggregates hundreds of commercial data breaches, allowing users and security analysts to query email addresses to determine breach exposure.  
  ![Have I Been Pwned](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/5f81010e-0bfb-4f9e-a225-e240f70d0e70/haveibeenpwned-5566ddf7c.webp)
- **Hunter.io ([hunter.io](https://hunter.io/)):** Identifies corporate email naming formats (`{first}.{last}@company.com`) and verifies active deliverability.
- **Email Checker ([email-checker.net](https://email-checker.net/)):** Validates recipient SMTP mailbox availability without triggering an actual email dispatch.

### Dark Web Intelligence & Hidden Services
- **Tor (The Onion Router) Project ([torproject.org](https://www.torproject.org/)):** Onion routing overlay network encrypting traffic through multiple intermediate relay nodes to preserve sender and destination anonymity.
- **Hidden Services (`.onion`):** Anonymously hosted websites operating inside the Tor network.
- **Ahmia ([ahmia.fi](https://ahmia.fi/)):** Clearnet-accessible search engine indexing active `.onion` hidden services.

### Deep Web Research Engines
- **DuckDuckGo ([duckduckgo.com](https://duckduckgo.com/)):** Privacy-respecting search engine that avoids user tracking and operates an official `.onion` gateway on the Tor network.
- **Not Evil:** Deep web search engine accessible through Tor.
- **Deep Web Technologies ([deepwebtech.com](http://www.deepwebtech.com/)):** Specialized search platforms querying academic databases and unindexed federated data repositories.

---

## The OSINT Framework

The **OSINT Framework** ([osintframework.com](https://osintframework.com/)) is a structured directory tree organizing open-source intelligence tools by operational discipline:

![OSINT Framework Interface](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/996237eb-2834-4b8c-91f7-eea595816ceb/osint-framework-690c585fa.webp)

### Core Taxonomy & Categories
- **Email Addresses:** Search tools (Hunter.io, Have I Been Pwned), email verification, and breach aggregators.
- **Social Media:** Profile discovery, timeline scrapers, image search.
- **Domain Names & IP Addresses:** WHOIS queries, DNS records, subdomains, reverse IP lookups, BGP routing tables.
- **Public Records & Legal Filings:** Government registries, patent records, corporate incorporation databases.
- **Dark Web Research:** Tor gateways, hidden service indexes, threat actor forums.
- **Digital Forensics:** Metadata analyzers, steganography detectors, file hash lookup repositories.
