# Crypto Refresher

Secrecy 
:   Keep data hidden from unintended receivers.
Confidentiality
:   Keep *somone else's* data secret.
Privacy
:   Keep data *about a person* secret.
Anonymity
:   Keep identity of a protocol participant secret.
Data integrity
:   Prevents unauthorized or improper changes to data.
Entity Authentication / Identification
:   Verify the identity of another protocol participant.
Data Authentication
:   Ensure data originates from claimed sender
Semantic Security
:   If adversary had to guess value of a single plaintext bit, can guess at best at random.

## Symmetric Crypto

Use same key `k` for encryption and decryption:

* **Stream Ciphers**:
    * One-time pad: Use unique random keystream for each message (secure if we dont reuse keystream, else we can xor two ciperhtexts and key cancels out)
    * Practical Stream Ciphers: Use PRG to generate keystream from seed, send ciphertext and IV (initialization vector = seed)
    * Examples: AES in CTR mode, ChaCha Stream Cipher.
    * Vulnerabilities
* **Block Ciphers**: Pseudo random permutation (PRP), each key defines a one-to-one mapping of input block     to output block. Need to encrypt each block seperately for that use a mode of operation.
    * Modes of Operation: CTR (Counter Mode), CBC (Cipher block chaining), GCM (Galois Counter Mode -> Encryption and Authentication in single pass).
    * Examples: DES, AES
* **Message Authentication Codes (MAC)**: Cryptographic checksum with a symmetric key for authentication/         integrity.
    * Hash-based MAC (HMAC)
    * Block-cipher based MAC (CMAC)

## Asymmetric Crypto
A public key `pk` for encryption and secret key `sk` for decryption. 

* Diffie-Hellman Key Generation: slide 25.
    * Discrete Logarithm Problem: $g^a mod p  = x$, given x, g and p find a is assumed to be very hard.
    * Problem: Man-in-the-Middle Attack
* RSA public, secret keypair generation: slide 29.
* Encrypted Key Exchange (EKE) DH Protocol: slide 31.
* Signature: encrypt a message with your secret key, others can decrypt it using your public key and thus    know it comes from you. Authentication enables the receiver to verify origin, signature additionally can   convince third party of origin as well.

**Asymmetric vs Symmetric Crypto:** 

| Asymmetric Crypto | Symmetric Crypto |
|------:|:-----|
| Need shared secret key | Need authentic public-key infastructure (PKI) |  
| 128 bit key for high security | 3072 bit key (RSA), 384 bit key (EC) for high security | 
| fast | slower | 
| hardware speedup | limited HW speedup | 

## Hash Functions:
Maps arbitrary length input into finite length output and fulfills following properties (Examples: MD5, SHA3):

* **One-way**: Given $y=H(x)$ cannot find $x'$ s.t. $H(x')=y$
* **Weak collision resistance**: Given $x$, cannot find $x'$ s.t. $H(x)=H(x')$
* **Strong collision resistance**: Cannot find $x \neq x'$ s.t. $H(x) = H(x')$




