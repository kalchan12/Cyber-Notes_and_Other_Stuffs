---
title: Cryptology Fundamentals & Applied Cryptography
category: Cryptology Fundamentals
tags:
  - cryptology
  - cryptography
  - cryptanalysis
  - symmetric-encryption
  - asymmetric-encryption
  - hashing
  - pki
---

# Cryptology Fundamentals & Applied Cryptography

## Overview & Foundational Principles

**Cryptology** (derived from the Greek *kryptós*, "hidden", and *lógos*, "study") is the mathematical and scientific discipline dedicated to securing information against unauthorized access, modification, and repudiation. Cryptology bifurcates into two complementary, interdependent sub-disciplines:

```text
┌────────────────────────────────────────────────────────┐
│                       Cryptology                       │
├───────────────────────────┬────────────────────────────┤
│       Cryptography        │       Cryptanalysis        │
│ (Constructing protections │  (Evaluating & breaking    │
│    and secure systems)    │    cryptosystem defenses)  │
└───────────────────────────┴────────────────────────────┘
```

- **Cryptography:** The design and implementation of mathematical algorithms, ciphers, and protocols to protect data confidentiality, integrity, authenticity, and non-repudiation.
- **Cryptanalysis:** The analytical and empirical study of ciphers and cryptosystems to discover weaknesses, reverse mathematical protections, and recover plaintexts or keys without authorized authorization.

### Historical Evolution

- **Ancient Era:** Hieroglyphic substitutions in Ancient Egypt; the **Caesar Cipher** (monoalphabetic shift cipher) employed by Julius Caesar across Roman military communications.
- **Renaissance to Early Modern Era:** Polyalphabetic substitutions such as the **Vigenère Cipher**, which defeated simple frequency analysis for centuries until Charles Babbage and Friedrich Kasiski developed statistical cryptanalysis methods.
- **World War II & Electro-Mechanical Ciphers:** Rotor machines such as the German **Enigma** and the Japanese Purple machine. The breakthrough work of **Alan Turing**, Marian Rejewski, and the codebreakers at Bletchley Park laid the foundations of modern theoretical computer science and automated cryptanalysis.
- **Modern Digital Era:** Standardized symmetric block ciphers (DES, AES), the birth of asymmetric public-key cryptography (Diffie-Hellman, RSA) in the 1970s, and modern elliptic-curve standards (ECC, Ed25519) securing global internet commerce and data-at-rest.

### Foundational Cryptographic Objectives

Modern cryptosystems are engineered to satisfy four primary security guarantees:

1. **Confidentiality:** Ensuring information is inaccessible and unreadable to eavesdroppers, interceptors, or unauthorized entities.
2. **Integrity:** Guaranteeing that data has not been altered, deleted, inserted, or tampered with in transit or storage.
3. **Authentication:** Verifying the genuine identity of the sender, device, or service generating a message.
4. **Non-Repudiation:** Preventing an originating party from denying the authenticity or transmission of a signed transaction.

### Core Cryptographic Terminology

- **Plaintext ($P$ or $M$):** The raw, unencrypted message or data stream.
- **Ciphertext ($C$):** The transformed, unreadable output produced by applying an encryption algorithm.
- **Cipher / Algorithm ($E, D$):** The mathematical function governing encryption ($C = E_K(P)$) and decryption ($P = D_K(C)$).
- **Key ($K$):** The secret mathematical parameter (string of bits) supplied to the algorithm to control the transformation.
- **Key Space:** The total number of unique keys possible within a cipher (e.g., an $n$-bit key possesses a key space of $2^n$).
- **Key Management Lifecycle:** The operational protocols governing key generation, secure distribution, storage, rotation, revocation, and cryptographic destruction.

### Core Theoretical Principles

#### 1. Kerckhoffs's Principle (1883)
> *"A cryptosystem should be secure even if everything about the system, except the key, is public knowledge."*

This is the antithesis of **"Security through Obscurity."** Proprietary, secret algorithms invariably fail under scrutiny. Modern ciphers (such as AES) publish full mathematical specifications, relying entirely on the computational infeasibility of key recovery.

#### 2. Shannon's Principles of Confusion and Diffusion (1949)
Claude Shannon defined the two core operations required to frustrate statistical cryptanalysis:
- **Confusion:** Obscuring the relationship between the secret key and the ciphertext (typically accomplished via non-linear substitution boxes / S-Boxes).
- **Diffusion:** Spreading the statistical influence of a single plaintext bit across many ciphertext bits (typically achieved via transposition / P-Boxes), ensuring that changing one bit in the plaintext flips approximately 50% of the ciphertext bits (**Avalanche Effect**).

---

## Symmetric Encryption

In **symmetric cryptography** (also known as secret-key or shared-key encryption), both communicating endpoints share the exact same secret key for both encryption and decryption.

![Symmetric Encryption Workflow](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/44f9fa37-a404-4249-a329-7ca1d76344b6/image-a76705e52.webp)

$$\text{Ciphertext } C = E_K(P) \quad \Longleftrightarrow \quad \text{Plaintext } P = D_K(C)$$

### Architectural Classifications: Block vs. Stream Ciphers

| Characteristic | Block Ciphers | Stream Ciphers |
| :--- | :--- | :--- |
| **Operational Unit** | Encrypts fixed-size chunks of data (e.g., 64-bit or 128-bit blocks). | Encrypts continuous data streams bit-by-bit or byte-by-byte. |
| **Padding** | Requires padding (e.g., PKCS#7) if plaintext does not align with block boundaries. | No padding required; ciphertext matches plaintext length exactly. |
| **Throughput & Latency** | Optimized for structured data-at-rest (databases, disk encryption, files). | Optimized for continuous, real-time data streams (VoIP, video, sensor telemetry). |
| **Representative Ciphers** | AES, DES, 3DES, Blowfish, Twofish | ChaCha20, Salsa20, RC4 |

---

### Major Block Cipher Algorithms

#### 1. DES (Data Encryption Standard)
- Standardized by the US National Bureau of Standards (NBS, now NIST) in 1977 based on IBM's Lucifer cipher.
- Uses a 64-bit block size with a Feistel network architecture.
- **Effective Key Length:** 56 bits (8 bits reserved for parity).
- **Security Status:** **Broken & Insecure.** In 1999, the EFF's "Deep Crack" custom hardware cracked a DES key in under 23 hours via brute force ($2^{56} \approx 7.2 \times 10^{16}$ operations).

#### 2. 3DES (Triple DES)
- Extends the lifespan of DES by applying DES three times in an Encrypt-Decrypt-Encrypt sequence:
  $$C = E_{K3}(D_{K2}(E_{K1}(P)))$$
- Key length: 112 bits (2-key 3DES) or 168 bits (3-key 3DES).
- **Security Status:** Deprecated due to vulnerability to the Sweet32 collision attack against 64-bit block sizes and slow hardware performance.

#### 3. AES (Advanced Encryption Standard / Rijndael)
- Standardized by NIST in 2001 (FIPS 197) to replace DES following an open multi-year international competition won by Belgian cryptographers Vincent Rijmen and Joan Daemen.
- **Block Size:** Fixed at 128 bits.
- **Key Lengths & Iteration Rounds:**
  - AES-128: 10 rounds of substitution-permutation processing.
  - AES-192: 12 rounds.
  - AES-256: 14 rounds.
- **Operations:** SubBytes (non-linear S-Box), ShiftRows, MixColumns, AddRoundKey.
- **Security Status:** The global gold standard for enterprise symmetric encryption, hardware-accelerated on modern CPUs via the AES-NI instruction set.

---

### Major Stream Cipher Algorithms

- **RC4 (Rivest Cipher 4):** Variable key length byte-oriented stream cipher designed by Ron Rivest in 1987. Formerly standard in SSL 3.0, TLS 1.0, and WEP Wi-Fi.  
  *Status:* **Completely Broken.** Cryptographic biases in the initial keystream bytes allow plaintext recovery; formally prohibited across modern TLS (RFC 7465).
- **Salsa20 & ChaCha20:** High-speed modern stream ciphers developed by Daniel J. Bernstein. ChaCha20 uses a 256-bit key and operates on 32-bit words, providing extreme throughput and resilience against cache-timing side-channel attacks. Frequently paired with the **Poly1305** authenticator (ChaCha20-Poly1305) in TLS 1.3 and WireGuard VPNs.

---

### Block Cipher Modes of Operation

Because block ciphers encrypt fixed chunks (e.g., 128 bits), encrypting arbitrary data requires a **Mode of Operation**:

#### 1. Electronic Codebook (ECB) Mode
- Each block is encrypted independently using the exact same key: $C_i = E_K(P_i)$.
- > [!CAUTION] Critical Vulnerability: Pattern Leakage
  > Identical plaintext blocks produce identical ciphertext blocks! This preserves underlying data structures (famously demonstrated by the ECB-encrypted Tux penguin image). **Never use ECB in production.**

#### 2. Cipher Block Chaining (CBC) Mode
- Each plaintext block is XORed with the preceding ciphertext block prior to encryption:
  $$C_i = E_K(P_i \oplus C_{i-1})$$
- Requires an unpredictable, random **Initialization Vector (IV)** for the first block: $C_1 = E_K(P_1 \oplus \text{IV})$.
- Eliminates patterns, but decryption errors propagate across adjacent blocks, and unauthenticated CBC is vulnerable to **Padding Oracle Attacks**.

#### 3. Counter (CTR) Mode
- Turns a block cipher into a stream cipher. A running counter concatenated with a nonce is encrypted, and the output is XORed with the plaintext:
  $$C_i = P_i \oplus E_K(\text{Nonce} \parallel \text{Counter}_i)$$
- **High Performance:** Individual blocks do not depend on preceding ciphertexts, allowing massive parallel processing in multi-core hardware and random access seeking.

#### 4. Cipher Feedback (CFB) & Output Feedback (OFB) Modes
- Feedback modes transforming block ciphers into self-synchronizing or synchronous stream ciphers.

---

## Asymmetric Encryption (Public-Key Cryptography)

Asymmetric cryptography solves the fundamental symmetric key distribution problem by employing mathematically linked **key pairs**:
- **Public Key ($K_{\text{pub}}$):** Published openly; used by anyone to encrypt messages or verify signatures.
- **Private Key ($K_{\text{priv}}$):** Guarded confidentially by the owner; used to decrypt received ciphertexts or generate digital signatures.

![Asymmetric Encryption Workflow](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/461ed9b5-fe53-43a0-a900-e38eda265be4/image-1-833b0f46d.webp)

$$\text{Confidentiality: } C = E_{K_{\text{pub}}}(P) \quad \Longrightarrow \quad P = D_{K_{\text{priv}}}(C)$$

### Major Asymmetric Algorithms

#### 1. RSA (Rivest–Shamir–Adleman)
- Published in 1977; supports both data encryption and digital signatures.
- **Mathematical Trapdoor:** The computational difficulty of the **Prime Factorization Problem**. Multiplying two large prime numbers ($p \times q = n$) is computationally trivial, but deriving $p$ and $q$ given only their product $n$ is computationally infeasible for large integers.
- **Key Sizes:** 2048-bit (minimum modern baseline) and 4096-bit (high-security archival). Keys below 2048 bits are considered cryptographically obsolete.

#### 2. ECC (Elliptic Curve Cryptography)
- Built on the algebraic structure of elliptic curves over finite fields ($y^2 = x^3 + ax + b$).
- **Mathematical Trapdoor:** The **Elliptic Curve Discrete Logarithm Problem (ECDLP)**.
- **Efficiency & Security Advantage:** Provides cryptographic equivalence to RSA with drastically smaller keys and lower memory/power consumption:
  - **ECC 256-bit** $\approx$ **RSA 2048-bit** security level.
  - **ECC 384-bit** $\approx$ **RSA 7680-bit** security level.
- Standard curves include NIST P-256, P-384, and Bernstein's Curve25519 (X25519).

#### 3. ElGamal & DSA
- **ElGamal (1985):** Asymmetric cipher and signature algorithm based on the Diffie-Hellman Discrete Logarithm problem.
- **DSA (Digital Signature Algorithm):** Standardized by NIST in 1991 exclusively for digital signing (not encryption).

---

### Symmetric vs. Asymmetric Cryptography

| Dimension | Symmetric Cryptography | Asymmetric Cryptography |
| :--- | :--- | :--- |
| **Key Usage** | One shared secret key for encryption & decryption. | Mathematically paired Public Key (encrypt) and Private Key (decrypt). |
| **Computational Speed** | Extremely fast; minimal CPU overhead (gigabits/sec). | Computationally intensive; slow (orders of magnitude slower than AES). |
| **Key Distribution** | Difficult; requires an out-of-band secure channel to exchange keys. | Simple; public keys can be distributed over unencrypted public channels. |
| **Key Scalability** | Poor; $N$ users communicating requires $N(N-1)/2$ secret keys. | Excellent; $N$ users require only $2N$ total keys. |
| **Standard Use Cases** | Bulk disk encryption, file archiving, transport payloads. | Key exchange handshakes (TLS), identity certificates, digital signing. |

> [!TIP] The Hybrid Cryptosystem Pattern
> Real-world protocols (TLS, SSH, PGP) combine both paradigms:
> 1. Asymmetric cryptography (RSA or ECDH) performs mutual authentication and securely negotiates a shared ephemeral **session key**.
> 2. High-speed symmetric cryptography (AES-GCM or ChaCha20) encrypts the actual bulk data stream using that negotiated session key.

---

## Cryptographic Hash Functions

A **cryptographic hash function** is an irreversible, one-way mathematical algorithm that ingests arbitrary-length data streams and compresses them into a fixed-length bit digest ($h = H(M)$).

### Four Fundamental Security Properties

1. **Deterministic:** Ingesting identical input bits produces the exact same hash output.
2. **Preimage Resistance (One-Way Property):** Computationally infeasible to reverse engineer the original input $M$ given its hash digest $h$ (finding $M$ such that $H(M) = h$).
3. **Second Preimage Resistance (Weak Collision Resistance):** Given input $M_1$, it is computationally infeasible to locate a different input $M_2$ such that $H(M_1) = H(M_2)$.
4. **Collision Resistance (Strong Collision Resistance):** Computationally infeasible to locate *any* two arbitrary, distinct inputs $M_1 \neq M_2$ producing the same digest $H(M_1) = H(M_2)$.
5. **The Avalanche Effect:** Flipping a single bit in the input flips roughly 50% of the resulting digest bits, ensuring outputs appear completely uncorrelated and pseudo-random.

---

### Overview of Common Hash Algorithms

| Algorithm | Digest Size | Security Status | Cryptographic Assessment & History |
| :--- | :---: | :---: | :--- |
| **MD5** | 128 bits | **Broken** | Collisions generated in seconds via commodity laptops; prohibited in security protocols. |
| **SHA-1** | 160 bits | **Broken** | Google demonstrated practical collision ("SHAttered") in 2017; prohibited in certificates. |
| **SHA-2** | 224, 256, 384, 512 bits | **Secure** | NIST standard; widely adopted across TLS, certificates, and blockchain (**SHA-256**). |
| **SHA-3** | 224, 256, 384, 512 bits | **Secure** | Based on the Keccak permutation-based sponge construction; alternative to SHA-2. |
| **RIPEMD-160** | 160 bits | **Specialized** | Developed in academic consortia; heavily utilized in Bitcoin address derivation. |

### Cryptanalytic Attacks on Hash Functions

- **Collision Attacks:** Exploits the **Birthday Paradox** to identify two arbitrary inputs producing identical digests in roughly $\mathcal{O}(2^{n/2})$ operations rather than $\mathcal{O}(2^n)$.
- **Preimage Attacks:** Brute-force inversion attempting to identify a message matching a specific known target digest ($\mathcal{O}(2^n)$ operations).
- **Rainbow Table Attacks:** Precomputing lookup tables of millions of plaintext-to-hash pairs to crack passwords; mitigated by appending unique, cryptographic **Salts** to passwords.

---

## Digital Signatures & Public Key Infrastructure (PKI)

A **digital signature** binds an identity cryptographically to an electronic document, guaranteeing authenticity, message integrity, and non-repudiation.

### The Signing and Verification Workflow

```text
Signing Phase (Sender):
  [Plaintext Document] ───► [Hash Function (SHA-256)] ───► [Message Digest]
                                                                  │
  [Sender's Private Key] ─────────────────────────────────────────┼─► [Asymmetric Encryption]
                                                                  │
                                                                  ▼
                                                      [Digital Signature]

Verification Phase (Receiver):
  [Received Document] ────► [Hash Function (SHA-256)] ───► [Calculated Digest]
                                                                  │
  [Digital Signature] ───► [Decrypt via Sender Public Key] ─► [Decrypted Digest]
                                                                  │
                                                        (Must Match Exactly!)
```

1. **Signature Generation:**
   - The signer runs the document through a secure hash function (e.g., SHA-256) to produce a digest.
   - The signer encrypts the digest with their **Private Key**. This encrypted digest is the digital signature.
2. **Signature Verification:**
   - The recipient re-computes the document digest using the same hash algorithm.
   - The recipient decrypts the digital signature using the signer's **Public Key**.
   - If the decrypted digest matches the calculated digest, the signature is valid. Any tampering with the document invalidates the match.

### Common Digital Signature Algorithms
- **RSA-PSS:** Probabilistic Signature Scheme offering provable security over legacy PKCS#1 v1.5.
- **DSA:** NIST standard based on modular exponentiation.
- **ECDSA & Ed25519:** High-performance signatures over elliptic curves (Ed25519 uses Twisted Edwards curves).

---

### Public Key Infrastructure (PKI) Architecture

PKI provides the organizational and cryptographic framework to verify that a public key belongs to a specific entity:

- **Certificate Authority (CA):** A trusted third party (e.g., Let's Encrypt, DigiCert) that cryptographically signs **Digital Certificates** validating domain ownership.
- **Registration Authority (RA):** An entity that verifies applicant credentials before the CA issues a certificate.
- **X.509 Standard:** The ITU-T standard format for public-key certificates, containing:
  - Subject identity (Domain name, organization).
  - Subject public key.
  - Issuing CA digital signature.
  - Validity date range.
  - Key usage constraints.
- **Certificate Revocation Mechanisms:**
  - *Certificate Revocation Lists (CRL):* Periodically published lists of revoked serial numbers.
  - *OCSP (Online Certificate Status Protocol):* Real-time query protocol checking certificate status.

---

## Major Cryptographic Protocols

Protocols coordinate algorithms to secure communication across untrusted networks:

### 1. SSL / TLS (Transport Layer Security)
- **Primary Function:** Secures web traffic (HTTPS), email transport, and API endpoints.
- **TLS 1.3 Architecture:** Drops obsolete, insecure features (RSA key exchange, CBC ciphers, SHA-1). Mandates **Ephemeral Diffie-Hellman (ECDHE)** for Perfect Forward Secrecy (PFS) and authenticated symmetric ciphers (AES-GCM, ChaCha20-Poly1305).

### 2. IPsec (Internet Protocol Security)
- **Primary Function:** Layer 3 suite establishing Virtual Private Networks (VPNs).
- **Core Sub-Protocols:**
  - *Encapsulating Security Payload (ESP):* Provides confidentiality, origin authentication, and connectionless integrity.
  - *Authentication Header (AH):* Provides integrity and authentication (omits encryption; incompatible with NAT).
  - *Internet Key Exchange (IKEv2):* Manages mutual authentication and dynamic session key negotiation.

### 3. SSH (Secure Shell)
- Provides encrypted terminal management, SFTP file transfer, and port forwarding over port `22`. Uses public-key cryptography for host and user authentication, transitioning to symmetric ciphers for terminal data.

### 4. PGP / GPG (Pretty Good Privacy)
- End-to-end cryptographic standard for email and file encryption. Operates as a hybrid cryptosystem, combining symmetric data encryption, asymmetric key wrapping, and digital signatures within a decentralized "Web of Trust" or centralized keyserver model.

### 5. Kerberos
- Network authentication protocol operating via trusted third-party **Key Distribution Centers (KDC)**. Issues temporary symmetric **Tickets** (Ticket Granting Tickets / TGTs) to users, enabling single sign-on across Microsoft Active Directory domains without transmitting cleartext passwords.

---

## Cryptanalysis Techniques & Attack Taxonomies

Cryptanalysis assesses the mathematical strength of cryptosystems and discovers exploitable flaws:

### 1. Brute-Force & Exhaustive Key Search
Iterating systematically through every combination in the key space until the correct plaintext emerges. Key lengths of 128 bits ($2^{128} \approx 3.4 \times 10^{38}$) are computationally impossible to brute-force with known classical computing architectures.

### 2. Dictionary & Credential Guessing Attacks
Targeting human-generated passwords by testing candidate words and permutations from pre-compiled leak wordlists (e.g., `rockyou.txt`).

### 3. Side-Channel Attacks
Exploits physical anomalies exhibited by the hardware executing cryptographic operations rather than attacking the cipher's mathematics:
- **Timing Attacks:** Measuring minute variations in CPU execution cycles during string comparisons or modular exponentiations.
- **Power Analysis (SPA / DPA):** Monitoring power consumption fluctuations across cryptographic smartcards to deduce private key bits.
- **Electromagnetic Analysis:** Measuring RF emissions radiating from hardware chips during cryptographic processing.

### 4. Known-Plaintext & Chosen-Plaintext Attacks (CPA)
- **Known-Plaintext Attack (KPA):** The analyst possesses pairs of known plaintexts and their corresponding ciphertexts, attempting to deduce the secret key.
- **Chosen-Plaintext Attack (CPA):** The analyst can submit arbitrary plaintexts to the encryption engine and observe the resulting ciphertexts (used to break ECB and unpadded RSA).

### 5. Statistical & Frequency Analysis
Analyzing the distribution of letters, digraphs, and trigraphs in ciphertexts. Effective against classical monoalphabetic and polyalphabetic ciphers where language letter frequencies (such as 'E', 'T', 'A' in English) persist in the output.

### 6. Differential & Linear Cryptanalysis
- **Differential Cryptanalysis:** Ingests pairs of plaintexts with specific differences and analyzes the statistical propagation of those differences through cipher rounds to recover round keys.
- **Linear Cryptanalysis:** Constructs linear algebraic approximations modeling non-linear S-Box transformations. Modern ciphers like AES are mathematically proven resistant to both techniques.

### 7. Man-in-the-Middle (MitM) Attacks
Interception where an adversary positions themselves between two endpoints, establishing separate encrypted sessions with each party. Mitigated by authenticating endpoints using validated X.509 digital certificates and public key pinning.
