# Public Key Infrastructure (PKI)

## Overview
Main challenge of public-key systems is key authentication, as keys need to be distributed via authentic channels.
PKI's provide a way to validate public keys.

CA
: Certification Authority
Public-key certificate
: is signed and binds a name to a public key
Trust anchor
: self-signed certificates of public keys that are allowed to sign other certificates
X.509
: standard format of digital certificate

### X.509
* Two sections: data and signature section
* A CA assigns a unique name to each user and issues a signed certificate
* Often name is the domain or Email address

## Problems of PKI
* If the private key of a root of trust is obtained, the adversary can issue any certificates he wants.

* **Content Delivery Network**:
    * distributed network of servers for hosting websites for domains
    * CDN's only need one single certificate for multiple domains. E.g. Incapsula.com is hosting a banking website and the Chick-fil-a website, so they have the same certificate, lol.
    * **Problem**: one website could impersonate another website on the CDN-Server and access the website

* **Compelled certificates**:
    * public/private key pair for law enforcement (MitM-attacks on TLS) with a CA certificate enabling private key to sign additional certificates
    * Pre-made MitM-Boxes with a compelled certificate exist (for government use)
    * **Problem**: If box is obtained by adversary, it can be hacked and the compelled certificate keys can be used to sign fake certificates.

* **Statistics**:
    * 300 roots of trust (root CA's)
    * 1200 intermediate keys users don't see in the browser, but are still trusted


## Two approaches for trust roots:
* **Monopoly model**: single root of trust
    * E.g. DNSSEC
    * **Problem**: world cannot agree on who controls root of trust
* **Oligarchy model**: numerous roots of trust
    * E.g. SSL/TLS PKI (over 1000 trusted root CA certificates)
    * **Problems**:
        * Weakest-link security: single compromised entity enables MitM-attack
        * Not trusting one trust roots result in unverifiable entities


## Improve TLS

### Let's Encrypt
Goal
: provide free certificates based on automated domain validation, issuance and renewal
* Non-profit, no company behind (sponsored by many companies tho)
* More than 100 million certificates issued until 2017
* short-lived certificates: 90 days
* Based on ACME (Automated Certificate Management Environment). Communications protocol for automating interactions between certificate authorities and their users' web servers, allowing the automated deployment of public key infrastructure at very low cost. (Wikipedia)

### Extended validation entities

Levels of trust:

* **No SSL/TLS**  
* **Domain Validation (DV)**
: Automated system like ACME
* **Organization Validation (OV)**
: Almost indistinguishable from DV
* **Extended Validation (EV)**
: Entities (companies) are being validated, maybe physically, costs over 1000 CHF
**Problem with EV**: If PKI is compromised and someone issues a DV for an EV domain, normally, people don't realize it, so-called "downgrade attack"

### HTTP Strict Transport Security (HSTS)
Goal
: allows servers to declare that clients should only use HTTPS 
* Prevents some "downgrade", "SSL stripping" and "session hijacking" attacks
* Browsers should automatically redirect to HTTPS or display a warning message

### HTTP Public-Key Pinning (HPKP)
Goal
: the server sends a set of publik keys to client. Only those are valid for connection to this domain.
Problem
: Public key is cached in browser. If domain has new public key, user cannot access website anymore.

### Certificate revocation list
Mechanism to invalidate certificates

Goal
: CA periodically publishes Certificate Revocation List
Problem
: If adversary controls the network, the can block the revocation list. This blocks the access to the website. 
Possible solution would be the OSCP.

### Online Certificate Status Protocol (OCSP):
Goal
: ensure certificate is valid and has not been revoked
* **Problems**:
    * OCSP servers can be slow
    * Some browsers ignore OCSP errors as fatal (Do not want to lose users)

OCSP stapling
: web servers can directly provice OCSP information, so no latency for accessing the OCSP server. **BUT**: multi-stapling needed for intermediate CAs

## Certificate Transparency (CT)

Goal
: make all public end-entity TLS certificates public knowledge, hold CAs publicly accountable for all certificates they issue

* A CT log is an append-only list of certificates.
* Merkle hash tree is used to implement log
* Entry of tree = certificate
* Periodically append new entries and sig the root

![alt text](Figures/CT_tree_original_size.png "Merkle tree structure")

When the log receives a certificate chain from domain or CA, it verifies the certificate and issues a Signed Certificate Timestamp (SCT).

![alt text](Figures/CT_log_original_size.png "Procedure of CT log")

### Security of CT

* **How CT improves security**:
    * Browser would require SCT for opening connection
    * Browser contacts log server to ensure that certificate is listed in log
    * A fake attack certificate would have to be listed in public log
    * Attacks become publicly known
* **Advantages**:
    * CT is fully operational today
    * No change to domain's web server required
* **Disadvantages**:
    * MitM attacks still possible (but public)
    * Browser still has to contact Log to verify that certificate is listed
    * Malicious Log server can add dumb certificate
