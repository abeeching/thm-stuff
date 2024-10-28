# Cryptography Basics

## [Task 1] Introduction

In the previous section, we talked a lot about having information stay hidden and encrypted, and how that can be used to help prevent adversaries from reading data. In this section, we're going to focus on the technology that makes this secure communication possible in the first place: cryptography.

You should have some familiarity with the Linux command line before stepping into this section of rooms.

## [Task 2] Importance of Cryptography

The ultimate purpose of cryptography is to secure communication in the presence of adversaries. This includes protecting the confidentiality of data (i.e., making sure it's hidden) and integrity of data (i.e., making sure it isn't modified by a bad actor). Our focus is primarily on how to ensure these things while we know an adversary (or even just a third party) is present. We also want to use it to make sure our data is authentic (i.e., we know it comes from a certain entity). There's a few scenarios in which you would use cryptography:
- Logging in to a website
- Connecting via SSH
- Any kind of online banking requires encryption
- Ensuring that you got what you intended to download (this is thanks to _hash functions_, which are the subject of a later room in this section)

You rarely directly interact with cryptography, but it's pretty much anywhere in the digital world. This is thanks, in part, to certain standards that have been placed upon communications. If you're ever working with a company that has to handle credit card transactions, chances are they're working under the Payment Card Industry Data Security Standard (PCI DSS). This standard requires a minimum level of security to be present for those who are handling data relating to credit cards. Part of the security requirements is encryption.

There are other such standards that folks must adhere to based on different industries and regions, and as you'd expect, cryptography is a major component in them all:
- HIPAA: Health Insurance Portability and Accountability Act, which focuses on healthcare records
- HITECH: Health Information Technology for Economic and Clinical Health
- GDPR: General Data Protection Regulation, which focuses on the proection of data of residents in the EU
- DPA: The Data Protection Act, which does the same, but for folks in the UK
- And many more standards/regulations

**[Task 2, Question 1] What is the standard required for handling credit card information?** - PCI DSS

## [Task 3] Plaintext to Ciphertext

Say we want to encrypt some readable data - in formal cryptography, we'd call it _plaintext_. This doesn't necessarily have to _be_ text - it could be an image or a set of medical records. Plaintext data is data that we can plainly see. To _encrypt_ the data and make it hidden from adversaries, we must pass the plaintext through an encryption function along with a key. This encryption function returns a _ciphertext_, which is the encrypted data. The encryption function is part of the cipher. The _cipher_ is an algorithm used to convert a plaintext into ciphertext and back again.

To recover the plaintext, we pass the ciphertext and the correct key through the decryption function, which should then spit out the correct plaintext.

Here's a summary of the terms and concepts presented here - they're going to be important in any discussion of cryptography:
- The _plaintext_ is the original, readable message/data that you'd like to encrypt. As long as it is readable data, it counts as plaintext.
- The _ciphertext_ is the encrypted (i.e., scrambled, unreadable) version of the plaintext message, generated after encrypting the plaintext. Ideally, the only thing we get from ciphertext is the approximate size of the plaintext.
- A _cipher_ is an algorithm or method used to convert plaintext into ciphertext and vice versa. These are usually developed by mathematicians.
- A _key_ is a string of bits that the cipher uses to encrypt and decrypt data. The cipher is generally public knowledge, with the key remaining secret in most cases. Some keys, like a _public key_, can be known to the public too.
- _Encryption_ is the process of converting plaintext into ciphertext with a key.
- _Decryption_ is the process of converting ciphertext back into plaintext. It is the reverse of encryption, and it shouldn't be possible to do without knowing the key.

**[Task 3, Question 1] What do you call the encrypted plaintext?** - ciphertext

**[Task 3, Question 2] What do you call the process that returns the plaintext?** - decryption

## [Task 4] Historical Ciphers

Cryptography has been around for quite a long time now. One of the simplest historical ciphers is the Caesar Cipher, used approximately during the first century BCE. The idea behind the Caesar Cipher is that you shift each letter by a certain number to encrypt the message. If we wanted to encrypt `TRYHACKME` with a key of 3 under the Caesar Cipher, we'd change the T to a W, an R to a U, a Y to a B, and so on. Generally the direction is a right shift, and once we reach Z we circle back around to A. The final ciphertext in this case is `WUBKDFNPH`.

For decryption, we reverse the process. We would have to know that the key is 3 and that we're using the Caesar Cipher to do so, but once we do, we simply take each letter and go back three. Unfortunately, the Caesar Cipher has proven itself to be easy to break. There are only 25 keys, after all (a shift of 26 would just keep everything unchanged - there's only 26 letters in the English alphabet, after all). It's not impossible to just cycle through the possible keys. If you're particularly crafty you can even use statistical analysis techniques to figure out what the key is likely going to be. The Caesar Cipher is inherently insecure.

There are other historical ciphers that you may want to be aware of:
- The Vigenère cipher, which is effectively a bunch of Caesar ciphers applied at once. It's still easy to break this; it just requires a little more ingenuity in figuring out how often the cipher "repeats" in a message.
- The Enigma machine, which was used by the Germans in World War II and was ultimately broken by Allied powers.
- The one-time pad, a Cold War-era method for encryption, which is the perfect encryption system if used correctly... the issue is that each key can be used once and only once.

For this question, you'll get to see how easy it is to break the Caesar cipher. There are websites dedicated to decoding messages with the Caesar cipher; I will make use of the `cryptii` app. Our goal is to decrypt the ciphertext `XRPCTCRGNEI`:

![image](https://github.com/user-attachments/assets/398dedb7-54a1-413e-8727-b3484207a9c4)

In the middle, there are options to set the cipher and the shift (which is the key in this case). We can keep hitting the plus and minus buttons in the "shift" section to change the amount of letters shifted. We see that with a shift of 15 (that is, each letter in the plaintext was shifted right by 15 letters), we get the plaintext. We can tell because there's something actually readable there:

![image](https://github.com/user-attachments/assets/d9bfe125-1900-4d45-a0c9-0547ebcacc12)

**[Task 4, Question 1] Knowing that `XRPCTCRGNEI` was encrypted using Caesar cipher, what is the original plaintext?** - ICANENCRYPT

## [Task 5] Types of Encryption

There are two main types of encryption: symmetric and asymmetric encryption. In symmetric encryption/cryptography, the same key is used to encrypt and decrypt the data. This requires that the key be kept secret at all times, hence the alternative name "private key cryptography." This introduces a bit of a challenge - how do we communicate the key to the other party securely? This becomes even more tricky if there are multiple participants involved in the communication, and even moreso if the adversary is particularly powerful. You'd have to ensure the presence of a secure channel (method of sharing the key)... which is likely just going to be physically meeting up with the other person and giving the key to them, among other solutions.

Some symmetric encryption systems include:
- Data Encryption Standard (DES): Encryption standard from 1977 using a 56-bit key. DES keys were broken in less than a day in 1999. Steer clear.
- Triple DES (3DES): DES applied three times, making the key 168 bits effectively. More of an ad-hoc solution and has been largely deprecated, though you may still spot it used in certain systems.
- Advanced Encryption Standard (AES): A 2001 standard that uses a key size of 128, 192, or 256 bits. The most secure solution of the three here, and the one that is likely going to be found in systems now.

Other ciphers do exist, though they serve very specific purposes.

Asymmetric encryption/cryptography uses a pair of keys. One key is used for encrypting messages, and the other is used for decrypting. You can encrypt data with the public key and share that key around, though data has to be decrypted with a private key. Because a public key is involved, we may also see this referred to as "public key cryptography."

Asymmetric encryption is generally slower than symmetric encryption, and larger keys are likely required. The key public-key systems include RSA, Diffie-Hellman, and Elliptic Curve Cryptography (ECC). RSA suggests a minimum key size of 2048 bits (though 3072-bit and 4096-bit keys are also used) and Diffie-Hellman also recommends 2048-bit keys. ECC is a special case since it can achieve similar security with smaller keys, e.g. 256-bit ECC keys are about as good as 3072-bit RSA keys.

Public-key encryption is based on the notion that certain mathematical problems are easy to solve in one direction but extremely difficult (perhaps nigh-impossible) to reverse. "Extremely difficult" is a bit of an exaggeration; they key property we're looking for here is that it's practically infeasible to reverse the problem. If you've got a math problem that can't be reversed after spending millions of years working on it, you've got a good problem for public key cryptography. This will all be the focus of the next room. For now, let's just focus on the fact that asymmetric/public key encryption introduces a public key that can be shared around, and a private key is still used for decryption and needs to be kept secret.

You may see, in writings about cryptography, the names "Alice" and "Bob" among others. It's been common practice to use these fictional characters in discussing encryption systems. The idea is that Alice and Bob want to communicate securely somehow, and there's a bunch of other characters who are interested in trying to break that secure communication or facilitate it.

In summary:
- Symmetric encryption: The same key is used to encrypt and decrypt, so it must be kept secure at all times.
- Asymmetric encryption: Different keys are used to encrypt and decrypt. The private key generally decrypts and must be kept secret at all times.

**[Task 5, Question 1] Should you trust DES? Yea/Nay** - Nay

**[Task 5, Question 2] When was AES adopted as an encryption standard?** - 2001

## [Task 6] Basic Math

As the previous task might lead you to believe, mathematics is a very critical component to cryptography. Various algorithms will make use of several mathematical operations. The first is XOR, or Exclusive OR. This is a logical operation in binary arithmetic. It takes two bits and returns 1 only if they are different. Otherwise, 0 is returned. You'll often see this represented with ⊕ or ^.

The room provides an example of a _truth table_, which shows the possible outcomes of a logical operation. This helps illustrate how XOR actually works:

![image](https://github.com/user-attachments/assets/e4a00867-d6fa-47b7-8ed0-6b93aa4affaf)

If we wanted to perform `1010 ^ 1100`, we would want to perform XOR bit-by-bit. That is:
- `1 ^ 1 = 0`
- `0 ^ 1 = 1`
- `1 ^ 0 = 1`
- `0 ^ 0 = 0`

The result of this operation is `0110`.

XOR has some neat properties that make it useful for cryptography (and detecting errors). XORing a value with itself results in 0, and XORing any value with 0 leaves it unchanged. It is also a commutative and associative operation. Numerically, assuming that A, B, and C are just arbitrary binary numbers:
1. `A ^ A = 0`
2. `A ^ 0 = A`
3. Commutative property: `A ^ B = B ^ A`
4. Associative Property: `(A ^ B) ^ C = A ^ (B ^ C)`

We can actually use XOR as a simple symmetric cipher. Let's suppose that P is the plaintext, K is the key, and C is the ciphertext (this is generally how these things are represented in cryptography writings). In this case, `C = P ^ K`. The plaintext XORed with the key gives us the cipher. If we know C and K, we can easily recover the plaintext:
- `C ^ K = (P ^ K) ^ K`
- `C ^ K = P ^ (K ^ K)` by Property (3) above
- `C ^ K = P ^ 0` by Property (1) above
- `C ^ K = P` by Property (2) above

Hence, we just need to XOR the ciphertext with the key again to recover the plaintext. This is a very theoretical example; in practice, there's some more complicated stuff going on, and you'd need a key as long as the plaintext.

The other key operation in cryptography is _modulo_. This is simply the remainder you get when you divide two numbers. It's often denoted as `%`. Therefore, `x % y` is the remainder that we get when we divide x and y.

As an aside: You will have to work with large numbers in cryptography by necessity. Your calculator may not be able to handle it; in this case, you can use Python to do some of the calculations for you. Python has some built-in tools that can be used to handle operations on large integers - we'll cover this in one of the questions. There are other programming languages that can support large integers, and if all else fails, you can turn to Wolfram Alpha.

Some examples of modulo:
- `25 % 5 = 0`, because `25 / 5` gives no remainder.
- `23 % 6 = 5`, because `25 / 6` gives us 3 with a remainder of 5.
- `23 % 7 = 2`, because `25 / 7` gives us 3 with a remainder of 2.

Modulo cannot be reversed. Think about it: if we got `x % 5 = 4`, then we could come up with many numbers to fit in for x. There are many numbers that, when we divide them by 5, we get a remainder of 4.

Note that modulo will always return a non-negative result less than the divisor. Thus, if we have `x % n`, we should get something between 0 and `n - 1`.

For this first question, we'll have to do the XOR operation. Recall that we just do this bit-by-bit. I went ahead and wrote it in columnar form to make it easier to see what's going on - just do a XOR in each column.

```
  1001
^ 1010
-------
  0011
```

**[Task 6, Question 1] What's `1001 ^ 1010`?** - `0011`

For this next question, we've got some big numbers, so we'll go ahead and use one of the tools mentioned above to calculate the modulo. There are online Python interpreters we can use to this end - if using Python, you would just want to type `print(118613842 % 9091)` and then press run:

![image](https://github.com/user-attachments/assets/b42fcfad-9b81-405d-9dbb-b930129459b2)

**[Task 6, Question 2] What's `118613842 % 9091`?** - 3565

For this last question, we can just recall that 60 / 12 gives us 5 with no remainder. If you wanted to make sure, you could use the tools mentioned above or a calculator with the modulo feature.

**[Task 6, Question 3] What's `60 % 12`?** - 0

## [Task 7] Summary

And that's some of the important basics of cryptography, the types of cryptography ciphers, and some of the key mathematical operations that we'll need to be familiar with before we can really start talking about cryptography. Our next focus will be on how public-key cryptography works.
