---
title: Web Fundamentals & Architecture
category: Network and Web Fundamentals
tags:
  - web-fundamentals
  - dns
  - http
  - client-server
  - networking
---

# Web Fundamentals & Architecture

## Overview & Foundations

Understanding how modern web applications function and how data flows across the internet is a fundamental prerequisite for web application penetration testing, vulnerability assessment, and defensive engineering. This module examines the core architecture of the World Wide Web, the client-server interaction model, Domain Name System (DNS) resolution, and the Hypertext Transfer Protocol (HTTP).

---

## The World Wide Web & Internet Architecture

### World Wide Web (WWW)

The **World Wide Web (WWW)** is a globally distributed information system operating on top of the physical internet, organized via interconnected hypertext documents. Hypertext allows documents to link directly to other documents, enabling users and automated agents to navigate seamlessly across resources.

The Web was invented in 1989 by British computer scientist **Tim Berners-Lee** while working at CERN (the European Organization for Nuclear Research). CERN hosted over 1,700 scientists across 100+ nations who needed an automated, reliable system to exchange research findings across disparate computing platforms.

Berners-Lee synthesized three foundational technologies that remain the cornerstone of the web:
- **HTML (HyperText Markup Language):** The structural formatting language used to author and render web documents.
- **URL (Uniform Resource Locator):** The standardized global addressing scheme used to locate specific web resources.
- **HTTP (HyperText Transfer Protocol):** The application-level protocol governing communication and data exchange between clients and servers.

### Internet vs. World Wide Web

Although colloquially used interchangeably, the **Internet** and the **Web** are fundamentally distinct layers of computing:

```text
┌────────────────────────────────────────────────────────┐
│               World Wide Web (HTTP / HTTPS)            │
│  (Other Services: Email [SMTP/IMAP], File Transfer     │
│   [FTP/SFTP], Remote Shell [SSH], DNS, VoIP)           │
├────────────────────────────────────────────────────────┤
│           Internet Global Network Infrastructure       │
│           (TCP/IP, Routers, Fiber, Satellites)         │
└────────────────────────────────────────────────────────┘
```

- **The Internet:** The underlying global physical and logical network infrastructure connecting millions of computers and autonomous systems (originating from ARPANET in the late 1960s). It provides the transport substrate for diverse services including email (SMTP, IMAP), file transfer (FTP, SFTP), network time (NTP), voice communications, and remote administration (SSH).
- **The World Wide Web:** A specific information-sharing service operating *on top of* the internet infrastructure. The web utilizes HTTP/HTTPS to transmit hypertext documents and media across the underlying network.

---

## The Client-Server Model

Modern web communications operate on a distributed **client-server architecture**. In this architecture, a client requests services or resources, and a server processes the request and delivers a response.

![Client and Server Model](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/eab4bd3d-cb57-49a6-886a-b6356ec4047c/client-server-model-02a1198f4.webp)

- **Clients:** End-user hardware running client-side software (web browsers such as Google Chrome, Mozilla Firefox, or command-line clients like `curl` and `burpsuite`). Clients parse HTML, execute JavaScript, render CSS, and transmit user interactions back to servers.
- **Servers:** Dedicated, high-availability host machines running web server software (Apache HTTP Server, Nginx, Microsoft IIS, Caddy) listening 24/7 on designated network ports (`80` for HTTP, `443` for HTTPS). Servers process application logic, query backend databases, and serve structured responses.

This centralized model minimizes operational overhead by storing data and business logic centrally, allowing clients to query resources on demand.

---

## Domain Name System (DNS)

The **Domain Name System (DNS)** is the distributed, hierarchical directory system of the internet—often described as the "phone book" of the web. It translates human-friendly domain names (e.g., `www.example.com`) into numerical IP addresses (e.g., `192.0.2.1` or `2606:2800:220:1:248:1893:25c8:1946`) required for routing packets across IP networks.

### Historical Evolution: `hosts.txt` to RFC 882/883
During the early ARPANET era, host-to-IP resolution was managed through a single centralized text file called `hosts.txt`, maintained by the Stanford Research Institute (SRI) and manually fetched by network nodes. As host counts expanded exponentially, this static flat file became impossible to maintain.

In 1983, **Paul Mockapetris** and **Jon Postel** introduced modern DNS via **RFC 882** and **RFC 883** (later updated by RFC 1034 and RFC 1035), establishing a dynamic, decentralized, hierarchical database capable of scaling worldwide.

### Domain Name Hierarchy

Domain names are structured in a hierarchical namespace read from right to left, separated by dots:

```text
   www.      example.        com.
  [Subdomain] [SLD]       [TLD]   [Root]
```

- **Root Domain:** Represented implicitly by a trailing dot (`.`), managed by global root servers.
- **Top-Level Domain (TLD):** The highest recognized level in the domain hierarchy.
  - *Generic TLDs (gTLDs):* Broad functional designations such as `.com` (commercial), `.org` (non-profit), `.gov` (government), `.edu` (education), as well as modern specialized extensions like `.tech`, `.photography`, and `.guru`.
  - *Country Code TLDs (ccTLDs):* Geographically tied two-letter suffixes such as `.uk` (United Kingdom), `.de` (Germany), `.ca` (Canada).
- **Second-Level Domain (SLD):** The organization-specific name registered under the TLD (e.g., `example` in `example.com`).
- **Subdomain:** Hierarchical prefixes designated by the domain owner to delegate specific sub-services or environments (e.g., `www`, `mail`, `api`, `dev`).

### Core DNS Server Types

Resolving an un-cached domain name relies on three primary classes of DNS servers:

1. **Root Name Servers:**  
   Thirteen root server clusters (named `A` through `M`, replicated globally via Anycast routing) sit at the apex of the DNS hierarchy. They do not store individual IP addresses; instead, they direct queries to the authoritative TLD name servers for the requested extension (e.g., pointing `.com` queries to Verisign's TLD servers).
2. **Top-Level Domain (TLD) Name Servers:**  
   Servers responsible for managing all domains registered within a specific TLD. They return the authoritative name server addresses for the requested second-level domain (e.g., pointing `example.com` to its designated authoritative nameservers).
3. **Authoritative Name Servers:**  
   The definitive, official source of truth for a specific domain. Maintained by hosting providers, domain registrars, or enterprises, authoritative servers hold the actual DNS zone files and records (`A`, `AAAA`, `CNAME`, `MX`, `TXT`, `NS`).
4. **Recursive Resolvers (DNS Resolvers):**  
   Typically operated by Internet Service Providers (ISPs), enterprise network teams, or public resolvers (e.g., Google `8.8.8.8`, Cloudflare `1.1.1.1`). Resolvers act as intermediaries on behalf of client devices, querying root, TLD, and authoritative servers sequentially to resolve the target IP while aggressively caching results to accelerate subsequent lookups.

### The 7-Step DNS Resolution Process

```text
[User Client] ──1. Query www.example.com──> [Recursive Resolver]
     ▲                                            │   ▲
     │                                     2.     │   │ 3.
     │                                            ▼   │
     │                                      [Root DNS Server]
     │                                            │   ▲
     │                                     4.     │   │ 5.
     │                                            ▼   │
     │                                      [TLD Server (.com)]
     │                                            │   ▲
     │                                     6.     │   │ 7.
     │                                            ▼   │
     └──────────── 8. Return IP ────────── [Authoritative NS]
```

1. **User Request:** A user inputs `www.example.com` into a web browser. The OS checks local DNS cache and `hosts` file.
2. **Querying the Recursive Resolver:** If not cached locally, the OS transmits a recursive DNS query to the configured local resolver.
3. **Contacting Root Servers:** If the resolver lacks a cached record, it queries a Root Name Server.
4. **Referral to TLD Servers:** The Root Server responds with the IP address of the `.com` TLD Name Server.
5. **Contacting TLD Servers:** The resolver queries the `.com` TLD server for `example.com`.
6. **Referral to Authoritative Nameservers:** The TLD server provides the nameserver (`NS`) records pointing to the Authoritative Name Server for `example.com`.
7. **Querying Authoritative Nameservers:** The resolver requests the host record (`A` record) directly from the Authoritative Server.
8. **Final IP Delivery:** The Authoritative Server returns the resolved IP address (`192.0.2.1`). The recursive resolver caches this record according to its **Time to Live (TTL)** and delivers the IP to the client browser, which initiates an HTTP/HTTPS connection.

---

## Hypertext Transfer Protocol (HTTP)

The **Hypertext Transfer Protocol (HTTP)** is a stateless, application-layer protocol designed to transfer hypertext documents and arbitrary binary payloads across network connections. It operates via an explicit request-response paradigm over TCP (typically port `80` for plaintext HTTP, and port `443` for TLS-encrypted HTTPS).

### Anatomy of an HTTP Request

An HTTP request consists of four primary structural elements:
1. **Request Line:** Contains the HTTP Method, Target URI Path, and Protocol Version.
2. **Request Headers:** Key-value metadata pairs supplying client context, authentication credentials, and encoding preferences.
3. **Empty Line (CRLF):** A carriage return and line feed separating headers from the payload body.
4. **Request Body (Optional):** Arbitrary data payloads (e.g., JSON parameters, URL-encoded form submissions, file bytes).

#### Basic Request Example
```http
GET /index.html HTTP/1.1
Host: www.example.com
Cookie: SESSIONID=badb655ebd99ed6c8e58c0a1aab44eb9
```
- `GET /index.html HTTP/1.1`: Requests the `/index.html` resource using the `GET` verb under protocol version `HTTP/1.1`.
- `Host: www.example.com`: Mandated by HTTP/1.1 to facilitate **Virtual Hosting**, enabling a single IP address to host multiple distinct domain websites.
- `Cookie: SESSIONID=...`: Supplies state information, linking the stateless HTTP connection to an authenticated user session on the server.

### Standard HTTP Request Methods (Verbs)

HTTP methods indicate the intended semantic action to be executed on the target resource:

| HTTP Method | Idempotent | Description & Purpose |
| :--- | :---: | :--- |
| **GET** | Yes | Retrieves data from the specified resource. Must not alter server state. |
| **POST** | No | Submits data to be processed by the server (e.g., form submissions, logins). |
| **PUT** | Yes | Uploads a complete representation to create or completely replace the target resource. |
| **DELETE** | Yes | Deletes the specified target resource. |
| **HEAD** | Yes | Identical to `GET`, but requests server headers only, omitting the response body. |
| **OPTIONS** | Yes | Queries supported HTTP methods and capabilities for a resource (frequently used in CORS preflight). |
| **PATCH** | No | Applies partial modifications to a specified resource. |
| **CONNECT** | No | Establishes a transparent bidirectional network tunnel (commonly used for HTTPS proxies). |
| **TRACE** | Yes | Performs a message loop-back diagnostic test along the path to the target resource. |

> [!NOTE] Idempotence Explained
> An HTTP method is **idempotent** if making multiple identical requests produces the exact same outcome on the server as making a single request (e.g., `GET`, `PUT`, `DELETE`). Non-idempotent methods (`POST`) may produce unintended side-effects if repeated (such as duplicate credit card charges).

### Essential HTTP Request Headers

Headers are transmitted in `Header-Name: Value` format:

```http
GET /api/info HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.82 Safari/537.36
Accept: application/json
Accept-Language: en-US,en;q=0.5
Authorization: Token secret123
Cache-Control: no-cache
Connection: keep-alive
Referer: https://www.google.com/
Pragma: no-cache
```

| Header Name | Purpose & Security Context |
| :--- | :--- |
| **Host** | Specifies the server domain name. Essential for virtual host routing. |
| **User-Agent** | Identifies the client software, OS version, and rendering engine. Frequently analyzed in bot detection. |
| **Accept** | Informs the server of client-supported MIME content types (e.g., `application/json`, `text/html`). |
| **Accept-Language** | Communicates preferred client locale and language rankings. |
| **Authorization** | Contains credentials (e.g., Bearer tokens, Basic base64 credentials) for authenticating the client. |
| **Cache-Control** | Directives controlling browser and intermediate proxy caching behaviors (`no-cache`, `private`). |
| **Connection** | Controls connection persistence (`keep-alive` to reuse existing TCP connections, `close` to terminate). |
| **Referer** | The URL of the preceding webpage that linked to the requested resource. |
| **Pragma** | Legacy implementation-specific directive (`Pragma: no-cache`) maintained for backwards compatibility with HTTP/1.0. |
| **Content-Type** | Declares the MIME type of data transmitted in the request body (`application/json`, `multipart/form-data`). |

### HTTP Request Body (Multipart Form Upload)

When transmitting structured data or uploading binary media via `POST`, `PUT`, or `PATCH`, data is packed inside the HTTP Request Body:

```http
POST /v1/photos/upload HTTP/1.1
Host: api.myphotos.com
x-api-key: API_KEY
Content-Length: 232
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="/C:/Users/John/Photos/my_photo.jpg"
Content-Type: image/jpeg

(raw binary image data)
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```
In this upload request:
- `Content-Type: multipart/form-data; boundary=...` defines a unique delimiter string isolating individual form elements.
- The server parses the boundary to extract file metadata and raw image bytes, saving the file to storage and registering the database record.

### Anatomy of an HTTP Response

A server replies to a client request with a structured HTTP response:

```http
HTTP/1.1 200 OK
Date: Sun, 28 Mar 2023 10:15:00 GMT
Content-Type: application/json
Server: Apache/2.4.39 (Unix) OpenSSL/1.1.1c PHP/7.3.6
Content-Length: 1024

{
    "name": "John Doe",
    "email": "johndoe@example.com"
}
```

- **Status Line:** Outlines the HTTP version (`HTTP/1.1`), the 3-digit numerical Status Code (`200`), and a human-readable Reason Phrase (`OK`).
- **Response Headers:** Server metadata, caching headers, content lengths, and cookie directives (`Set-Cookie`).
- **Response Body:** The resource payload requested by the client (JSON data, HTML markup, image binaries).

### Common HTTP Status Codes

Status codes are grouped into five distinct functional classes:

| Class | Status Code | Reason Phrase | Semantic Meaning |
| :---: | :---: | :--- | :--- |
| **1xx** | `100` | Continue | Server received initial request headers; client should proceed to transmit body. |
| **1xx** | `101` | Switching Protocols | Server agrees to upgrade connection (e.g., upgrading HTTP/1.1 to WebSockets). |
| **2xx** | `200` | OK | Standard successful response for `GET`, `POST`, etc. |
| **2xx** | `201` | Created | The request succeeded and led to the creation of a new resource (common in REST APIs). |
| **2xx** | `202` | Accepted | Request accepted for processing, but processing has not yet completed. |
| **2xx** | `203` | Non-Authoritative Info | Request succeeded, but payload modified by an intermediate transforming proxy. |
| **3xx** | `301` | Moved Permanently | Target resource permanently relocated to a new URL provided in the `Location` header. |
| **4xx** | `404` | Not Found | Requested resource does not exist on the server. |
| **5xx** | `500` | Internal Server Error | Server encountered an unhandled exception or critical misconfiguration. |

---

## End-to-End Web Request Lifecycle

When a user visits a website (e.g., `https://example.com/login`), the operating system, network stack, and browser execute a coordinated 6-step sequence:

```text
[Step 1: URL Entry] 
        │
        ▼
[Step 2: DNS Resolution] ─────────► Converts example.com to 93.184.216.34
        │
        ▼
[Step 3: TCP & TLS Handshake] ────► TCP SYN/ACK (Port 443) + TLS 1.3 Key Exchange
        │
        ▼
[Step 4: HTTP Request Sent] ──────► GET /login HTTP/1.1 (Encrypted over TLS)
        │
        ▼
[Step 5: Server Processes & Replies] ──► HTTP 200 OK with HTML Body
        │
        ▼
[Step 6: Browser Rendering & Sub-Resources] ──► Fetches CSS/JS/Images, executes DOM
        │
        ▼
[Step 7: Connection Maintenance] ──► Keep-Alive maintains socket for subsequent requests
```

1. **URL Entry & Parsing:** The user types the URL. The browser breaks the string into protocol (`https`), hostname (`example.com`), and path (`/login`).
2. **DNS Resolution:** The browser queries local cache, OS cache, and recursive resolvers to identify the target IP address.
3. **Transport Connection & Encryption:** The browser initiates a TCP three-way handshake (SYN, SYN-ACK, ACK) on port `443`, followed immediately by a TLS cryptographic handshake to establish session encryption keys.
4. **HTTP Request Transmission:** The browser formats and transmits the HTTP `GET` request over the encrypted TLS tunnel.
5. **Server Processing & Response:** Web server software (Nginx/Apache) routes the request to application handlers, queries backend databases, formats response headers and HTML markup, and transmits an `HTTP 200 OK` response back to the client.
6. **DOM Rendering & Asset Resolution:** The browser parses the returned HTML. Upon encountering linked stylesheets, scripts, or images, it fires parallel secondary HTTP requests to fetch those dependent assets.
7. **Connection Teardown / Re-use:** In HTTP/1.1 and HTTP/2, TCP connections remain open via `keep-alive` to service immediate future requests, minimizing handshake latency until an idle timeout closes the socket.
