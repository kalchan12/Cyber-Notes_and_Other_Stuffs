---
title: Introduction to Cybersecurity
category: Foundations
tags:
  - cybersecurity
  - foundations
  - cia-triad
  - pentesting
  - security-careers
---

# Introduction to Cybersecurity

## Overview & Foundations

This module establishes the foundational principles of information security, the terminology used across the industry, the role of ethical hackers, and the various operational domains and career paths in cybersecurity. It also introduces penetration testing concepts, network communication fundamentals (ports and IP addressing), and fundamental security constructs like vulnerabilities, exploits, and shells.

### Core Concepts of Information & Security

In information theory and security, **information** is structured data characterized by the highest possible degree of certainty—assumed to be accurate, valid, and actionable for individuals and organizations ([Wikipedia: Knowledge](https://en.wikipedia.org/wiki/Knowledge)). Information provides meaning and context, directly shaping decisions, thoughts, and technical operations.

**Information Security (InfoSec)** is the discipline and practice of protecting information and information systems from unauthorized access, use, disclosure, disruption, modification, inspection, recording, or destruction ([Wikipedia: Information Security](https://en.wikipedia.org/wiki/Information_security)). In practical terms, it encompasses all policies, controls, and technical countermeasures taken to ensure data is not compromised or weaponized in malicious hands.

Information security spans both physical assets (printed documents, data center access, hardware security) and electronic data (databases, network traffic, cloud storage, application logic), employing layered controls such as encryption, identity and access management (IAM), antivirus software, and intrusion detection systems.

### Principles of Information Security: The CIA Triad

The core foundation of information security rests upon three fundamental pillars known as the **CIA Triad**:

![CIA Triad](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/d65a0fca-d9c9-4774-9397-42ef64272226/cia-653439271.webp)

- **Confidentiality:** Ensures that sensitive information and assets are accessible solely to authorized entities (users, processes, or devices) and prevented from unauthorized viewing or disclosure. Implemented via encryption, access controls, and data classification.
- **Integrity:** Guarantees that data remains accurate, complete, and trustworthy throughout its lifecycle, preventing unauthorized or accidental tampering, alteration, or deletion. Implemented via cryptographic hashes, digital signatures, and audit logs.
- **Availability:** Ensures that information, systems, and communication channels are accessible to authorized users whenever required. Maintained via redundancy, backups, high-availability architecture, and protection against Denial of Service (DoS) attacks.

---

## The Hacker Mindset & Classifications

The term **hack** originally stems from exploring clever, non-obvious ways to make systems perform tasks beyond their original design. In computing, it describes analyzing, modifying, or using a system in unintended ways.

A **hacker** is an expert with deep technical comprehension of operating systems, programming, and computer networks who uses that expertise to solve complex technical problems, explore system boundaries, or manipulate behavior.

### Hacker Classifications

Hackers are broadly classified into three categories based on intent, authorization, and adherence to legal boundaries:

![Types of Hackers](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/c07fef69-9c47-46e3-b157-1074e447abed/hackers-57024f6a3.webp)

| Classification | Intent & Authorization | Typical Activities & Legality | Reference |
| :--- | :--- | :--- | :--- |
| **White Hat** | Defensive, constructive, and strictly authorized. | Penetration testers and security researchers who identify vulnerabilities, collaborate with organizations, and assist in remediation within defined legal bounds. | [Wikipedia: White Hat](https://en.wikipedia.org/wiki/White_hat_\(computer_security\)) |
| **Black Hat** | Malicious, destructive, and unauthorized. | Cybercriminals who breach systems for financial gain, espionage, extortion, data theft, or service disruption, operating entirely outside the law. | [Wikipedia: Black Hat](https://en.wikipedia.org/wiki/Black_hat_\(computer_security\)) |
| **Gray Hat** | Ambiguous; often well-intentioned but unauthorized. | Individuals who identify vulnerabilities without prior authorization, later disclosing them to vendors (often requesting a reward). While their intent may not be malicious, their actions frequently violate laws and acceptable engagement standards. | [Wikipedia: Grey Hat](https://en.wikipedia.org/wiki/Grey_hat) |

### Ethical Hackers

Ethical hackers belong to the White Hat category. They leverage the exact same methodologies, toolsets, and techniques as malicious adversaries, but operate under formal authorization and contractual agreements.

Key responsibilities and attributes include:
- Performing penetration testing, vulnerability assessments, and risk evaluations.
- Discovering weaknesses before adversaries can exploit them.
- Delivering actionable remediation guidance to strengthen system resilience.
- Operating under strict ethical codes, non-disclosure agreements (NDAs), and legal frameworks.

> "To be a hacker is to bring the impossible within reach through unexpected behaviors."  
> — *Anonymous*

> [!NOTE] Practical Concept: The Hacker Approach to Efficiency
> **Problem:** What is the sum of the squares of the numbers from 1 to 100?  
> **Direct Brute-Force:** Iterating through each number, squaring it, and adding them sequentially ($1^2 + 2^2 + \dots + 100^2$).  
> **The "Hack" (Algorithmic Insight):** Applying the closed-form mathematical formula:
> ```text
> n * (n + 1) * (2 * n + 1) / 6
> ```
> Substituting $n = 100$:
> ```text
> 100 * 101 * 201 / 6 = 338,350
> ```
> A true hacker mindset seeks unconventional, optimized solutions that bypass brute force to achieve the objective efficiently.

---

## Sub-Fields of Cybersecurity

Cybersecurity encompasses several specialized operational and analytical domains:

- **Application Security (AppSec):** Focuses on integrating security into every phase of the Software Development Life Cycle (SDLC). Emphasizes secure code reviews, static and dynamic analysis (SAST/DAST), dependency management, and bug mitigation.
- **Web Application Security:** A specialized branch of AppSec dedicated to securing web applications, APIs, and client-server interactions against flaws such as SQL Injection (SQLi), Cross-Site Scripting (XSS), Cross-Site Request Forgery (CSRF), and Insecure Direct Object References (IDOR).
- **Network Security:** Protects network infrastructure, data in transit, and boundary devices against unauthorized access, eavesdropping, and exploitation. Employs firewalls, Intrusion Detection/Prevention Systems (IDS/IPS), Virtual Private Networks (VPNs), and network segmentation.
- **Reverse Engineering (RE):** Analyzes compiled binary software, firmware, or hardware components through static and dynamic analysis to understand execution logic, uncover vulnerabilities, dissect malware, or inspect undocumented protocols.
- **Cloud Security:** Governs data, applications, virtual machines, containers, and serverless environments hosted in public or private cloud infrastructures (AWS, Azure, GCP). Key components include Identity and Access Management (IAM), infrastructure-as-code security, and cloud configuration audits.
- **Mobile Application Security:** Focuses on vulnerabilities unique to Android and iOS ecosystems, addressing local storage insecurities, reverse engineering of APK/IPA packages, insecure IPC, and insecure network transmission.
- **IoT (Internet of Things) Security:** Secures resource-constrained connected devices, sensors, and embedded firmware across industrial and consumer networks.
- **Digital Forensics and Incident Response (DFIR):** Involves forensically collecting, preserving, and analyzing electronic evidence from compromised endpoints, memory images, and logs to reconstruct cyber attacks for legal proceedings and post-incident remediation.
- **Threat Intelligence (CTI):** The systematic collection, enrichment, and analysis of data regarding threat actors, adversary campaigns, malware indicators of compromise (IOCs), and tactical attack patterns (TTPs) to enable proactive defense.

---

## Cybersecurity Careers & Industry Landscape

The rapid growth of cyber threats has driven massive investment by commercial enterprises and nation-states seeking to protect infrastructure and maintain strategic advantages.

### Industry Demand and Workforce Gap

![Cybersecurity Workforce Landscape](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/01489097-b2f5-43a9-baf8-18a9f1321e4d/cybersecurity-workforce-d8fd8334a.webp)

Industry metrics from **(ISC)²** show that while cybersecurity employment numbers grow year over year, the global workforce gap continues to widen. This deficit highlights the acute demand for skilled, hands-on practitioners who can bridge offensive insights and defensive operations.

### Career Journey & Mindset

Thriving in cybersecurity requires discipline, continuous self-directed learning, and persistence through complex technical hurdles. Key factors for professional growth include:
- **Technical Discipline:** Building foundational mastery in networking, operating system internals, scripting, and system architecture.
- **Soft Skills & Communication:** Technical findings must be translated into business risk. Clear report writing, teamwork, and cross-functional communication are critical.
- **Community & Professional Networking:** Active engagement with cybersecurity communities, conferences, CTF competitions, and technical forums (e.g., Discord, LinkedIn, local BSides).

Key self-assessment questions for entering the field:
1. *How deep is my curiosity regarding technical systems and how they break?*
2. *Can I maintain focus and problem-solve effectively when facing obscure errors?*
3. *How well do I manage stress when investigating high-impact incidents?*
4. *Can I collaborate transparently within a team during time-sensitive tasks?*

### Cybersecurity Operational Teams

Organizations structure their defensive and testing operations into collaborative teams:

- **Red Team (Offensive):** Emulates realistic threat-actor tactics, techniques, and procedures (TTPs) to test an organization's detection and response capabilities through authorized adversary simulations, social engineering, physical access testing, and covert network compromise.
- **Blue Team (Defensive):** Maintains, monitors, and protects organizational assets. Responsibilities include Security Operations Center (SOC) monitoring, SIEM tuning, vulnerability management, incident response, hardening network perimeter devices, and employee awareness training.
- **Purple Team (Collaborative Integration):** A collaborative engagement model where Red and Blue teams work side-by-side. The Red team executes specific attacks while the Blue team verifies detection rules and log coverage in real time, rapidly improving overall defensive posture.

### Bug Bounty Programs

Bug Bounty platforms provide a structured framework where organizations incentivize independent security researchers to uncover and responsibly report vulnerabilities within a defined scope. Researchers receive monetary bounties based on vulnerability severity, giving organizations continuous, diverse testing outside of periodic compliance audits.

---

## Penetration Testing (Pentest) Fundamentals

A **penetration test** is an authorized, simulated cyber attack against an organization's systems, network infrastructure, applications, or physical controls. Its goal is to uncover exploitable vulnerabilities, determine the potential business impact of a compromise, and provide validated remediation instructions.

> [!IMPORTANT] Legal & Contractual Authority
> Penetration tests must strictly adhere to a pre-defined legal contract and **Rules of Engagement (RoE)** signed by both the client and testing provider. The agreement establishes:
> - Explicit authorization (preventing legal liability under computer misuse laws).
> - Strict scoping (identifying approved targets, IP ranges, domains, and prohibited systems).
> - Testing timeframes and communication escalation channels.

Pentesters apply an ethical hacker mindset to systematically uncover and document flaws before malicious actors discover them.

### Penetration Testing Categories

Testing models differ based on the level of internal information provided to the tester:

| Model | Information Provided | Realism vs. Thoroughness | Practical Application |
| :--- | :--- | :--- | :--- |
| **White Box** | Complete disclosure (source code, architecture diagrams, credentials, network maps). | Deepest analysis; highly efficient for finding structural and edge-case code flaws. | Comprehensive code reviews, internal audits, high-security application assessments. |
| **Grey Box** | Partial disclosure (e.g., standard user credentials, high-level network topology). | Balanced; mimics an insider threat or an attacker who has achieved initial foothold. | Standard web app pentests, authenticated enterprise assessments. |
| **Black Box** | Zero prior knowledge (only target domain or external IP range provided). | Closest emulation of an unauthenticated external attacker; reconnaissance-heavy. | External perimeter assessments, adversary simulation exercises. |

### Penetration Testing Methodologies

Industry-standard frameworks ensure assessments are structured, repeatable, and comprehensive:

- **OWASP (Open Web Application Security Project):** The premier standard for web application and API security, providing comprehensive testing guides (e.g., OWASP Web Security Testing Guide / WSTG).
- **OSSTMM (Open Source Security Testing Methodology Manual):** A scientifically grounded, metrics-driven methodology for measuring operational security across human, physical, wireless, telecommunications, and data networks.
- **NIST (SP 800-115):** The National Institute of Standards and Technology technical guide to information security testing and assessment, widely adopted by government entities and enterprise compliance frameworks.

---

## Core Technical Concepts

### Network Ports & Protocols

A **port** is a logical communication endpoint used by transport layer protocols (TCP and UDP) to direct data to specific applications or services.
- **Physical Ports:** Hardware connection interfaces on physical endpoints (Ethernet RJ-45, USB, HDMI).
- **Virtual (Logical) Ports:** Numbered endpoints from `0` to `65535` that identify specific running services on an IP host.

```text
Port Range: 0 to 65535
  - Well-Known Ports:     0 to 1023 (Reserved for system/privileged services)
  - Registered Ports:     1024 to 49151 (Vendor and standard applications)
  - Dynamic/Private Ports: 49152 to 65535 (Ephemeral client connections)
```

Common standard ports and corresponding services:

| Port Number | Service / Protocol | Description / Security Relevance |
| :---: | :--- | :--- |
| `21` | **FTP** (File Transfer Protocol) | Cleartext file transfer; susceptible to credential sniffing unless secured with FTPS/SFTP. |
| `22` | **SSH** (Secure Shell) | Encrypted remote management; primary replacement for Telnet. |
| `23` | **Telnet** | Legacy cleartext terminal communication; transmits credentials in plain text. |
| `25` | **SMTP** (Simple Mail Transfer Protocol) | Mail transfer agent; often evaluated for open mail relay vulnerabilities. |
| `53` | **DNS** (Domain Name System) | Resolves domain names to IP addresses; UDP/TCP 53. Subject to cache poisoning and zone transfers. |
| `80` | **HTTP** (Hypertext Transfer Protocol) | Unencrypted web traffic; subject to MitM interception. |
| `443` | **HTTPS** (HTTP Secure) | HTTP over TLS/SSL; standard for encrypted web communications. |
| `3306` | **MySQL** | Relational database service; target for SQL injection and brute-force access. |
| `3389` | **RDP** (Remote Desktop Protocol) | Microsoft graphical remote management; common target for brute-force and exploit attacks (e.g., BlueKeep). |
| `5432` | **PostgreSQL** | Common open-source relational database port. |

### Vulnerability Management Terms

- **Vulnerability:** A flaw, bug, or weakness in system design, implementation, or configuration that exposes the system to compromise or exploitation.
- **OWASP Top 10:** A regularly updated consensus report ranking the top ten most critical web application security risks.
- **CVE (Common Vulnerabilities and Exposures):** A standardized, publicly accessible catalog of known cybersecurity vulnerabilities identified by unique IDs (e.g., `CVE-2021-44228`).
- **CVSS (Common Vulnerability Scoring System):** An open industry standard providing a numerical score (0.0 to 10.0) reflecting the severity, exploitability, and impact of a vulnerability.

### Exploits & Proof of Concepts

- **Exploit:** Tailored code, a script, or a specialized technique that takes advantage of a specific vulnerability to force unintended behavior, execute arbitrary commands, or gain unauthorized privileges.
- **PoC (Proof of Concept):** Demonstration code or a documented procedure verifying that a reported vulnerability is genuinely exploitable, without executing destructive payloads.
- **Zero-Day (0-day):** A vulnerability unknown to the software vendor or public, with zero days of vendor patch availability. Exploits targeting these are highly prized by sophisticated adversaries.

### Shell Access Types

A **shell** is an interactive command-line environment or program that provides direct access to the underlying operating system kernel and services. Gaining a remote shell is a primary offensive objective during penetration testing.

- **Bind Shell:** The exploit payload binds a listener on a specific port on the target machine. The attacker connects directly to that open port to receive a command prompt.  
  *Limitation:* Often blocked by target inbound firewall rules and NAT.
- **Reverse Shell:** The target host initiates an outbound connection back to an attacker-controlled listener machine on a designated IP and port.  
  *Advantage:* Bypasses most ingress firewall restrictions because outbound client traffic is typically permitted.
- **Web Shell:** A malicious script (written in PHP, ASPX, JSP, Python, etc.) uploaded to an accessible web server directory. When requested over HTTP/HTTPS, it executes operating system commands via web requests, granting backdoor persistence.

### IP (Internet Protocol) Addressing

IP addresses provide unique numerical identifiers assigned to devices on a network to facilitate routing and packet delivery:
- **IPv4 (Internet Protocol version 4):** A 32-bit addressing scheme written in dotted-decimal format (e.g., `192.168.1.1`), providing $2^{32} \approx 4.29$ billion unique addresses. Exhaustion of IPv4 addresses led to widespread use of NAT (Network Address Translation).
- **IPv6 (Internet Protocol version 6):** Developed to overcome IPv4 address depletion. Uses a 128-bit addressing format written in hexadecimal notation separated by colons (e.g., `fe80::1ff:fe23:4567:890a`), providing $2^{128}$ unique addresses.

---

## Practical Reference & Course Resources

Reference guides and tool documentation from the course material:

- **[Explore Pentesting Tactics](https://hackviser.com/tactics/pentesting):** Detailed practical scenarios, penetration testing methodology steps, and workflow breakdowns.
- **[Explore Hardening Tactics](https://hackviser.com/tactics/hardening):** Defensive baseline recommendations, configuration guidelines, and system hardening playbooks.
- **[Explore Tools Tactics](https://hackviser.com/tactics/tools):** Operational usage guides for essential cybersecurity security auditing and exploitation tools.
