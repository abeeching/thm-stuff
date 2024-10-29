# John the Ripper: The Basics

## [Task 1] Introduction

- John the Ripper: Hash cracking tool designed to crack many types of hashes
- Prerequisites: Completed cryptography sequence (Cryptography, Public Key Cryptography, and Hashing basics)
- Room resources: See task files and attached VM

## [Task 2] Basic Terms

- Hash: take input/data of any length, spit out output/data of fixed length. Hashes are unique to the data.
- Hashing algorithms: MD4, MD5, SHA1, NTLM
- Secure hash functions: One-way; can calculate hash of data easily, but computationally infeasible to reverse this process
  - Polynomial time (class P problems): Easy to compute. If it's larger it'll take longer (but not exponentially longer or worse). Computing a hash can be done in P time.
  - Nondeterministic polynomial time (NP): Easy to verify a solution. It can be easily shown that a proposed solution is actually one. However, it may not be easy or computationally feasible to solve. Figuring out what produces a given hash is NP.
- Cracking hashes: Start with a dictionary of words/passwords and hash them. Compare with the hash you have. If you find a match, you know what password gives a given hash value.
- Most popular extended version of John the Ripper: Jumbo John

**[Task 2, Question 1] What is the most popular extended version of John the Ripper?** - Jumbo John

## [Task 3] Setting Up Your System

- Required tools: Jumbo John (John the Ripper) and the Rock You password list. Both available on the attached VM.
- Versions of John the Ripper: Standard/core version + community versions with extended eatures, most popular of which is Jumbo John. Jumbo John is available on many offensive security Linux distributions, such as Kali.
- To check if you have John the Ripper: Run `john` to get information on what version of John you have.
- For other distributions: You can install from repositories, or you can build it yourself. The latter is recommended if you want to ensure you have Jumbo John.
- For Windows: Install the correct version from their website
- Wordlists: List of words to hash and compare against, many of which can be found in the SecLists repository. On Kali and the AttackBox, check `/usr/share/wordlists`.
- Rockyou: Very large common password list from `rockyou.com` in 2009. Can be found from SecLists under Passwords -> Leaked-Databases, may need to extract with `tar xvzf rockyou.txt.tar.gz`. Available in Kali, AttackBox, and the VM.

**[Task 3, Question 1] Which website's breaches was the `rockyou.txt` wordlist created from?** - `rockyou.com`

## [Task 4] Cracking Basic Hashes

- Syntax of John: `john (OPTIONS) (FILE_PATH)`
- Automatic cracking of a hash: `john --wordlist=(WORDLIST) (PATH_TO_FILE)` - John will try to automatically identify a hash, though this may fail
- Tools: Can use websites to identify hashes if John can't; otherwise there are tools like `hash-id.py` that you can use. It's in the VM; to use it, run `python3 hash-id.py` and paste in the hash.
- Formats: `john --format=(FORMAT) --wordlist=(PATH_TO_WORDLIST) (PATH_TO_FILE)` to specify a format. Specify basic formats like MD5 with `raw-` prefix, e.g. `raw-md5`.
- List formats: `john --list=formats`. Use `grep` if needed.

The hashes here can be found in `~/John-the-Ripper-The-Basics/Task04/` on the VM. For questions 1, 3, 5, and 7 we have to use `python3 hash-id.py` and paste in hashes. For this first hash we have:

![image](https://github.com/user-attachments/assets/18f88c6e-aa13-4584-b5e2-59134157293e)

**[Task 4, Question 1] What type of hash is `hash1.txt`?** - MD5

We run `john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt` to crack the hash.

![image](https://github.com/user-attachments/assets/5910aa28-cf16-44bf-a501-e483c20fdaf4)

**[Task 4, Question 2] What is the cracked value of `hash1.txt`?** - `biscuit`

We use the same tool as before to identify the hash type in this question:

![image](https://github.com/user-attachments/assets/b144d141-3fd4-4e7c-baec-34c6e4c3c730)

**[Task 4, Question 3] What type of hash is `hash2.txt`?** - SHA1

Now that we know the hash is a SHA1 kind of hash, we can run `john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt hash2.txt`.

![image](https://github.com/user-attachments/assets/d55f8227-38c8-42ef-9d36-56bf6e83ae78)

**[Task 4, Question 4] What is the cracked value of `hash2.txt`?** - `kangeroo`

The `hash-id` script tells us the third hash is...

![image](https://github.com/user-attachments/assets/652ecbe4-7734-4e5a-aa35-9525e604104d)

**[Task 4, Question 5] What type of hash is `hash3.txt`?** - SHA256

Running `john --format=raw-sha256 --wordlist=/usr/share/wordlists/rockyou.txt hash3.txt` yields:

![image](https://github.com/user-attachments/assets/6d3c5cce-dd7f-4321-8c0c-a827c21eeedd)

**[Task 4, Question 6] What is the cracked value of `hash3.txt`?** - `microphone`

The script tells us the possible hashes are as follows. It's actually _not_ SHA-512, according to the hint. So the only other option we have is...

![image](https://github.com/user-attachments/assets/cdd5dd3a-5bc6-4c36-a31c-b5743a7e653f)

**[Task 4, Question 7] What type of hash is `hash4.txt`?** - whirlpool

Running `john --format=whirlpool --wordlist=/usr/share/wordlists/rockyou.txt hash4.txt` yields:

![image](https://github.com/user-attachments/assets/b93e2a84-a49b-417f-8b5b-9904a670cac6)

**[Task 4, Question 8] What is the cracked value of `hash4.txt`?** - `colossal`

## [Task 5] Cracking Windows Authentication Hashes

- Common task in a red team/pentesting engagement: get a hold of Windows authentication hashes and crack/leverage the hashes.
- Authentication hash: Hashed versions of passwords stored by operating systems, can crack these too. Requires privileged user access to acquire hashes.
- NThash / NTLM: Hash format used by modern Windows operating systems to store user passwords. NT stands for New Technology and is used here as a holdover from when that was an important distinction.
- Security Account Manager (SAM): Holds NTLM hashes. Can dump hashes from this by using Mimikatz or leveraging the AD database, `NTDS.dit`.
- May not need to crack these hashes in an actual environment - you could use "pass the hash" attacks from there - but cracking hashes might work if the target has bad password policy.

We'll need to crack the hash in `ntlm.txt`. To know what format we need to use, we could run `john --list=formats | grep "nt"`:

![image](https://github.com/user-attachments/assets/4fb36791-7f22-45f8-9e2c-9bccf22c7dd6)

**[Task 5, Question 1] What do we need to set the `--format` flag to in order to crack this hash?** - `nt`

So we run `john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt` to get the hash:

![image](https://github.com/user-attachments/assets/e3046ad9-6662-4b4e-83ec-e32b2a798098)

**[Task 5, Question 2] What is the cracked value of this password?** - `mushroom`

## [Task 6] Cracking /etc/shadow Hashes

- `/etc/shadow`: file where password hashes are stored on Linux machines, as well as other information such as the date of last password change + password expiration information. Each user gets a line in this file. Only accessible by root user typically.
- `unshadow (PATH_TO_PASSWD) (PATH_TO_SHADOW) > (UNSHADOWED_FILE)` combines the contents of `/etc/shadow` and `/etc/passwd` such that John can interpret and attempt to crack the hash. You can use the entire contents of both files or just the relevant lines.
- Once ready to crack, run `john --wordlist=(WORDLIST) (UNSHADOWED_FILE)`. You may need to specify a format if it doesn't work.

We'll try to crack the root user's password in `etchashes.txt`. We must first run `unshadow local_passwd local_shadow > unshadowed.txt` to generate the proper file for John, and then run `john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt`.

![image](https://github.com/user-attachments/assets/0972bd62-0781-4035-ba54-c14146ac2eae)

**[Task 6, Question 1] What is the root password?** - `1234`

## [Task 7] Single Crack Mode

- Single Crack Mode: John uses only information provided in the username to try and work out possible passwords by changing letters and numbers in the username
- Word mangling: John builds a dictionary based on information given and rules on how to mutate the word. This includes adding in numbers, capitalizing different letters, and adding different symbols. These are _mangling rules_.
- Mangling works because folks will use passwords based on usernames or services that they're logging into.
- GECOS: General Electric Comprehensive Operating System - `/etc/shadow` and `/etc/passwd` include information in the fifth field about the username in question, e.g. real name, office number, telephone number, and more. John will account for these.
- To use single crack mode, run `john --single --format=(FORMAT) (PATH_TO_FILE)`. You need to change the file format such that John can understand what information it has; do this by putting the username in front of the hash. Format: `(USERNAME):(HASH)`.

Let's crack `hash07.txt` in the `Task07` directory. We first need to identify the hash type by running `hash-id.py`:

![image](https://github.com/user-attachments/assets/35c1ccba-5233-420d-8e4c-e47f0e00cd16)

It's likely an MD5 file. Then we need to prepend the username to the hash. We can copy the hash, and echo the username plus the hash into a new file:

![image](https://github.com/user-attachments/assets/fa7b6eb7-6c89-49f3-a936-8acb65b14daa)

Once we do this, we can then run `john --single --format=raw-md5 hash07single.txt` - the filename may differ depending on how you did the preivous step:

![image](https://github.com/user-attachments/assets/0d3d7d43-fba7-4728-bd36-68e32c271b36)

**[Task 7, Question 1] What is Joker's password?** - `Jok3r`

## [Task 8] Custom Rules

- Can set custom rules for Single Crack Mode depending on what you know about the target and how passwords are set by them
- Common custom rules: Organizations require a minimum password complexity, and this is brought up for the person attempting to log in. Password complexity is good but users tend to make their passwords complex in predictable ways.
  - Example: Putting capital letters first, numbers/symbols at the end, etc.
- Creating custom rules: Set up in `/opt/john/john.conf` in the AttackBox, or `/etc/john/john.conf` elsewhere.
  - First line will be `[List.Rules:(RULE_NAME)]` - you will use this name to call the rule later
  - Regex pattern:
    - `Az`: Take word and append with characters you define
    - `A0`: Take word and prepend it with characters you define
    - `c`: Capitalize character positionally
    - `[ ]`: Character sets to append, prepend, or otherwise include. For instance, `[a]` only includes the letter "a", and `[!$%@]` includes the symbols listed within. `[0-9]` throws in all numbers, `[A-z]` includes upper- and lower-case, `[a-z]` includes lowercase only, and `[A-Z]` includes uppercase only.
    - `" "`: modifier patterns, can consist of combinations of the above
    - Example: `cAz"[0-9] [!$%@]"` would capitalize the first letter, and append a number followed by a symbol to the end
- Use custom rules by running `john --wordlist=(WORDLIST) --rule=(RULE_NAME) (PATH_TO_FILE)`

**[Task 8, Question 1] What do custom rules allow us to exploit?** - Password complexity predictability

Remember that `Az` will put anything we specify at the end of a word. We want all capital letters, so we want to use `A-Z`.

**[Task 8, Question 2] What rule would we use to add all capital letters to the end of the word?** - `Az"[A-Z]"`

**[Task 8, Question 3] What flag would we use to call a custom rule called `THMRules`?** - `--rule=THMRules`

## [Task 9] Cracking Password Protected Zip Files

- Need to use `zip2john (OPTIONS) (ZIP_FILE) > (OUTPUT_FILE)` to convert a password-protected ZIP file into something John understands.
- Once done, we run `john --wordlist=(WORDLIST) (OUTPUT_FILE)` to crack the associated hash.

We'll crack the password in the `Task09` directory. First we run `zip2john` against `secure.zip` - that is, we run `zip2john secure.zip > secure.txt`. Then we can run `john --wordlist=/usr/share/wordlists/rockyou.txt secure.txt`:

![image](https://github.com/user-attachments/assets/8d0166bb-62bc-40b5-8dfa-901ffa600f4e)

**[Task 9, Question 1] What is the password for the `secure.zip` file?** - `pass123`

Now we can extract the contents of the file. We can do this by running `unzip secure.txt` and typing the password above. From there, we can get the contents by running `cat flag.txt` in the new folder that appears:

![image](https://github.com/user-attachments/assets/539c5985-3aae-4ff0-8291-15012410370a)

**[Task 9, Question 2] What is the contents of the flag inside the zip file?** - `THM{w3ll_d0n3_h4sh_r0y4l}`

## [Task 10] Cracking Password Protected RAR Archives

- `.rar` files are those compressed by WinRAR. The process for cracking these passwords is similar to the previous task.
- Run `rar2john (RAR_FILE) > (OUTPUT_FILE)` to convert the file into something John understands.
- Then we run `john --wordlist=(WORDLIST) (OUTPUT_FILE)` to get the password, as per usual.

We start by running `rar2john secure.rar > secure.txt` and then we run `john --wordlist=/usr/share/wordlists/rockyou.txt secure.txt` to get the password:

![image](https://github.com/user-attachments/assets/d2475176-2747-455d-a2a2-13009d765644)

**[Task 10, Question 1] What is the password for the `secure.rar` file?** - `password`

To get the contents of the file, we'll need to run a different tool such as `unrar`. You may need to install it if not using the VM. To use it, we run `unrar e secure.rar` and type in the password. Then we can `cat flag.txt`.

![image](https://github.com/user-attachments/assets/22ed8fe6-3811-4977-9eb1-b58fb8b8641a)

**[Task 10, Question 2] What are the contents of the flag inside the ZIP file?** - `THM{r4r_4rch1ve5_th15_t1m3}`

## [Task 11] Cracking SSH Keys with John

- SSH private keys are stored in `id_rsa` and may often be password-protected. These can be used to log into a remote machine with key-based authentication, which may be required in CTF rooms.
- Start by using `ssh2john` (or `python3 /opt/john/ssh2john.py` depending on how the machine is set up). The command syntax is `ssh2john (ID_RSA_FILE) > (OUTPUT_FILE)`.
- Then run `john --wordlist=(WORDLIST) (OUTPUT_FILE)` to get the password.

We'll use that against the `id_rsa` file in `Task11`. Running `python /opt/john/ssh2john.py id_rsa > id_rsa.txt`, and then running `john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.txt` yields:

![image](https://github.com/user-attachments/assets/63a3beb9-6f85-4437-b732-8fd673f66bf5)

**[Task 11, Question 1] What is the SSH private key password?** - `mango`
