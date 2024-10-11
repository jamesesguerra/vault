**Cryptography** is a cornerstone of network security as it plays a part in confidentiality, integrity, and authentication. It is the act of disguising data so that an intruder can gain no information from intercepting it, while still being able to be decrypted by the receiver.

If Alice wants to encrypt a message such as "i love you", which is in **cleartext**, she must use an encryption algorithm to turn it into **ciphertext**. These encryption algorithms are known and standardized, even to intruders, and so there must be a way for it to still be secure. This is where keys come in.

#### Keys
To encrypt a message, Alice needs to provide a **key** along with her message. The algorithm takes these 2 as inputs, and produces ciphertext as output in a function `K1(m)`. Similarly, Bob needs to provide a key along with the ciphertext to a **decryption algorithm** to decode the message into cleartext in a function such as `K2(K1(m)) = m`

Keys can either be **symmetrical**, in which case Alice and Bob's keys are identical and secret; or **public keys** in which case a pair of keys is used -- 1 of them known publicly, and the other kept in secret by either Alice or Bob, but not both.

#### Types of attacks
There are different scenarios that can take place, depending on what information an intruder has.
- **ciphertext-only** - the intruder only has the ciphertext with no certain information about what it stores
- **known-plaintext** - the intruder has a part / whole of the plaintext
- **chosen-plaintext** - the intruder chooses the plaintext message and obtains its corresponding ciphertext ie if an intruder could get Alice to send a certain message, he could break the encryption scheme

#### Symmetric key cryptography

**Caesar Cipher**
One example of a rather dated and simple symmetric key algorithm is the Caesar cipher which works by taking each letter in the plaintext message and substituting it with a letter that is `k` letters later in the alphabet. In this case, the key is `k` and if you have the key it would be easy to decode the message (by unshifting them `k` places back).

**Monoalphabetic cipher**
An improvement over the Caesar cipher is the monoalphabetic cipher wherein you can't substitute every letter in the alphabet for any other letter. Brute-forcing this algorithm would require too much computing power and time, but one weakness is that if an intruder can figure out which letter corresponds to common letters / words in the alphabet (e, a, the), they can narrow down the mapping. Moreover, if they know what the message might be about, they can try and narrow down the mapping even further. 

**Block cipher**
In block ciphers, the message to be encrypted is processed in blocks of *k* bits. These are used to secure many Internet protocols such as SSL and IPsec.

To encode a block, the cipher uses a one-to-one mapping to map the *k*-bit block of text to a *k*-bit block of ciphertext. The bigger the block, the harder it is to crack this mapping as it's a function of *(2^k)!*

A technique known as **Cipher Block Chaining (CBC)** is used to add randomness to block ciphers by adding a random initialization vector (IV) and performing XORs against the previous blocks. This makes it so that 2 blocks with identical cleartext won't produce identical ciphtertext.

#### Public key encryption
Symmetric keys work fine, but one problem is that if 2 people want to share the same key, they have to establish some form of communication first. In this scenario, the receiver, Bob, has 2 keys -- 1 publicly known key, and a private key. If Alice wants to send Bob a message, she encrypts it with Bob's public key using a known encryption algorithm. On the other end when Bob receives the ciphertext, he uses his private key with a known decryption algorithm to decrypt the message. This makes it so that neither of them has to distribute any secret keys.

However, there are 2 key issues. 
1. The intruder can still figure out Bob's public key as well as the encryption algorithm used, and carry out a chosen-plaintext attack to try and figure out Alice's message to Bob
2. Unlike symmetric keys that are shared, a message no longer carries the identity of the sender. That is, if Alice and Bob share a symmetric key means that encrypted messages imply that each message is from one another. This could be lost with public keys since anyone can use Bob's public key to encrypt a message.

**RSA**
RSA is the most famous algorithms that addresses these concerns.

#### Message Integrity
Encryption can provide confidentiality between two users, but another concern is message integrity and authentication. If you were Bob, you need to verify that (1) the message indeed came from Alice and (2) the message wasn't tampered on its way to you.

##### Hash functions
A cryptographic hash function is a function that takes an input *m* and computes a fixed-size string *H(m)* known as a hash. It has a property that it's computationally infeasible two construct a message that has an identical hash with another message. 2 major hash algorithms in use today are MD5 and SHA.

##### Message Authentication Code (MAC)
Knowing hash functions, you can probably see how message integrity is performed:
1. Alice wants to send a message *m* to Bob, so she computes for its hash *H(m)*
2. Alice attaches the hash to the message, creating a payload *(m, h)*
3. Bob receives the extended message and calculates *H(m)*, and if it's the same as *h*, then Bob got the message without anyone tampering it

But it leaves one more problem, the message might not even be from Alice.So to perform message integrity and authentication, Alice and Bob will need a shared secret *s* -- their **authentication key**. Using this secret, message integrity can be performed as:
1. Alice creates her message *m* and concatenates their secret *s* to it, creating *m + s*
2. She computes the hash for this with *H(m + s)* -- their message authentication code
3. She appends their MAC to the message, creating an extended message *(m, H(m + s))*
4. Bob receives the extended message *(m, h)* and knowing *s*, calculates *H(m + s)*, and if this is equal to *h*, Bob concludes everything is fine

##### Digital Signatures
Digital signatures are a way of signing a document to guarantee anyone else that the message indeed came from you. These are more trusted than MACs because they provide non-repudiation, but are typically slower and less efficient than MAC algorithms. A natural way to support this is the public key infrastructure (PKI) since the sender has something that belongs to only them -- their private key. And so to guarantee anyone that a message came from them, they can take the hash of their message to create *H(m)*, and encrypt it with their private key to make *K(H(m))*. The receiver can then decrypt the message with the sender's public key, run the attached message through the same hashing algorithm, and if it's the same as the *H(m)* that Bob sent, Alice can guarantee the message is from Bob.

**Public key certification**
An important aspect of digital signatures is that Alice needs to trust that the public key indeed came from Bob. Otherwise, this would work for any sender as long as they encrypt the message with their private key and send in their own public key. For this reason, Alice needs to verify that the public key is indeed Bob's through a **Certification Authority (CA)** which binds a particular entity to a public key. CAs verify that an entity is who it says it is

