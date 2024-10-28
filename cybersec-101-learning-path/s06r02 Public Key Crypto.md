# Public Key Cryptography

## [Task 1] Introduction

Suppose you're talking to someone face-to-face:
- You know who they are by their voice and face - this is sort of like authentication
- You can confirm that what you're hearing is coming from the other person - you are verifying the message's authenticity by making this connection. Also, no one can somehow "change" the message in transit in this scenario, so you can be rest assured of its integrity.
- If you keep your voices low, you can keep the message hidden - thus this helps ensure its confidentiality.

This summarizes the key goals of secure communication:
- You want to make sure you're actually talking with the right person.
- You want to make sure the information came from the verified source.
- You want to make sure that no one changes the data you exchange.
- You want to make sure that no one can eavesdrop on the conversation.

Cryptography satisfies these requirements, amonmg others. Symmetric encryption mainly protects confidentiality; public-key cryptography can help provide authentication, authenticity, and integrity. Having an understanding of the basics of cryptography is good here. See the previous room.

## [Task 2] Common Uses of Asymmetric Encryption

Asymmetric encryption can be used in a number of ways. One way it can be used is to securely exchange symmetric keys, so we can leverage the faster symmetric encryption process.

The use of asymmetric encryption in this manner can be thought of as follows:
- You have a secret code for communicating and instructions on how to use the secret code, and you'd like to share it with a friend. This is the symmetric encryption cipher/key.
- You ask your friend for a lock and an indestructible box. Your friend keeps the key. You put the code in the box and lock it. The lock is the public key component.
- Your friend receives the locked box, which they unlock with their key. The key in this case is the private key.

In this case, you'd use asymmetric encryption once so that it won't affect the speed of any subsequent communication. You'd need more cryptography in order to verify that people are who they say they are, of course, but that's a discussion for later.

**[Task 2, Question 1] In the analogy presented, what real object is analgous to the public key?** - Lock

## [Task 3] RSA

RSA is a public key encryption algorithm that enables secure data transmission over insecure channels; we expect adversaries to listen in on these channels, so we'd like to protect our data.

RSA is based on the problem of factoring large numbers. Multiplying two large prime numbers is straightforward, e.g. 113 x 127 = 14351 and 982451653031 x 169743212279 = 166764499494295486767649. The tricky part is factoring out these numbers - if you didn't know the factors to begin with, you'd have a hard time factoring 14351 and 166764499494295486767649 into prime factors.

Computers can easily factorize these numbers, though once you start hitting numbers with 600 digits or so, even computers start having a hard time. So this is a good system for encryption, at least as far as we understand.

The process for performing RSA encryption is as follows. Assume Alice and Bob try to communicate with each other securely:
1. Bob chooses two prime numbers, p and q. He calculates p x q.
2. We calculate `Tot(n) = n - p - q + 1` Bob chooses a value `e` such that `e` is relatively prime to `Tot(n)`. He also chooses d such that `e x d = 1 mod Tot(n)`. The public key is `(n,e)` and the private key is `(n, d)`.
3. Then they start encrypting values `x`. Alice calculates and sends `y = x^e mod n` where `^` represents exponentiation instead of XOR.
4. Bob decrypts by calculating `x = y^d mod n`.

Mathematicians have proved that this algorithm works with modular arithmetic, though this is beyond the scope of the room. Note that p and q are typically 300-digit prime numbers _at minimum_ in practice.

RSA mathematics typically comes up in CTFs, requiring you to calculate values and break encryption with them. Some tools for solving these rooms include `RsaCtfTool` and `rsatool`. The main variables in play are typically noted as follows:
- `p` and `q`: large primes
- `n`: the product of `p` and `q`
- `(n, e)`: The public key
- `(n, d)`: The private key
- `m`: The original message, plaintext
- `c`: The encrypted message, ciphertext

In CTFs you will often be given a set of values and be asked to break the encryption and decrypt a message to retrieve a flag.

Recall that n = p x q, so in this case we must calculate `n = 4391 x 6659`.

**[Task 3, Question 1] Knowing that p = 4391 and q = 6659. What is n?** - 29239669

To calculate `Tot(n)` - that's what the ϕ symbol is, by the way - we calculate `Tot(n) = 29239669 - 4391 - 6659 + 1`.

**[Task 3, Question 2] Knowing that p = 4391 and q = 6659. What is `Tot(n)`?** - 29228620

## [Task 4] Diffie-Hellman Key Exchange

We've been discussing the issue of _key exchange_ as an issue in cryptography. Diffie-Hellman Key Exchange aims to solve this problem. It lets two parties established a shared secret over an insecure communication channel without needing a pre-existing channel and without an observer potentially acquiring the key.

For the Diffie-Hellman Key Exchange to work, we need Alice and Bob to generate secrets independently. We'll call them A and B. There's also some public common material they have, called C. Assume that when secrets are combined, they're impossible to separate, and the order in which they're combined doesn't matter. Alice and Bob combine their secrets with the common material to form AC and BC, and then they will send these to each other. They combine the received part with their own secret. This results in identical keys, ABC.

The exact process looks like this:
1. Alcie and Bob agree on public variables: a large prime number p and a generator g, 0 < g < p. These values are disclosed publicly over the communication channel, and they're expected to be large.
2. Each party picks a private integer. These are the private key and must not be disclosed.
3. Each party calculates their public key with their private key from Step 2 and the agreed-upon variables from Step 1. Alice calculates `A = g^a mod p` and Bob calculates `B = g^b mod p`. These are the public keys. The `^` represents exponentiation.
4. The key exchange happens when Alice and Bob send the keys to each other. Bob gets A, Alice gets B.
5. Alice and Bob calcualte the shared secret with the public key and their own private key. Alice calculates `B^a mod p` and Bob calculates `A^b mod p`. These should yield the same result: `g^(ab) mod p`. This is the shared secret key.

Diffie-Hellman is often used alongside RSA public key cryptography. This is used for key agreement, primarily, whereas RSA is used for digital signatures, key transport, and authentication, as well as identity-proofing. This would prevent someone else from pretending to be Alice or Bob.

For this first question, calculate A = 5^12 mod 29.

**[Task 4, Question 1] Consider p = 29, g = 5, a = 12. What is A?** - 7

Then calculate B = 5^17 mod 29.

**[Task 4, Question 2] Consider p = 29, g = 5, b = 17. What is B?** - 9

Now calculate key = 9^12 mod 29.

**[Task 4, Question 3] Knowing that p = 29, a = 12, and you have B from the second question, what is the key calculated by Bob?** - 24

We can calculate key = 7^17 mod 29 to get the final answer. Since this is the shared key, it should be the same as the previous question...and it is.

**[Task 4, Question 4] Knowing that p = 29, b = 17, and you have A from the first question, what is the key calculated by Alice?** - 24

## [Task 5] SSH

If you've used SSH before, you would be familiar with the SSH command. When using the command, we often have to type `yes` to bypass a certain prompt - let's dive into that. The SSH client will ask us if we recognize the "public key fingerprint" before proceeding with the connection. It'll call out the algorithm used to generate digital signatures and verification. It does this because it's possible that a man-in-the-middle attack is taking place; someone may have intercepted the connection and is attempting to act like the target server.

The user must authenticate the host themselves by checking the public key signature. If they say "yes" then the SSH client records the public key signature, and in the future, it won't complain about the server unless it replies with a different public key.

Once that's been settled, we then need to authenticate with SSH. This is typically done with passwords, but this isn't the best security practice since passwords can be cracked. SSH machines may actually use key authentication, using public and private keys to prove the client is valid and an authorized user on the server. SSH keys are RSA keys by default, though you can choose which algorithm to generate and add a passphrase to encrypt the SS key.

The `ssh-keygen` command generates key pairs, typically. It supports the following algorithms:
- DSA, Digital Signature Algorithm: A public-key cryptography algorithm for digital signatures
- ECDSA, Elliptic Curve DSA: Uses ECC to provide smaller key sizes for equivalent security
- ECDSA-SK: ECDSA with Security Key: An extension of ECDSA that incorporates hardware-based security keys
- Ed25519: Public-key signature system using Edwards-curve DSA (EdDSA) with Curve25519
- Ed25519-SK, Ed25519 with Security Key: Variant of Ed25519 that uses hardware-based security keys for protection.

To generate a key, use the command `ssh-keygen -t (ALGORITHM)`. You are then prompted to enter the file in which to save the key, and provide a passphrase if desired. The key gets saved along with a fingerprint and a random art image. The generated public key is typically written as `id_(ALGORITHM).pub` and the private key typically as `id_(ALGORITHM)`.

SSH private keys are meant to be private for a reason - anyone with your SSH private key can log into any server that accepts it. Thus sharing it is a catastrophically bad idea. Note that the passphrase used to decrypt the private key doesn't identify you to the server at all; it's just used to decrypt the SSH private key.

With tools like John the Ripper, you can attack an encrypted SSH key to try and find the passphrase. Hence, you should use a complex passphrase and keep your private key private.

When generating an SSH key to log into a remote machine, generate the keys on your machine and then copy over the public key via `ssh-copy-id`. This means that the private key never exists on the target machine. This won't be a major issue for temporary keys geneated to access CTF boxes.

The permissions must be set correctly to use a private SSH key; the SSH client ignores the file and warns on it otherwise. Only the owner should be able to read or write to the private key, so you should set it with the `600` permissions or stricter. To specify a key for the Linux OpenSSH client, you'd run `ssh -i (PRIVATE_KEY_FILE_NAME) (USERNAME)@(HOST)`.

The `~/.ssh` folder is the default location for keys in OpenSSH. The `authorized_keys` file holds public keys that are allowed access to the server if key authentication is enabled. This is set up by default on Linux machines since this is more secure than using passwords. Key authentication is idea for if you want to allow SSH access for the root user.

SSH keys are a good way to get better shells in CTFs and other similar exercises. Typically you'll get in via a reverse shell with reduced functionality (e.g., those associated with `www-data`). If you can get to a normal user's account, you can leave an SSH key in the `authorized_keys` file as a backdoor.

We can check an example of a private key in the VM by going to `Public-Crypto-Basics/Task-5` and checking the file. The filename and the contents of the file suggest that RSA was used:

![image](https://github.com/user-attachments/assets/7b7ba49c-8036-4916-b0cd-27bb64fad2cf)

**[Task 5, Question 1] Check the SSH Priate Key in `~/Public-Crypto-Basics/Task-5`. What algorithm does the key use?** - RSA

## [Task 6] Digital Signatures and Certificates

Signatures are used in the real world to agree to certain conditions, to authorize transactions, or to acknowledge receipt of an item. Typically this is done with a written signature or some other thing (e.g., fingerprints). In the digital world, you have to make use of a digital signature.

Digital signatures verify the authenticity and integrity of a message or document, meaning we'll know who created/modified them. Asymmetric cryptography can be used to create a signature via the private key, and the public key can be used to verify the signature. You should be the only person with access to the private key; that way you can actually proved you signed the file. Digital signatures often have the same legal value as actual signatures.

The simplest way to digitally sign a document is to encrypt it via your private key. People can verify it came from you by decrypting it with your public key and ensuring the files match.

This is not the same as providing an electronic signature, which is the process of creating an image of a signature and putting it on top of a document. This does not prove integrity, since anyone can copy and paste images. Digital signatures will refer to signing a document via a private key or certificate. Typically Bob will encrypt a hash of his document and share it with Alice, along with the original document. Alice decrypts the encrypted hash and comapres it with the hash of the file she received. More on hashing in the next room.

Certificates are crucial for public key cryptography, as suggested above, and they're also used in HTTPS to make sure that the browser is talking to the website you intended to go to. Web servers have certificates indicating that they are the real website. Certificates have a chain of trust starting with a root certificate authority (CA). Devices, operating systems, and browsers have root CAs automatically trusted. Certificates get trusted only when the root CAs trust them. These chains of trust are typically quite long.

If you want to use HTTPS on your website, you will need a TLS certificate. You can get one from a CA by paying an annual fee, or you can use something like Let's Encrypt to get a certificate for free. Modern websites use HTTPS, so you should pursue these if that's what you're trying to do.

**[Task 6, Question 1] What does a remote web server use to prove itself to the client?** - Certificate

**[Task 6, Question 2] What would you use to get a free TLS certificate for your website?** - Let's Encrypt

## [Task 7] PGP and GPG

PGP stands for Pretty Good Privacy, and implements encryption for encrypting files, digital signatures, and more. An open source implementation of the OpenPGP standard is GnuPG, or GPG. It's commonly used in email to protect the confidentiality of email messages, and it can be used to sign an email message and confirm its integrity.

To generate GPG, you can run `gpg --full-gen-key`. You'll be prompted to choose the cryptographic algorithm used and whether you're using it to sign things only or to sign and encrypt things. You will also be prompted to choose an expiry date of the generated key, along with information about yourself including your name, email address, and a comment about the key's purpose.

GPG may be needed to decrypt things in CTFs. With PGP and GPG, we can protect private keys with passphrases similarly to SSH private keys. If the key is passphrase-protected, it may be possible to crack it with a tool like John the Ripper (and its associated `gpg2john` utility). In this task, we won't be using a key protected with a passphrase.

Let's use GPG to decrypt the message in `~/Public-Crypto-Basics/Task-7`, specifically the `message.gpg`. To set up the key, we'd run:
- `gpg --import tryhackme.key` to import the key, and
- `gpg --decrypt message.gpg` to decrypt the message.

Doing these yields:

![image](https://github.com/user-attachments/assets/476b5dc3-6ae5-4d35-be1b-81d362dd2033)

**[Task 7, Question 1] Use GPG to decrypt the message in `~/Public-Crypto-Basics/Task-7`. What secret word does the message hold?** - Pineapple

## [Task 8] Conclusion

Some concluding remarks:
- Cryptography is the science of securing communication in the presence of adversaries.
- Cryptanalysis is the science of breaking and bypassing cryptographic systems.
- A brute-force attack involves trying every possible password combination.
- A dictionary attack involves trying words from a dictionary in an attempt to find a password.

To summarize: This room focused on public key cryptography systems and how they're commonly used. We also know how RSA, Diffie-Hellman, SSH key pairs, digital signatures, certificates, and OpenPGP all work. Our next focus will be on hashing.
