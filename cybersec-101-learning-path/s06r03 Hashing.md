# Hashing Basics

## [Task 1] Introduction

Say you want to know if a copy of a file is the exact same as the original. Alternatively, suppose you wanted to know if a file you downloaded _is_ the file you intended to download. In these situations, we can use _hash values_. If the hash values of two files are equal, then you can say with high confidence that the files are identical. Hash values are just fixed-size strings/characters computed by a hash functions. Hash functions take input of arbitrary size and return an output of fixed size.

This is the last room in the Cryptography basics sequence (though there's one more room in this particular section that's focused on _breaking_ hashes - more on this later). You should have pretty good familiarity with basic cryptography concepts and asymmetric encryption. The attached VM will be used quite a bit throughout the tasks.

## [Task 2] Hash Functions

Hash functions are not the same as encryption for two key reasons:
1. There's no key.
2. It should be impossible, or at least computationally infeasible, to go from the output back to the input.

Hash functions take input data and create a summary (digest) of the data; this output is fixed in size. It should be hard to predict the output for any given input, and as mentioned before, it should be very hard to identify the input from the output. Good hashing algorithms are fast to compute, but incredibly slow to reverse. A slight change in the data ideally causes a significant change in the output.

A hash function's output is typically raw bytes which are then encoded with base64 or hexadecimal. The Linux functions `md5sum`, `sha1sum`, `sha256sum`, and `sha512sum` can all be used to produce hexadecimal-encoded hashes. Just as a heads-up: hexadecimal format prints each raw byte as two hexadecimal digits.

Hashing is vital for Internet and communications, even if it remains relatively hidden from the user. It's used to maintain data integrity and ensure password confidentiality. Whenever you log into a website, you're not sending your password over - that would just be horrific security practice. You're actually sending a hash of the password, which the server then checks to ensure it's correct. This happens locally on a computer as well.

Hash functions are designed in very particular ways; one goal is to minimize the chance of a hash collision occurring, whether by accident or because of an adversary who wants to cause one. This is a tricky requirement, because there's practically infinite inputs and only a limited number of outputs.

Say a hash function produces a 4-bit hash. In this case, we only have `2^4 = 16` possible hash values, meaning that the chance of a collision happening is very high.

In mathematical terms, this is a consequence of the _pigeonhole principle_ or the _pigeonhole effect_. Say you want to put a certain number of items ("pigeons") into a certain number of containers ("pigeonholes"), and that you have more items than you do containers. By necessity, you must put multiple items into the same container if you want all items to be in a container. If we have a hash function with 16 possible hash values, and we have 21 files we want to hash with it, then there must be files that have the same hash. Some pigeons will have to share the same pigeonhole. This is a hash collision, and while it may be unavoidable, the good hashing algorithms are designed such that it is very unlikely.

Some hash functions have been found to be insecure because an attacker can induce hash collisions - these are specifically the MD5 and SHA1 hash functions. The only saving grace for these two are that a collision cannot occur in both simultaneously - so if two files have the same MD5 hash, they may have different SHA1 hashes. Regardless, you should avoid using either of them for hashing passwords and data. For more details, you should look into the various examples of MD5 collisions out there, as well as the "Shattered" attack for SHA1.

To calculate the SHA256 hash for the `Hashing-Basics/Task-2/passport.jpg` file, we can run `sha256sum passport.jpg`.

![image](https://github.com/user-attachments/assets/526b9021-b307-4bb7-8c38-dde2d103b4fe)

**[Task 2, Question 1] What is the SHA256 hash of the `passport.jpg` file in `~/Hashing-Basics/Task-2`?** - `77148c6f605a8df855f2b764bcc3be749d7db814f5f79134d2aa539a64b61f02`

To calculate the output size in bytes, we count how many hexadecimal digits are in the MD5 output, and then divide that by 2. There are 32 total digits, so the output size must be 16.

**[Task 2, Question 2] What is the output size in bytes of the MD5 hash function?** - 16

With an 8-bit output, we'd have 2^8 = 256 possible values.

**[Task 2, Question 3] If you have an 8-bit hash output, how many possible hash values are there?** - 256

## [Task 3] Insecure Password Storage for Authentication

Our key focus now will be on password storage in the context of authentication. Authentication mechanisms in this context are just those that confirm that the user knows the password, which differs from password managers that allow us to retrieve our passwords in plaintext securely.

There are three major insecure practices when it comes to the secure storage of passwords.
1. Storing them in plaintext: Some data breaches have led to the leakage of plaintext passwords, such as RockYou, a company that developed social media apps and widgets. This led to the publishing of password _wordlists_, which literally just contain lists of passwords. This is why we've got `/usr/share/wordlists/rockyou.txt` in the pentesting distributions of Linux.
2. Using insecure encryption: Some companies, like Adobe, used bad encryption algorithms (on top of storing password hints in plaintext, among other things). Thus, when their data got breached, it was easy to retrieve the plaintext password.
3. Using insecure hashing: Still other companies, like LinkedIn, used insecure hash algorithms like SHA-1 without any _salt_. A salt is a random value that is added to a password before being hashed.

We can check the RockYou wordlist in the attached VM. To see the 20th password, we can run `cat /usr/share/wordlists/rockyou.txt | head -n 20` and see the end of the output:

![image](https://github.com/user-attachments/assets/b372472c-8c2e-4aa6-bba6-c5714283382e)

**[Task 3, Question 1] What is the 20th password in `rockyou.txt`?** - `qwerty`

## [Task 4] Using Hashing for Secure Password Storage

Storing the hash of a password is much more secure than storing the password itself. In the event of a data breach, an attacker would have to simply crack the hashes to figure out what the passwords are. However, there are some concerns with this method of password storage.

One issue is, if two users have the same password, then their hashes will be the same. This would allow an attacker to gain access into multiple accounts, and they may even go so far as to create a Rainbow Table to break the hashes. Rainbow tables are lookup tables, correlating hashes with plaintexts. This lets you figure out what password a user had just from the hash. It takes time to create, and it can take up a lot of space, but it can be used very easaily and very quickly.

Some websites, like Crackstation and Hashes.com, use massive rainbow tables to provide fast password cracking as long as the passwords aren't salted. They're typically faster to use than trying to crack the hash altogether.

That's why we use salts. Again, salts are randomly-generated values that are stored in the database and are unique to each user. You could use the same salt for all users, but this is not great practice - you'd still get duplicate passwords, and it wouldn't be hard to construct a rainbow table.

A salt is added to the start or end of a password before being hashed, meaning that each user has a different password hash even if they have the same password. Some functions like Bcrypt and Scrypt do this automatically. A salt does not need to be kept private.

There are various good practices you should follow when storing passwords securely:
1. Use a secure hashing function, e.g. Argon2, Scrypt, Bcrypt, PBKDF2. Don't use an algorithm that hasn't been well-tested by professionals.
2. Add unique salts to your passwords.
3. Concatenate passwords with the unique salts.
4. Calculate the hash of the combined password and salt.
5. Store the hash value and the unique salt used.

Note that we do all of this instead of encrypting the passwords, since you still need to store the key if you're encrypting. If the key is found or breached, then there goes all the passwords!

Let's dive into the questions. For this first one, we use the rainbow table provided in the task to get the hash.

![image](https://github.com/user-attachments/assets/4b128d20-5d8c-4ea3-9346-77eef23897ce)

**[Task 4, Question 1] Manually check the hash "4c5923b6a6fac7b7355f53bfe2b8f8c1" using the rainbow table above.** - `inS3CyourP4$$`

For this next question we'll have to turn to an online tool, such as Hashes.com (Crackstation is the one I often use for this, but it won't pick up there). Pasting the hash there and clicking Submit & Search yields:

![image](https://github.com/user-attachments/assets/b0eb56c8-4045-44f9-bd7b-2cb63803d61a)

The string at the end, after the colon, is the password.

**[Task 4, Question 2] Crack the hash "5b31f93c09ad1d065c0491b764d04933" using an online tool.** - `tryhackme`

**[Task 4, Question 3] Should you encrypt passwords in password-verification systems?** - Nay

## [Task 5] Recognizing Password Hashes

Let's now look at this in terms of offensive security. Typically if you are presented with a hash you won't be given information on what kind of hash it is - you'll have to figure it out yourself. Is it possible to recognize the type? Sure it is!

You could start by using automated tools such as hashID; however, note that these are often unreliable when used against many formats. For hashes with a prefix, these tools are reliable. You should still use context clues in the hash itself and where it came from, as well as tools, to figure out what kind of hash you're working with. If you spot a hash in a web app database, chances are you're looking at, say, an MD5 hash instead of an NTLM hash. Automated tools will get these mixed up.

Linux password hashes, for instance, are those stored in `/etc/shadow` (they used to be stored in `/etc/passwd`, but that's readable by everyone, so...). Each line has nine fields, separated by colons `:`. The first two are the login name and the encrypted password. For more information, run `man 5 crypt`.

The encrypted password field contains the hashed passphrase with four components: the prefix (algorithm ID), the options (parameters), the salt, and the hash. It looks like this: `$prefix$options$salt$hash`. The prefix makes it easy to recognize Unix/Linux-style passwords, and it specifies the hashing algorithm used to create the hash.

Here are some common Unix-style password prefixes you may encountered, ordered from strongest to weakest.
- $y$ indicates yescrypt being used, which is a scalable hashing scheme, the default, and a recommended choice.
- $gy$ indicates gost-yescrypt, which uses the GOST R 34.11-2012 hash function and the yescrypt hash method.
- $7$ indicates scrypt, which is a password-based key derivation function (PBKDF).
- $2b$, $2y$, $2a$, and $2x$ all indicate the use of bcrypt, a hash based on the Blowfish block cipher originally developed for OpenBSD. It's supported on recent versions of FreeBSD, NetBSD, Solaris 10+, and several Linux distributions.
- $6$ indicates sha512crypt, which is based on SHA-2 with 512-bit output originally developed for GNU libc. It's commonly found on older systems.
- $md5 indicates the use of SunMD5, used in Solaris.
- $1$ indicates md5crypt, used in FreeBSD.

In Microsoft Windows, passwords are hashed with NTLM, which is a variant of MD4. They're visually identical to MD4 and MD5 hashes, so you need to use context to determine the correct hash type. Password hashes get stored in the Security Account Manager, SAM. Windows tries to stop normal users from grabbing these (or "dumping" them), but there are tools like Mimikatz that can circumvent this security. Hashes are split into NT hashes and LM hashes.

Hashcat, a hash cracking tool that will be getting quite a bit of use later in this room, has more hash formats and examples available in their documentation. When it comes to other hash types, look at their length and encoding, and perhaps check the application that generated them, for more inforamtion.

For the first question, we can check online documentation or the man pages at `man 5 crypt` and scroll down. The information on `yescrypt` tells us what the hash size is:

![image](https://github.com/user-attachments/assets/315714b1-f8bb-4372-921e-3b3b84862e02)

**[Task 5, Question 1] What is the hash size in yescrypt?** - 256

The following questions can be answered by checking the Hashcat example hashes page linked in the task. For instance, if we CTRL+F and look for Cisco-ASA, we see this:

![image](https://github.com/user-attachments/assets/23882567-440a-44e9-8d18-bd40910c3461)

The number at the left is the hash mode.

**[Task 5, Question 2] What's the Hash-Mode listed for Cisco-ASA MD5?** - 2410

Looking for Cisco-IOS (and specifically looking for the entry where there's `$9$`) will give us the answer. Check what's written in parentheses below:

![image](https://github.com/user-attachments/assets/779dc487-6904-4d08-a28a-ae229f99e3fa)

**[Task 5, Question 3] What hashing algorithm is used in Cisco-IOS if it starts with `$9$`?** - `scrypt`

## [Task 6] Password Cracking

If all else fails (e.g., if a password has a salt), you'll have to sit down and actually crack the hashes. This is different from decryption; here, you'd try many different inputs (such as those from `rockyou.txt`) and potentially add in the salt if there is one and compare it to the target hash. Once it matches, you can be assured of what the password is. We use tools like Hashcat and John the Ripper (more on the latter in the next room).

To crack hashes, you'll need to pull in a lot of computational power. Modern graphics processing units (GPUs) are often used for this, as they have thousands of cores for computer graphs and can do mathematical calculations involved in hash functions quite well. Some hash types, like bcrypt, don't get cracked any faster on a GPU than a CPU, however.

Virtual machines (VMs) typically do not have access to the host's graphics cards. So, depending on the virtualization software that you're using, it may be possible to make this work, but it can take some time and finangling. If you use the CPU on a virtualized OS, prepare the performance to get hit _bad_.

If you want to run Hashcat, you're advised to run it on your host machine to get the most out of your GPU. In these exercises, I was able to make it work in Parrot OS, but not Kali Linux. You can get a copy of Hashcat for Microsoft Windows from their website, and you can simply run it from PowerShell.

John the Ripper uses a CPU by default and works in a VM out of the box, though you may get better performance if you run it on a host machine with its resources.

Our goal now is to crack some hashes. We can use `hashcat` to crack the first three hashes. It is worth noting that the attached VM will not work for this purpose, so you will need to copy the hashes into text documents on another virtual machine or your host computer and run the tools from there. You're advised not to use an online cracking service if you can, since this defeats the purpose of actually using the tools discussed in this room.

The hashcat commands will be in the form of `hashcat -m (HASH_TYPE) -a 0 (HASH_FILE) /usr/share/wordlists/rockyou.txt`. We'll specify the HASH_TYPE in each question, and the HASH_FILE will be the file specified in the question. The latter may differ if you're doing this on your own machine - it's just going to be whatever file contains the hash.

For this first question, if we check against the hashcat examples, we see that this is likely a bcrypt hash. Bcrypt hashes tend to start with something like `$2a$05`. Running `hashcat` with `-m 3200` should be fine for our purposes.

![image](https://github.com/user-attachments/assets/430d20b8-b910-4651-87f7-94a058888109)

The string of text at the end, past the colon, is the password.

**[Task 6, Question 1] Use `hashcat` to crack the hash `$2a$06$7yoU3Ng8dHTXphAg913cyO6Bjs3K5lBnwq5FJyA6d01pMSrddr1ZG`, saved in `~/Hashing-Basics/Task-6/hash1.txt`.** - `85208520`

For this next question, we're told that the password hash is SHA-256. According to the Hashcat documentation this means we will want to run with `-m 1400`.

![image](https://github.com/user-attachments/assets/0c78346a-9dbb-46b7-8994-03cf15b8ab36)

**[Task 6, Question 2] Use `hashcat` to crack the SHA-256 hash `9eb7ee7f551d2f0ac684981bd1f1e2fa4a37590199636753efe614d4db30e8e1`, saved in `~/Hashing-Basics/Task-6/hash2.txt`.** - `halloween`

Looking at the hashcat examples, this next one appears to be a sha512crypt hash, requiring us to use `-m 1800` as the mode.

![image](https://github.com/user-attachments/assets/57547b3a-5d3c-44ae-96d7-1f5086dfb3c7)

**[Task 6, Question 3] Use `hashcat` to crack the hash `$6$GQXVvW4EuM$ehD6jWiMsfNorxy5SINsgdlxmAEl3.yif0/c3NqzGLa0P.S7KRDYjycw5bnYkF5ZtB8wQy8KnskuWQS3Yr1wQ0`, saved in `~/Hashing-Basics/Task-6/hash3.txt`.** - `spaceman`

This last one won't actually show up in the `rockyou.txt` wordlist. Our next best option is to use one of the online tools. In this case, I used Crackstation. You simply paste the hash, complete the Captcha, and then click Crack Hashes.

![image](https://github.com/user-attachments/assets/25508bb4-b645-4226-a68f-5716ad8723e8)

**[Task 6, Question 4] Crack the hash `b6b0d451bbf6fed658659a9e7e5598fe`, saved in `~/Hashing-Basics/Task-6/hash4.txt`.** - `funforyou`

## [Task 7] Hashing for Integrity Checking

The other main use of hashing is to ensure data integrity, specifically the integrity of files. HAshing can be used to check if a file hasn't been changed. If you put the same data in, you should get the same data out whenever you use a hashing function. For certain software, you can get a copy of the hashes of their download files. You would simply check the hash file, and then hash the download files yourself and make sure they line up.

You can also look for duplicate files by checking for duplicate hashes on your system. If two documents have the same hash, then chances are they're the same document, unless you're using an insecure hash function.

HMAC - the Keyed-Hash Message Authentication Code - is a type of message authentication code (MAC) that uses a cryptographic hash function and a secret key to verify authenticity and integrity. HMACs can be used to make sure the person who created the HMAC is who they say they are, and it can prove that the message wasn't modified or corrupted. This is all done with a secret key to prove authenticity and a hashing algorithm.

The processed involved in using HMAC is:
1. Pad the secret key out to the block size of the hash function (i.e., make it as long as the blocks used in hashing)
2. Take the padded key and XOR it with a constant, usually a block of zeroes or ones.
3. Hash the message with the hash function and the XORed key.
4. Repeat Step 3 with the same hash function, but using the padded key XORed with another constant.
5. The final output will be the HMAC value, and is typically a fixed-size string.

Mathematically, HMAC looks like this: HMAC(K,M) = H((K ^ opad)|H((K^ipad)||M)). M and K represent the message and the key, and ^ represents the XOR operation.

To answer this question, we can just run `sha256sum libgcrypt-1.11.0.tar.bz2`:

![image](https://github.com/user-attachments/assets/f57a8c77-a53d-41ac-bf83-dece8261a05e)

**[Task 7, Question 1] What is the SHA256 hash of `libgcrypt-1.11.0.tar.bz2` found in `~/Hashing-Basics/Task-7`?** - 09120c9867ce7f2081d6aaa1775386b98c2f2f246135761aae47d81f58685b9c

And for this we can just check the hashcat examples one last time:

![image](https://github.com/user-attachments/assets/537344d3-c2a7-4ae7-85db-3447e3f98837)

**[Task 7, Question 2] What's the hashcat mode number for `HMAC-SHA512 (key = $pass)`?** - 1750

## [Task 8] Conclusion

Here are the key points:
1. Hashing takes input data and produces a fixed-size hash/digest that uniquely represents the data. Small changes in the data lead to big changes in the hash. This is not the same as encryption, as hashing is meant to be one-way only.
2. Encoding converts data from one form to another to make it compatible with a specific system, with some examples including ASCII, UTF-8, UTF-16, and more. Other encodings are used for sending and saving data without regard to language, such as base64, which youc an tinker with by running the `base64` command. This is not the same as encryption, as it can be easily reversed.
3. Encryption is the only thing that protects data confidentiality; it uses a cipher and a key. Encryption is reversible only in certain circumstances (i.e., knowing the key and the cipher).

We'll forgo using an online tool for this question; we'll instead use the command `base64 -d decode-this.txt` from the attached VM to get the decoded value:

![image](https://github.com/user-attachments/assets/cb720e33-084c-423c-94ad-0332de6409cb)

**[Task 8, Question 1] Use `base64` to decode `RU5jb2RlREVjb2RlCg==`, saved as `decode-this.txt` in `~/Hashing-Basics/Task-8`. What is the original word?** - `ENcodeDEcode`
