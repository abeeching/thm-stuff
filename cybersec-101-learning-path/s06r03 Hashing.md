# Hashing Basics

## [Task 1] Introduction

## [Task 2] Hash Functions

To calculate the SHA256 hash for the `Hashing-Basics/Task-2/passport.jpg` file, we can run `sha256sum passport.jpg`.

![image](https://github.com/user-attachments/assets/526b9021-b307-4bb7-8c38-dde2d103b4fe)

**[Task 2, Question 1] What is the SHA256 hash of the `passport.jpg` file in `~/Hashing-Basics/Task-2`?** - `77148c6f605a8df855f2b764bcc3be749d7db814f5f79134d2aa539a64b61f02`

To calculate the output size in bytes, we count how many hexadecimal digits are in the MD5 output, and then divide that by 2. There are 32 total digits, so the output size must be 16.

**[Task 2, Question 2] What is the output size in bytes of the MD5 hash function?** - 16

With an 8-bit output, we'd have 2^8 = 256 possible values.

**[Task 2, Question 3] If you have an 8-bit hash output, how many possible hash values are there?** - 256

## [Task 3] Insecure Password Storage for Authentication

To see the 20th password, we can run `cat /usr/share/wordlists/rockyou.txt | head -n 20` and see the end of the output:

![image](https://github.com/user-attachments/assets/b372472c-8c2e-4aa6-bba6-c5714283382e)

**[Task 3, Question 1] What is the 20th password in `rockyou.txt`?** - `qwerty`

## [Task 4] Using Hashing for Secure Password Storage

We use the rainbow table provided in the task to get the hash.

![image](https://github.com/user-attachments/assets/4b128d20-5d8c-4ea3-9346-77eef23897ce)

**[Task 4, Question 1] Manually check the hash "4c5923b6a6fac7b7355f53bfe2b8f8c1" using the rainbow table above.** - `inS3CyourP4$$`

For this next question we'll have to turn to an online tool, such as Hashes.com (Crackstation is the one I often use for this, but it won't pick up there). Pasting the hash there and clicking Submit & Search yields:

![image](https://github.com/user-attachments/assets/b0eb56c8-4045-44f9-bd7b-2cb63803d61a)

The string at the end, after the colon, is the password.

**[Task 4, Question 2] Crack the hash "5b31f93c09ad1d065c0491b764d04933" using an online tool.** - `tryhackme`

**[Task 4, Question 3] Should you encrypt passwords in password-verification systems?** - Nay

## [Task 5] Recognizing Password Hashes

For this, we can check online documentation or the man pages at `man 5 crypt` and scroll down. The information on `yescrypt` tells us what the hash size is:

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

(TBD - need to run these on my own machine. The VM does not handle hashcat very well!)

**[Task 6, Question 1] Use `hashcat` to crack the hash `$2a$06$7yoU3Ng8dHTXphAg913cyO6Bjs3K5lBnwq5FJyA6d01pMSrddr1ZG`, saved in `~/Hashing-Basics/Task-6/hash1.txt`.** - 

**[Task 6, Question 2] Use `hashcat` to crack the SHA-256 hash `9eb7ee7f551d2f0ac684981bd1f1e2fa4a37590199636753efe614d4db30e8e1`, saved in `~/Hashing-Basics/Task-6/hash2.txt`.** - 

## [Task 7] Hashing for Integrity Checking

## [Task 8] Conclusion
