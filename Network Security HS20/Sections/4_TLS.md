# TLS

## High level goals

**Entity authentication**

* Server side of the channel is **always** (only in TLS 1.3) authenticated
* Client side is **optionally** authenticated
* Via asymmetric crypto (e.g. signatures) or a symmetric pre-shared key

**Confidentiality**

* Data sent over the channel is only visible to the endpoints
* TLS does not hide the length of the data it transmits (but allows padding)

**Integrity**

* Data sent over the channel cannot be modified without detection
* Integrity guarantees also cover reordering, insertion, deletion of data (packets)

TLS aims for security even if the attacker has complete control over the network. Only requires reliable, in-order data stream (TCP).

### Secondary goals

* Efficiency
* Flexibility (choices of algorithms and authentication methods)
* Self-negotiation 
* Protection of negotiations (Prevent MitM attacker from performing version and cipher suite downgrade attacks)

\begin{figure}[h]
\centerline{\includegraphics[width=3.8in,height=2in]{Figures/TLS_architecture.png}}
\caption{TLS protocol architecture (1.3)}
\label{fig}
\end{figure}

## Why TLS 1.3

* Lot of attacks in older versions found
* Increasing importance of protocol
* Older versions are too slow (3 RTT to set up secure channel)

## Record Protocol

Records
: Protocol data units in TLS (fragment of data stream)

Ctype field
: Single byte indicating whether conten is handshake message, alert message or          application data
AED nonce 
: Constructed from 64-bit sequence number(SQN) and IV (pseudorandom value derived from   secrets in TLS Handshake Protocol)
Record Header
: Contains dummy type field, to imitate TLS 1.2 and length of AEAD ciphertext.
SQN
: sequence nr that is maintained as a counter at each endpoint, but not included in the record header.

### Cryptographic protections in TLD Record Protocol

* Data origin authentication, integrity for records using a MAC
* Confidentiality for records using a symmetric encryption algorithm
* Prevention of replay, reordering, deletion of records using per record sequence number protected by the MAC (include sequence number in the computation of the MAC, on the other end, use shallow copy of sequence number to verify MAC)
* Prevention of reflection attacks by key separation (different symmetric keys in different directions, but see Selfie attack)
* AEAD is obligatory in TLS 1.3, while earlier versions of TLS also supported MAC-encode-encrypt (MEE), which is vulnerable to several attacks.

### Padding

* Optional feature that can be used to hide true lengths of fragments
* Removed after integrity chek, so no padding oracle issues arise

### Attacks not prevented by TLS 1.3 Record Protocol

* Truncation attacks on the stream of records.
* Application layer confusion: record boundaries ≠ APDU boundaries.
* Timing attacks on the padding scheme (recognised in RFC).

## Handshake Protocol

1. Client includes DH share(s) in its first message, along with `ClientHello`,           anticipating group(s) that server will accept
2. Server responds with single DH share in its
  ServerKeyShare response.

* If this works, a forward secure key is established after 1 round
  trip (1 RTT). If server does not like DH group(s) offered by client, it sends a `HelloRetryRequest` and a group description back to client, in this case handshake will be 2-RTT. (It is recommended to use ECDHE because of its efficiency also )
* Provides authentication of server (usually) and client (rarely), using public key     cryptography supported by digital certificates or pre-shared key (less common, used    in IoT).
* Protects negotiation of all cryptographic parameters. SSL/TLS version number,         encryption and hash algorithms, authentication and key establishment methods. This    prevents version rollback and cipher suite downgrade attacks.
* When using static DH instead of ephemeral i.e. when the server always uses the same DH share, we loose perfect forward secrecy. Also when DH shares are generated with bad Pseudo Random Number Generators (PRNG), we might loose perfect forward secrecy or even all security guarantees (if DH share can be predicted by an attacker for both the server and client)

\begin{figure}[ht!]
\includegraphics[width=\linewidth]{Figures/TLS_handshake.PNG}
\caption{TLS Handshake Protocol}
\end{figure}

### Efficiency

* **full handshake in 1 RTT** Achieved via feature reduction (we always do (EC)DHE in   one of a short list of groups). Client speculatively sends severl DH share in         supported groups. Server picks on, replies with its share, and can already derive     Record Protocol keys.
* **o-RTT handshake** when resuming a previously established connection, client +       server keep share state enabling them to derive a PSK (pre-shared-key). But o-RTT     sacrifices perfect forward secrecy.

### Privacy
TLS 1.3. encrypts almost all handshake messages. This provides security against passive/active attackers.

### Continuity 
The handshake messages imitate the ones of TLS 1.2, so middleboxes do not block the protocol.

### Cipher Suits and Version Negotiation

* Client proposes list of cipher suites and list of (EC)DHE groups in `ClientHello` message.
* Values selected by server and incorporated into signatures and Finished messages as part of transcript.
* Assures that both sides have same view of what was proposed and what was accepted.

### Session Resumption and PSKs
Lightweight handshake protocol, exchange of nonces and new key derivation based on existing mastersecret. Reduces latency and server load, since clients return frequently to same servers.

* Achieves 1 RTT, no public key crypto. O-RTT improves this by enabling the client to send secure             application data in its first flow in the Resumption Handshake, but without perfect forward secrecy. For this an `early_traffic_secret` derived    from the stored PSK is used.
* Client and server are assumed to have already established Pre Shared Keys (PSK) using `NewSessionTicket` handshake messages (or via out of band method in pure PSK mode).
* These messages are sent under the protection of existing Record Protocol.
* Each PSK has an identity a unique string identifying it at client and server.
* 0 RTT data cannot be replayed within a connection, and cannot be
confused with 1 RTT data (by key separation). However, 0 RTT data can be vulnerable to replay attacks across connections, especially in distributed server environments.

\begin{figure}[ht!]
\includegraphics[width=\linewidth]{Figures/TLS_resumption.PNG}
\caption{TLS Resumption Handshake Protocol}
\end{figure}


