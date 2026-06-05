
# OSI Model (Open Systems Interconnection)

The OSI Model is a framework that explains how data travels across a network. It divides communication into **7 layers**, where each layer has a specific responsibility.

## Why Do We Need OSI?

- Standardizes network communication
    
- Makes troubleshooting easier
    
- Allows different systems to communicate
    
- Separates networking tasks into manageable layers
    

---

# OSI Layers Overview

|Layer|Name|Data Unit (PDU)|Main Function|
|---|---|---|---|
|7|Application|Data|User services|
|6|Presentation|Data|Translation, Encryption, Compression|
|5|Session|Data|Session management|
|4|Transport|Segment|End-to-end delivery|
|3|Network|Packet|Routing & IP addressing|
|2|Data Link|Frame|MAC addressing & error detection|
|1|Physical|Bit|Physical transmission|

### Easy Memory Trick

**A**ll  
**P**eople  
**S**eem  
**T**o  
**N**eed  
**D**ata  
**P**rocessing

(Application → Physical)

---

# Layer 1: Physical Layer

### Purpose

Transfers raw bits through cables or wireless media.

### Responsibilities

- Bit transmission
    
- Bit synchronization
    
- Bit rate control
    
- Physical topology management
    

### Devices

- Hub
    
- Repeater
    
- Modem
    
- Cables
    

### PDU

```text
Bit
```

### Exam Favorites

- Lowest OSI layer
    
- Deals with bits
    
- Handles physical transmission
    

---

# Layer 2: Data Link Layer

### Purpose

Provides reliable communication between directly connected devices.

### Responsibilities

- Framing
    
- MAC addressing
    
- Error detection
    
- Flow control
    

### Devices

- Switch
    
- Bridge
    

### PDU

```text
Frame
```

### Important Concept

Uses:

```text
MAC Address
```

instead of IP Address.

### Exam Favorites

- Uses MAC addresses
    
- Frame creation
    
- Error detection
    

---

# Layer 3: Network Layer

### Purpose

Finds the best path between source and destination.

### Responsibilities

- Routing
    
- Logical addressing
    
- Packet forwarding
    

### Devices

- Router
    

### PDU

```text
Packet
```

### Important Concept

Uses:

```text
IP Address
```

### Exam Favorites

- Routing
    
- IP Addressing
    
- Router
    

---

# Layer 4: Transport Layer

### Purpose

Provides end-to-end communication.

### Responsibilities

- Segmentation
    
- Reassembly
    
- Error recovery
    
- Reliability
    

### PDU

```text
Segment
```

### Uses Port Numbers

Examples:

```text
HTTP  → Port 80
HTTPS → Port 443
```

---

## TCP vs UDP

### TCP

- Reliable
    
- Connection-oriented
    
- Uses acknowledgements
    
- Slower
    

Examples:

- Email
    
- File Transfer
    
- Web browsing
    

### UDP

- Faster
    
- Connectionless
    
- No delivery guarantee
    

Examples:

- Gaming
    
- VoIP
    
- Live Streaming
    

### Exam Favorite

**Which protocol is preferred for live video streaming?**

✅ UDP

---

# Layer 5: Session Layer

### Purpose

Manages communication sessions between devices.

### Responsibilities

- Session establishment
    
- Session maintenance
    
- Session termination
    
- Synchronization checkpoints
    

### Example

A WhatsApp or Messenger conversation remains active because of session management.

### Exam Favorite

- Opens and closes communication sessions
    

---

# Layer 6: Presentation Layer

### Purpose

Makes data understandable between different systems.

### Responsibilities

### Translation

Converts data into a standard format.

### Encryption / Decryption

Provides security.

### Compression

Reduces data size.

### Examples

- JPEG
    
- GIF
    
- MPEG
    
- SSL/TLS
    

### Exam Favorite

**Which OSI layer handles encryption?**

✅ Presentation Layer

---

# Layer 7: Application Layer

### Purpose

Provides network services directly to users and applications.

### Common Protocols

|Protocol|Function|
|---|---|
|HTTP|Web Browsing|
|HTTPS|Secure Web|
|FTP|File Transfer|
|SMTP|Email Sending|
|DNS|Name Resolution|

### Exam Favorites

**DNS does what?**

✅ Converts domain names into IP addresses

Example:

```text
google.com → 142.x.x.x
```

---

# Data Flow Through OSI

When sending data:

```text
Application
↓
Presentation
↓
Session
↓
Transport
↓
Network
↓
Data Link
↓
Physical
```

When receiving data:

```text
Physical
↑
Data Link
↑
Network
↑
Transport
↑
Session
↑
Presentation
↑
Application
```

---

# Quick Revision Table

|Layer|Keyword|
|---|---|
|Application|User Services|
|Presentation|Encryption|
|Session|Sessions|
|Transport|TCP / UDP|
|Network|Routing|
|Data Link|MAC Address|
|Physical|Bits|



# OSI Model (Open Systems Interconnection)Exit Exam Practice Questions

## OSI Model Questions

### Q1. How many layers are there in the OSI Model?

**Answer:** 7

---

### Q2. Which layer is responsible for routing packets between networks?

**Answer:** Network Layer

---

### Q3. Which layer uses MAC addresses?

**Answer:** Data Link Layer

---

### Q4. Which layer uses IP addresses?

**Answer:** Network Layer

---

### Q5. Which layer handles encryption and decryption?

**Answer:** Presentation Layer

---

### Q6. Which layer manages communication sessions?

**Answer:** Session Layer

---

### Q7. Which layer is responsible for end-to-end communication?

**Answer:** Transport Layer

---

### Q8. Which layer breaks data into segments?

**Answer:** Transport Layer

---

### Q9. Which layer converts frames into bits?

**Answer:** Physical Layer

---

### Q10. Which layer is closest to the user?

**Answer:** Application Layer

---

## TCP vs UDP Questions

### Q11. Which protocol is connection-oriented?

**Answer:** TCP

---

### Q12. Which protocol is faster but less reliable?

**Answer:** UDP

---

### Q13. Which protocol is best for live video streaming?

**Answer:** UDP

---

### Q14. Which protocol guarantees delivery of data?

**Answer:** TCP

---

### Q15. Which protocol performs a handshake before communication?

**Answer:** TCP

---

## Networking Protocol Questions

### Q16. Which protocol translates domain names into IP addresses?

**Answer:** DNS

---

### Q17. Which protocol is mainly used for web browsing?

**Answer:** HTTP

---

### Q18. Which protocol provides secure web communication?

**Answer:** HTTPS

---

### Q19. Which protocol is commonly used for file transfer?

**Answer:** FTP

---

### Q20. Which protocol is used to send emails?

**Answer:** SMTP

---

## IP Addressing Questions

### Q21. What class is the IP address 193.131.27.255?

**Answer:** Class C

---

### Q22. Which IP class uses the first two octets for network identification?

**Answer:** Class B

---

### Q23. Which IP class supports the largest number of hosts?

**Answer:** Class A

---

## Network Devices Questions

### Q24. Which device operates at the Data Link Layer?

**Answer:** Switch

---

### Q25. Which device operates at the Network Layer?

**Answer:** Router

---

### Q26. Which device simply repeats signals?

**Answer:** Repeater

---

### Q27. Which device broadcasts traffic to all ports?

**Answer:** Hub

---

## Troubleshooting Questions

### Q28. Which command is commonly used to test network connectivity?

**Answer:** Ping

---

### Q29. What does DNS stand for?

**Answer:** Domain Name System

---

### Q30. What does HTTP stand for?

**Answer:** HyperText Transfer Protocol

---

# Quick Exit Exam Facts

|Concept|Remember|
|---|---|
|Bits|Physical Layer|
|Frames|Data Link Layer|
|Packets|Network Layer|
|Segments|Transport Layer|
|MAC Address|Data Link Layer|
|IP Address|Network Layer|
|Router|Layer 3|
|Switch|Layer 2|
|TCP|Reliable|
|UDP|Fast|

---

# Additional Resources

For a more detailed explanation of the OSI Model and its layers:

👉 [Click Here](https://www.geeksforgeeks.org/computer-networks/open-systems-interconnection-model-osi/)

