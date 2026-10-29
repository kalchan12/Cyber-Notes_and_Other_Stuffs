---
title: Computer Network Fundamentals & Architecture
category: Network and Web Fundamentals
tags:
  - networking
  - osi-model
  - tcp-ip
  - subnetting
  - protocols
  - hardware
---

# Computer Network Fundamentals & Architecture

## Overview & Foundations

A computer network is an interconnected collection of autonomous computing devices (workstations, servers, network appliances, mobile endpoints) capable of exchanging digital data and sharing hardware/software resources. The global internet represents the largest computer network in existence, linking billions of heterogenous endpoints via standardized communication protocols.

### Network Standards & Governing Bodies

Protocols establish the deterministic syntactic and semantic rules governing data transmission, packet framing, error detection, and synchronization across network boundaries.

Key international standards organizations include:

- **IEEE (Institute of Electrical and Electronics Engineers):** Develops foundational physical and data-link layer engineering standards. Most notably, the **IEEE 802** project defines standards for Local Area Networks (LAN) and Metropolitan Area Networks (MAN), including **IEEE 802.3** (Ethernet) and **IEEE 802.11** (Wi-Fi).
- **IETF (Internet Engineering Task Force):** An open international community that governs internet architecture and operational protocol specifications. Standards are authored, reviewed, and published as **RFC (Request for Comments)** documents (e.g., RFC 791 for IPv4, RFC 793 for TCP).
- **ISO (International Organization for Standardization):** A global non-governmental body establishing worldwide industrial and technology standards, including the **ISO/IEC 7498-1** OSI 7-Layer Reference Model and the **ISO/IEC 27000** information security management system (ISMS) family.
- **ITU (International Telecommunication Union):** A specialized United Nations agency managing global radio spectrum allocation and telecommunication standards via sectors like **ITU-T** (telecommunications standardization) and **ITU-R** (radiocommunications).
- **ANSI (American National Standards Institute):** Coordinates US voluntary standards with international frameworks, ensuring hardware and interface interoperability.
- **W3C (World Wide Web Consortium):** Directs the evolution of World Wide Web protocols and standards (HTML, CSS, XML, WebAssembly) to preserve cross-platform open web compatibility.

---

## Network Classifications by Geographic Scope

Networks are classified primarily by the physical and geographic span over which connected endpoints are distributed:

```text
┌────────────────────────────────────────────────────────┐
│                      WAN (Global)                      │
│   ┌────────────────────────────────────────────────┐   │
│   │                   MAN (City)                   │   │
│   │   ┌────────────────────────────────────────┐   │   │
│   │   │               LAN (Campus)             │   │   │
│   │   └────────────────────────────────────────┘   │   │
│   └────────────────────────────────────────────────┘   │
└────────────────────────────────────────────────────────┘
```

### Local Area Network (LAN)
- **Geographic Scope:** Confined to a localized physical area (a single room, office, home, or multi-story commercial building).
- **Performance Characteristics:** High bandwidth throughput (typically 1 Gbps to 10 Gbps and beyond) with ultra-low latency.
- **Resource Sharing:** Enables seamless local sharing of storage arrays (NAS), print servers, and local application clusters.
- **Management & Security:** Centrally managed by internal IT teams using boundary switches, local firewalls, and 802.1Q Virtual LANs (VLANs).

### Metropolitan Area Network (MAN)
- **Geographic Scope:** Spans a contiguous regional or municipality-wide area (e.g., interconnected municipal public safety networks, distributed university campuses, or city-wide banking systems).
- **Infrastructure:** Typically deployed using high-capacity optical fiber backbones (Dark Fiber, DWDM) managed by telecom carriers or municipal utility consortia.
- **Placement:** Bridges the architectural gap between localized LANs and broad WAN backbones.

### Wide Area Network (WAN)
- **Geographic Scope:** Spans vast geographic territories across cities, nations, and continents. The public Internet is the prime archetype of a global WAN.
- **Underlying Media:** Relies on long-haul transit media, including undersea transoceanic fiber cables, commercial telecommunication leased lines (MPLS), and satellite links.
- **Characteristics:** Higher operational costs, lower relative throughput speeds, and higher transit latency compared to localized LAN architectures.

### Internet vs. Intranet
- **Internet:** A globally accessible, public confederation of interconnected autonomous systems (AS) routing packets via BGP across public IP space.
- **Intranet:** A strictly perimeterized, private corporate network restricted to internal employees and authenticated organizational assets, concealing internal services from public exposure.

---

## Network Hardware & Boundary Devices

Network hardware directs, inspects, and converts physical and electrical signals into logical frames and routable packets.

### Network Interface Card (NIC)
A **Network Interface Card (NIC)** provides the physical and data-link connection between a host's internal system bus and the network medium.
- **Layer Operation:** Physical (Layer 1) and Data Link (Layer 2).
- **Hardware Addressing:** Each NIC is assigned a globally unique 48-bit **Media Access Control (MAC)** address hardcoded by the manufacturer.
- **Types:**
  - *Wired NIC:* Connects via copper twisted-pair (RJ-45) or modular fiber optic transceivers (SFP/SFP+).
  - *Wireless NIC (WNIC):* Transceives RF signals using IEEE 802.11 standards.

### Hub (Legacy Layer 1 Repeater)
- **Operation:** A legacy multi-port repeater operating strictly at Layer 1.
- **Behavior:** Incoming frames received on one port are blindly repeated (flooded) out of all other connected ports.
- **Security & Collision Flaw:** Creates a single large **Collision Domain** and **Broadcast Domain**. Any connected host running a packet sniffer in promiscuous mode can intercept all network traffic traversing the hub.

### Switch (Layer 2 Bridge)
- **Operation:** An intelligent multiport Layer 2 device that inspects source and destination MAC addresses in received frames.
- **MAC Address Table (CAM Table):** The switch learns host locations by inspecting incoming frame source MAC addresses and builds a Content Addressable Memory (CAM) table mapping MACs to physical switch ports.
- **Forwarding Logic:** Unicast traffic is forwarded *solely* to the specific port hosting the target MAC address, eliminating collisions and segmenting collision domains.
- **Switch Classifications:**
  - *Unmanaged Switch:* Fixed-configuration, plug-and-play device without configuration interfaces or VLAN support.
  - *Managed Switch:* Enterprise-grade appliance supporting VLANs (802.1Q), Spanning Tree Protocol (STP), port mirroring (SPAN), SNMP monitoring, and Quality of Service (QoS).
  - *Smart Switch:* Web-managed switch offering an intermediate feature set suitable for smaller networks.

### Router (Layer 3 Gateway)
- **Operation:** A Layer 3 internetworking device that forwards packets between distinct logical IP networks based on Layer 3 IP routing tables.
- **Segmentation:** Routers break up both collision domains and **Broadcast Domains**.
- **Network Address Translation (NAT):** Translates private RFC 1918 addresses into globally routable public IPs.

### Modem (Modulator-Demodulator)
Converts digital signals from computing devices into analog transmission signals (and vice versa) for transport across ISP telecom infrastructure:
- **Dial-Up:** Uses legacy analog telephone lines (PSTN) with maximum speeds up to 56 kbps.
- **DSL (ADSL/VDSL):** Transmits high-frequency digital signals simultaneously over standard twisted-pair copper telephone wires.
- **Cable Modem (DOCSIS):** Operates over coaxial broadband cable television lines.
- **Fiber Optic Optical Network Terminal (ONT):** Converts optical light pulses from fiber backbones into digital Ethernet signals.
- **Cellular Wireless Modem:** Interfaces with cellular radio networks (3G, 4G LTE, 5G).

### Wireless Access Point (AP)
A Layer 2 appliance bridging wireless RF clients (IEEE 802.11) with the wired Ethernet distribution system.

### Firewall
A boundary security appliance that inspects and filters incoming and outgoing network traffic based on configured security rulebases:
- **Packet-Filtering Firewall (Stateless / Layer 3-4):** Inspects individual packet headers (Source IP, Destination IP, Source Port, Destination Port, Protocol) in isolation without tracking session connection state.
- **Stateful Inspection Firewall (Layer 4):** Maintains an active state connection table. Automatically permits return traffic corresponding to legitimate outbound connections.
- **Application Layer / Proxy Firewall (Layer 7):** Terminates client connections and inspects full application payloads (HTTP headers, URLs, payload data) before initiating a secondary connection to backend servers.
- **Next-Generation Firewall (NGFW):** Combines stateful inspection with deep packet inspection (DPI), integrated Intrusion Prevention Systems (IPS), TLS decryption, and application/user awareness.

---

## Physical Transmission Media & Cabling

### 1. Coaxial Cable

![Coaxial Cable](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/coaxial-cable-a88b4944e.webp)

Features a central copper conductor surrounded by a dielectric insulator, metallic braided shielding (to mitigate electromagnetic interference / EMI), and an outer jacket:
- **RG-6:** Standard for broadband cable internet (DOCSIS modems) and satellite TV.
- **RG-59:** Common in legacy analog CCTV video surveillance.
- **RG-58:** Used in legacy 10Base2 ("Thinnet") bus networks (50-ohm impedance).
- **RG-8:** Used in early 10Base5 ("Thicknet") bus backbones.

#### Connectors
- **BNC (Bayonet Neill–Concelman):** Twist-and-lock connector used in legacy 10Base2 networks and laboratory RF gear.  
  ![BNC Connector](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/bnc-connector-09e4d8879.webp)
- **F-Type Connector:** Threaded screw-on connector standard for cable modems and satellite receivers.  
  ![F-Connector](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/F-Connector-02ec4b9ea.webp)

---

### 2. Twisted-Pair Copper Cabling

![Twisted Pair Cable](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/twisted-pair-cable-ec47795ab.webp)

The primary physical medium for LAN deployments. Copper pairs are twisted symmetrically to cancel electromagnetic interference and crosstalk via **differential signaling**.
- **UTP (Unshielded Twisted Pair):** Standard office and residential cable lacking metallic foil shielding.
- **STP (Shielded Twisted Pair):** Features individual foil or braided outer shielding to suppress extreme industrial EMI.

#### Twisted Pair Standards

![Twisted Cable Pair Standards](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/twistedCablePairStandards-5ed5bfa59.webp)

| Category | Max Bandwidth / Frequency | Max Speed Rating | Typical Application |
| :--- | :--- | :--- | :--- |
| **Cat 3** | 16 MHz | 10 Mbps | Legacy 10Base-T Ethernet & analog telephony. |
| **Cat 5** | 100 MHz | 100 Mbps | Fast Ethernet (100Base-TX). |
| **Cat 5e** | 100 MHz | 1 Gbps (1000Base-T) | Enhanced shielding; standard for Gigabit Ethernet up to 100 meters. |
| **Cat 6** | 250 MHz | 10 Gbps (up to 55m) | High-speed enterprise LAN backbones. |
| **Cat 6a** | 500 MHz | 10 Gbps (full 100m) | Augmented shielding for data centers and server links. |
| **Cat 7** | 600 MHz | 10 Gbps | Fully shielded (S/FTP) industrial deployments. |

#### Modular Connectors
- **RJ-11:** 4-pin or 6-pin modular connector used for analog landline telephone connections.  
  ![RJ-11](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/rj11-0feebe456.webp)
- **RJ-45 (8P8C):** 8-position, 8-contact connector standard for twisted-pair Ethernet cables.  
  ![RJ-45](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/rj45-6c439aada.webp)
- **DB-9:** 9-pin serial connector used for RS-232 serial console management on routers and switches.  
  ![DB-9](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/db9-a084e73f8.webp)

#### TIA/EIA 568A & 568B Pinout Standards

![TIA/EIA 568A and 568B Wiring Standards](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/TIA-EIA-568A-and-568B-Wiring-Standards-9a691875d.webp)

- **T568A Pin Order:** White/Green, Green, White/Orange, Blue, White/Blue, Orange, White/Brown, Brown.
- **T568B Pin Order:** White/Orange, Orange, White/Green, Blue, White/Blue, Green, White/Brown, Brown. *(Widely adopted commercial standard).*

#### Straight-Through vs. Crossover Cabling
- **Straight-Through Cable:** Both cable ends terminate with the identical wiring standard (e.g., T568B to T568B). Connects dissimilar devices:
  - Host to Switch
  - Router to Switch
- **Crossover Cable:** One end terminates in T568A and the other in T568B (swapping transmit pins 1 & 2 with receive pins 3 & 6). Used to connect identical devices directly:
  - Host to Host
  - Switch to Switch
  - Router to Router
- **Auto-MDIX (Automatic Medium-Dependent Interface Crossover):** Modern Ethernet interfaces automatically detect and reconfigure internal pin routing dynamically, making manual crossover cables largely obsolete.

---

### 3. Fiber Optic Cabling

![Fiber Optic Cable](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/fiberoptic-cable-ce82871a1.webp)

Transmits information as modulated light pulses through flexible glass or silica cores. Completely immune to electromagnetic interference (EMI), radio frequency interference (RFI), and electronic eavesdropping.

- **Multimode Fiber (MMF):**
  - Core diameter: 50 to 62.5 microns.
  - Employs inexpensive LED or VCSEL light sources. Light rays bounce at multiple reflective angles (modes).
  - Subject to modal dispersion; effective distance limited to ~2 km (ideal for intra-building LANs).
- **Singlemode Fiber (SMF):**
  - Ultra-narrow core diameter: ~9 microns.
  - Employs calibrated infrared laser transmitters. Light travels along a direct single axial mode.
  - Virtually no modal dispersion; supports spans of 100+ km at speeds up to 400 Gbps.

#### Fiber Connectors
- **LC (Lucent Connector):** Compact small-form-factor push-pull connector standard for high-density patch panels and SFP transceivers.  
  ![LC Connector](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/lc-connector-bc95bef1a.webp)
- **SC (Subscriber / Standard Connector):** Square push-pull latching connector common in gigabit optical equipment.  
  ![SC Connector](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/sc-connector-f2eabc00d.webp)
- **MTRJ (Mechanical Transfer-Registered Jack):** Duplex connector enclosing two fibers within a single compact body.  
  ![MTRJ Connector](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/9a8044d8-66f6-4186-b4ea-e0c238721ec0/mtrj-connector-86765e8ea.webp)

---

## Network Topologies

Network topology describes the physical arrangement of cabling or the logical traversal of data signals across endpoints:

- **Star Topology:** All endpoints terminate into a central hub or switch.  
  *Pros:* Easy to isolate faults; failure of one cable affects only that single endpoint.  
  *Cons:* The central switch represents a single point of failure (SPoF).
- **Ring Topology:** Devices connect in a closed circular loop. Tokens or frames circulate sequentially in one direction.  
  *Pros:* Deterministic transmission with zero frame collisions.  
  *Cons:* A break in the physical ring interrupts connectivity unless dual redundant counter-rotating rings (FDDI) are deployed.
- **Bus Topology:** Legacy architecture where all stations connect along a shared linear coaxial trunk terminated with 50-ohm resistors.  
  *Pros:* Low initial cable requirements.  
  *Cons:* Difficult to troubleshoot; a cable break collapses the entire segment.
- **Tree Topology:** A hierarchical arrangement combining multiple star networks into tiered aggregation layers (Core, Distribution, Access).
- **Mesh Topology:**
  - *Full Mesh:* Every node maintains a dedicated point-to-point physical connection to every other node ($N(N-1)/2$ connections). Provides complete fault tolerance and zero single points of failure.
  - *Partial Mesh:* Redundant paths connect mission-critical routers while edge nodes maintain fewer links to control costs.
- **Peer-to-Peer (P2P):** Decentralized topology where nodes function simultaneously as both clients and servers without centralized directory controllers.
- **Hybrid Topology:** Combines two or more distinct topological paradigms (e.g., Star-Wired Ring, Star-Bus).

---

## Communication Modes & Transmission Casts

### Transmission Directional Modes
- **Simplex:** Unidirectional transmission. Data travels exclusively from sender to receiver (e.g., keyboard input to motherboard, broadcast television).
- **Half-Duplex:** Bidirectional transmission, but only one party can transmit at any given instant (e.g., walkie-talkies, legacy hub-based Ethernet using CSMA/CD).
- **Full-Duplex:** Simultaneous bidirectional transmission. Both stations send and receive simultaneously over dedicated transmit/receive pairs (e.g., switched Ethernet, telephone voice circuits).

### Communication Cast Types
- **Unicast:** Point-to-point delivery from one sender to one designated receiver (e.g., standard HTTP web browsing).
- **Multicast:** Point-to-multipoint delivery from one sender to an opted-in group of subscribed receivers (e.g., IPTV streams, OSPF routing updates via `224.0.0.5`).
- **Broadcast:** One-to-all transmission from one sender to every active endpoint on the local Layer 2 broadcast domain (e.g., ARP requests, DHCP Discover queries).

---

## Layered Network Architecture Models

Layered abstraction models decompose network operations into modular functional boundaries. Lower layers provide transport services to upper layers while remaining protocol-independent.

### OSI 7-Layer Reference Model

![OSI Encapsulation](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/634c04be-ed2c-43a1-99c8-5162bd190d73/osi-encapsulation-DeEncapsulation-2-5d7a355a0.webp)

Published by ISO in 1984, the OSI model defines seven discrete layers:

![OSI Model Architecture](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/634c04be-ed2c-43a1-99c8-5162bd190d73/osi-model-921d34ed6.webp)

1. **Layer 7 - Application Layer:** Provides human-to-network interfaces and application services (HTTP, FTP, SMTP, DNS).
2. **Layer 6 - Presentation Layer:** Manages data formatting, character encoding (ASCII, UTF-8), compression, and cryptographic serialization/encryption (TLS/SSL).
3. **Layer 5 - Session Layer:** Establishes, manages, checkpoints, and terminates duplex communication dialogs between applications.
4. **Layer 4 - Transport Layer:** Governs end-to-end data transport, flow control, segment sequencing, and error retransmission (TCP, UDP).
5. **Layer 3 - Network Layer:** Manages logical addressing (IP), path selection across autonomous systems, and packet routing (IPv4, IPv6, ICMP, OSPF, BGP).
6. **Layer 2 - Data Link Layer:** Governs reliable hop-to-hop frame delivery across local physical media, MAC hardware addressing, and error checking (Ethernet, PPP, Switch operations).
7. **Layer 1 - Physical Layer:** Governs the transmission of unstructured raw bitstreams over physical electrical, optical, or RF media (cabling, pinouts, voltages, hubs).

---

## Data Encapsulation & Protocol Data Units (PDUs)

As data descends the stack on the transmitting host, each layer prepends a protocol-specific header (and Layer 2 appends a trailer). This is **Encapsulation**. On the receiving host, headers are stripped off layer by layer in reverse order—a process called **De-Encapsulation**.

![OSI Encapsulation Flow](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/634c04be-ed2c-43a1-99c8-5162bd190d73/osi-ncapsulation-DeEncapsulation-afade08f0.webp)

| OSI Layer | Protocol Data Unit (PDU) | Added Metadata / Identifiers | Hardware Devices |
| :---: | :--- | :--- | :--- |
| **Layer 7, 6, 5** | **Data** / Payload | Application headers, encryption state, MIME types | Workstations, Servers, Layer 7 Firewalls |
| **Layer 4** | **Segment** (TCP) / **Datagram** (UDP) | Source/Destination Ports, Sequence & ACK numbers | Load Balancers, Layer 4 Firewalls |
| **Layer 3** | **Packet** (IP Datagram) | Source/Destination IP Addresses, TTL, Protocol ID | Routers, Layer 3 Switches |
| **Layer 2** | **Frame** | Source/Destination MAC Addresses, 802.1Q tags, FCS Trailer | Layer 2 Switches, Bridges, NICs |
| **Layer 1** | **Bits** | Voltages, light pulses, RF waveforms | Hubs, Repeaters, Physical Transceivers |

---

## The TCP/IP Protocol Suite

The pragmatic four-layer TCP/IP model forms the actual operational foundation of the internet:

```text
OSI 7-Layer Model              TCP/IP 4-Layer Suite
┌────────────────────────┐     ┌────────────────────────┐
│ 7. Application         │ ──┐
│ 6. Presentation        │ ──┼─ Application Layer (HTTP, SSH, DNS)
│ 5. Session             │ ──┘
├────────────────────────┤     ├────────────────────────┤
│ 4. Transport           │ ──── Transport Layer (TCP, UDP)
├────────────────────────┤     ├────────────────────────┤
│ 3. Network             │ ──── Internet Layer (IPv4, IPv6, ICMP, ARP)
├────────────────────────┤     ├────────────────────────┤
│ 2. Data Link           │ ──┐
│ 1. Physical            │ ──┴─ Network Access Layer (Ethernet, Wi-Fi)
└────────────────────────┘     └────────────────────────┘
```

![TCP Data Flow](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/tcp-data-flow-76501ca5b.webp)

### 1. Transport Layer Protocols

#### Transmission Control Protocol (TCP)
TCP provides a connection-oriented, reliable byte-stream service with error recovery, flow control, and ordered packet delivery.

![TCP Segment](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/tcpsegment-b8f04ce25.webp)

- **TCP Header Structure:** Minimum 20 bytes (up to 60 bytes with optional fields).
  - *Source Port (16 bits) / Destination Port (16 bits):* Identifies originating and listening application processes.
  - *Sequence Number (32 bits):* Tracks the position of the byte stream, enabling reassembly in proper order.
  - *Acknowledgment Number (32 bits):* Indicates the next sequence number expected from the sender.
  - *Data Offset / Header Length (4 bits):* Specifies header size in 32-bit words.
  - *Control Flags (9 bits):* Includes `SYN` (synchronize), `ACK` (acknowledge), `FIN` (graceful termination), `RST` (abrupt reset), `PSH` (push data to application), and `URG` (urgent pointer).
  - *Window Size (16 bits):* Implements flow control, specifying the receive buffer capacity.
  - *Checksum (16 bits):* Validates segment integrity across header and data.

#### The TCP Three-Way Handshake
Before data transmission begins, TCP establishes synchronization using a three-step handshake:

![TCP Handshake](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/tcp-three-way-handshake-ef54efadc.webp)

1. **SYN (Step 1):** The client transmits a packet with the `SYN` flag set and an Initial Sequence Number (`ISN_C`).  
   *State: Client enters SYN-SENT.*
2. **SYN-ACK (Step 2):** The server returns a segment with both `SYN` and `ACK` flags set, acknowledging the client (`ACK = ISN_C + 1`) and providing its own sequence number (`ISN_S`).  
   *State: Server enters SYN-RECEIVED.*
3. **ACK (Step 3):** The client replies with an `ACK` flag (`ACK = ISN_S + 1`).  
   *State: Both endpoints enter ESTABLISHED.*

#### User Datagram Protocol (UDP)
UDP is a lightweight, connectionless Transport Layer protocol that transmits datagrams without establishing prior sessions, guaranteeing delivery, or enforcing packet ordering.

![UDP Segment](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/udpsegment-1b32f5f43.webp)

| Header Field | Size | Purpose |
| :--- | :---: | :--- |
| **Source Port** | 16 bits | Originating application port. |
| **Destination Port** | 16 bits | Receiving application port. |
| **Length** | 16 bits | Total length of the UDP segment (header + payload) in bytes. |
| **Checksum** | 16 bits | Optional error detection for header and data. |

---

### 2. Internet Layer Protocols

![IP Layer](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/iplayer-e6604dcf4.webp)

The Internet Layer routes packets across network boundaries using logical addressing:

![IP Encapsulation](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/ipencap-fda5fc6c1.webp)

#### IPv4 Header Architecture

![IP Header Segment](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/ipsegment-511c069cc.webp)

| Field | Size | Description |
| :--- | :---: | :--- |
| **Version** | 4 bits | IP version (IPv4 = `4`). |
| **IHL (Header Length)** | 4 bits | Length of the IP header in 32-bit words (minimum value `5` = 20 bytes). |
| **TOS / DSCP & ECN** | 8 bits | Quality of Service (QoS) classification and explicit congestion notification. |
| **Total Length** | 16 bits | Entire datagram length (header + payload) in bytes. |
| **Identification** | 16 bits | Unique identifier linking fragments belonging to the original datagram. |
| **Flags** | 3 bits | Control bits: Reserved (must be 0), `DF` (Don't Fragment), `MF` (More Fragments). |
| **Fragment Offset** | 13 bits | Identifies where the fragment belongs relative to the start of the unfragmented payload (in 8-byte units). |
| **TTL (Time to Live)** | 8 bits | Hop counter decremented by each intermediate router. Discards packet at `0` to prevent infinite routing loops. |
| **Protocol** | 8 bits | Identifies encapsulated payload protocol (`1` = ICMP, `6` = TCP, `17` = UDP). |
| **Header Checksum** | 16 bits | Error verification computed over the IP header fields only. |
| **Source Address** | 32 bits | Originating host IPv4 address. |
| **Destination Address** | 32 bits | Target destination IPv4 address. |

#### Internet Control Message Protocol (ICMP)
ICMP is an auxiliary network diagnostic and error-reporting protocol encapsulated directly within IP datagrams (`Protocol 1`):
- **Echo Request (Type 8, Code 0) & Echo Reply (Type 0, Code 0):** Leveraged by the `ping` utility to measure end-to-end network latency and packet loss.
- **Time Exceeded (Type 11, Code 0):** Generated by an intermediate router when an IP packet's TTL field decrements to zero, forming the operational basis of `traceroute`.

```bash
ping -c 4 google.com
```

![Ping Analysis](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/ping-be9de99d9.webp)

*Output Interpretation:*
- `bytes`: Size of received response payload.
- `icmp_seq`: Incremental sequence number verifying packet order.
- `ttl`: Remaining time-to-live hops.
- `time`: Round-trip latency in milliseconds.
- `packet loss`: Measures transport reliability.

#### Address Resolution Protocol (ARP)
Bridges Layer 3 logical IP addresses to Layer 2 physical MAC addresses within a local broadcast domain:
- **ARP Request:** A host needing to transmit to an IP transmits a Layer 2 broadcast (`FF:FF:FF:FF:FF:FF`) asking: *"Who has 192.168.1.1? Tell 192.168.1.50"*.
- **ARP Reply:** The host owning that IP unicasts an ARP reply containing its hardware MAC address.
- **ARP Table (`arp -a`):** Caches IP-to-MAC resolution entries to accelerate future transmissions.

```bash
arp -a
```

![ARP Command Output](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/e46b2dee-f6fa-44de-86f2-a1e0c105d4e2/arp-a-f29ff8b02.webp)

---

## Standard Network Protocols & Assigned Port Numbers

Transport protocols map incoming packets to target application processes using 16-bit port numbers ($0$ through $65,535$):
- **Well-Known Ports ($0 - 1023$):** Assigned by IANA to standard infrastructure services.
- **Registered Ports ($1024 - 49,151$):** Assigned to vendor-specific software applications.
- **Dynamic / Ephemeral Ports ($49,152 - 65,535$):** Allocated dynamically by client operating systems for temporary outbound connections.

| Protocol / Service | Default Port | Transport | Functional Role & Security Context |
| :--- | :---: | :---: | :--- |
| **FTP Data / Control** | `20`, `21` | TCP | Unencrypted file transfer; transmits credentials in cleartext. |
| **SSH (Secure Shell)** | `22` | TCP | Cryptographic terminal access and encrypted tunneling. |
| **SFTP** | `22` | TCP | Secure File Transfer Protocol running over SSH. |
| **Telnet** | `23` | TCP | Legacy unencrypted command-line management. |
| **SMTP** | `25` | TCP | Mail transfer agent relay; cleartext without STARTTLS. |
| **DNS** | `53` | UDP/TCP | Domain resolution (UDP for queries < 512 bytes, TCP for zone transfers). |
| **DHCP Server / Client** | `67`, `68` | UDP | Dynamic host IP configuration and lease assignment. |
| **TFTP** | `69` | UDP | Trivial File Transfer Protocol; unauthenticated UDP file transfer. |
| **HTTP** | `80` | TCP | World Wide Web cleartext hypertext transport. |
| **POP3** | `110` | TCP | Post Office Protocol v3 for mailbox retrieval. |
| **NTP** | `123` | UDP | Network Time Protocol for clock synchronization. |
| **IMAP4** | `143` | TCP | Modern mailbox retrieval with server-side synchronization. |
| **SNMP** | `161` | UDP | Simple Network Management Protocol queries and polling. |
| **LDAP** | `389` | TCP | Directory services authentication (Active Directory). |
| **HTTPS** | `443` | TCP | HTTP over TLS/SSL cryptographic tunnel. |
| **SMB** | `445` | TCP | Server Message Block file/printer sharing in Windows networks. |
| **LDAPS** | `636` | TCP | LDAP encrypted over SSL/TLS. |
| **H.323** | `1720` | TCP | ITU audiovisual communication call signaling. |
| **RDP** | `3389` | TCP | Microsoft Remote Desktop Protocol graphical management. |
| **SIP** | `5060`, `5061` | TCP/UDP | Session Initiation Protocol for VoIP signaling. |

---

## Physical vs. Logical Addressing

```text
Layer 2 Physical Address:  00:21:70:6F:06:F2   (48 bits / 6 octets)
                           └────┬────┘ └────┬────┘
                               OUI        NIC Specific

Layer 3 Logical Address:   192.168.1.10        (32 bits / 4 octets)
                           └────┬─────┘ └──┬──┘
                              Network    Host
```

### Media Access Control (MAC) Address
- A 48-bit (6-byte) hexadecimal identifier assigned to the hardware NIC.
- **Organizationally Unique Identifier (OUI):** The first 24 bits (3 bytes) designate the registered vendor/manufacturer assigned by IEEE.
- **Device Identifier:** The final 24 bits are assigned uniquely by the vendor.
- **Broadcast MAC:** `FF:FF:FF:FF:FF:FF` targets all hosts on the local Layer 2 segment.

### Internet Protocol (IPv4) Address
A 32-bit logical address written in dotted-decimal notation consisting of four 8-bit octets:

| Representation | 1st Octet | 2nd Octet | 3rd Octet | 4th Octet |
| :--- | :---: | :---: | :---: | :---: |
| **Decimal** | `192` | `168` | `1` | `10` |
| **Binary** | `11000000` | `10101000` | `00000001` | `00001010` |

Binary conversion operates across powers of 2 ($2^7$ down to $2^0$):
```text
Bit Position:  2^7   2^6   2^5   2^4   2^3   2^2   2^1   2^0
Bit Value:     128    64    32    16     8     4     2     1
Octet 192:       1     1     0     0     0     0     0     0  --> 128 + 64 = 192
```

### Legacy IPv4 Address Classes

| Class | Network Bits | Host Bits | Leading Bits | Address Range | Default Mask |
| :---: | :---: | :---: | :---: | :--- | :--- |
| **A** | 8 | 24 | `0...` | `1.0.0.0` to `126.255.255.255` | `255.0.0.0` (`/8`) |
| **B** | 16 | 16 | `10..` | `128.0.0.0` to `191.255.255.255` | `255.255.0.0` (`/16`) |
| **C** | 24 | 8 | `110.` | `192.0.0.0` to `223.255.255.255` | `255.255.255.0` (`/24`) |
| **D** | N/A | N/A | `1110` | `224.0.0.0` to `239.255.255.255` | Reserved for Multicast |
| **E** | N/A | N/A | `1111` | `240.0.0.0` to `255.255.255.254` | Reserved for Experimental |

### Public vs. Private (RFC 1918) IP Space

To mitigate IPv4 address exhaustion, RFC 1918 reserved three private address blocks for internal networks, non-routable across the public internet:

| Address Scope | Start Address | End Address | CIDR Prefix | Total Capacity |
| :--- | :--- | :--- | :---: | :--- |
| **Class A Private** | `10.0.0.0` | `10.255.255.255` | `10.0.0.0/8` | 16,777,216 addresses |
| **Class B Private** | `172.16.0.0` | `172.31.255.255` | `172.16.0.0/12` | 1,048,576 addresses |
| **Class C Private** | `192.168.0.0` | `192.168.255.255` | `192.168.0.0/16` | 65,536 addresses |
| **APIPA (Link-Local)**| `169.254.0.0` | `169.254.255.255` | `169.254.0.0/16` | Auto-assigned when DHCP fails |
| **Loopback** | `127.0.0.1` | `127.255.255.255` | `127.0.0.0/8` | Internal host loopback testing |

---

## IP Subnetting: FLSM and VLSM

Subnetting divides a contiguous IP address block into smaller, logically isolated broadcast sub-networks by borrowing host bits for the network identifier.

### Subnet Sizing Formulas

- **Total Subnets Created:** $2^s$ (where $s$ is the number of borrowed bits).
- **Usable Hosts Per Subnet:** $2^h - 2$ (where $h$ is the remaining host bits).
  *(Two addresses are reserved: the network base address and the broadcast address).*

### 1. Fixed-Length Subnet Masking (FLSM)

In FLSM, every resulting subnet shares the exact same subnet mask and capacity.

**Example: Partitioning `192.168.1.0/24` into 4 Equal Subnets**
- Subnets needed: $4 \rightarrow 2^2 = 4$ (borrow 2 host bits).
- New Prefix: $/24 + 2 = /26$ (Mask: `255.255.255.192`).
- Remaining Host Bits: $8 - 2 = 6$ bits $\rightarrow 2^6 - 2 = 62$ usable hosts per subnet.
- Block Size: $256 - 192 = 64$.

| Subnet | Network Address | Usable Host Range | Broadcast Address |
| :---: | :--- | :--- | :--- |
| **1** | `192.168.1.0/26` | `192.168.1.1` – `192.168.1.62` | `192.168.1.63` |
| **2** | `192.168.1.64/26` | `192.168.1.65` – `192.168.1.126` | `192.168.1.127` |
| **3** | `192.168.1.128/26` | `192.168.1.129` – `192.168.1.190` | `192.168.1.191` |
| **4** | `192.168.1.192/26` | `192.168.1.193` – `192.168.1.254` | `192.168.1.255` |

---

### 2. Variable-Length Subnet Masking (VLSM)

VLSM allocates custom subnet masks of varying lengths matching exact host counts, eliminating address waste.

**Scenario: Allocating `192.168.1.0/24` across four departmental requirements:**
1. *Subnet 1:* 50 hosts needed
2. *Subnet 2:* 30 hosts needed
3. *Subnet 3:* 10 hosts needed
4. *Subnet 4:* 2 hosts needed (Point-to-Point WAN link)

#### Step 1: Compute Required Host Bits (Order Largest to Smallest)
- **Subnet 1 (50 hosts):** Requires $h=6$ bits ($2^6 - 2 = 62$ hosts). Prefix: $32 - 6 = \mathbf{/26}$ (`255.255.255.192`).
- **Subnet 2 (30 hosts):** Requires $h=5$ bits ($2^5 - 2 = 30$ hosts). Prefix: $32 - 5 = \mathbf{/27}$ (`255.255.255.224`).
- **Subnet 3 (10 hosts):** Requires $h=4$ bits ($2^4 - 2 = 14$ hosts). Prefix: $32 - 4 = \mathbf{/28}$ (`255.255.255.240`).
- **Subnet 4 (2 hosts):** Requires $h=2$ bits ($2^2 - 2 = 2$ hosts). Prefix: $32 - 2 = \mathbf{/30}$ (`255.255.255.252`).

#### Step 2: Sequential VLSM Address Allocation

| Subnet | Target Size | Subnet Mask | Network Address | Usable Host Range | Broadcast Address |
| :--- | :---: | :---: | :--- | :--- | :--- |
| **Subnet 1** | 50 hosts | `/26` (`.192`) | `192.168.1.0/26` | `192.168.1.1` – `192.168.1.62` | `192.168.1.63` |
| **Subnet 2** | 30 hosts | `/27` (`.224`) | `192.168.1.64/27` | `192.168.1.65` – `192.168.1.94` | `192.168.1.95` |
| **Subnet 3** | 10 hosts | `/28` (`.240`) | `192.168.1.96/28` | `192.168.1.97` – `192.168.1.110` | `192.168.1.111` |
| **Subnet 4** | 2 hosts | `/30` (`.252`) | `192.168.1.112/30` | `192.168.1.113` – `192.168.1.114` | `192.168.1.115` |
