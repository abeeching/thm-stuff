# Yara

## Purpose of the Room

Yara - Yet Another Ridiculous Acronym - is another tool that we can use to detect anomalies. Basic Linux familiarity is expected.

## [Task 2] What is Yara?

Yara can identify information based on both binary and textual patterns, including hexadecimal and strings contained within a file. An example includes "Hello world".

Rules are labels for these patterns. Yara rules can be written to determine if a file is malicious or not, based on features/patterns. Strings, a fundamental part of programming languages, are used to store data. Some examples include
- Bitcoin wallet data in ransomware
- C&C server IPs for botnets

Note that malware functionality is not discussed in this room. There's a couple of malware analysis rooms in THM though!

Anyway, the questions below can be answered with the information above.

**[Task 2, Question 1] What is the name of the base-16 numbering sytem that Yara can detect? - hex**

**[Task 2, Question 2] Would the text "Enter your Name" be a string in an application? - Yay**

## [Task 4] Introduction to Yara Rules

Yara is a pretty easy language to learn, but hard to master. Yara is only as effective as your understanding of the patterns. To use a yara rule is simple. All you need is
- The rule file
- The name of the file, directory, or PID

Every rule must have a name and condition. A Yara file (.yar) looks like this:

```
rule examplerule {
  condition: true
}
```

To run yara, use the syntax `yara [filename].yar [filename/directory to use rule on]`. If it works, you should see something like "examplerule file" -- indicating a match has been found. If not, you'll see an error message stating the file cannot be opened.

## [Task 5] Expanding on Yara Rules

We can use Yara to detect more than just the existence of a given file. There are several conditions we can search for.

The `meta` section of a Yara rule provides descriptive information about the author of the rule. The `desc` keyword summarizes what the rule checks for. In all, this section simply describes and summarizes the code, much like a comment in a code.

We can also add a section purely for `strings`, which we discussed briefly in [Task 2]. This section will look for specific text or hex in files and programs. The example code given allows us to search for files containing "Hello World!":

```
rule helloworld_checker {
  strings:
    $hello_world = "Hello World!"
}
```

The `$hello_world` variable contains the string that we want to search for. This variable is used to create a condition that makes the rule valid:

```
rule helloworld_checker {
  strings:
    $hello_world = "Hello World!"

  condition:
    $hello_world
}
```

This rule tells us that if a given file contains the string "Hello World!", then the rule will match. However, strings in Yara are _case-sensitive_. We won't be able to match instances of "hello world" or "HELLO WORLD" (or any combination of upper- and lower-case characters and punctuation for that matter) with just this string alone. We'd have to create multiple strings in this case. Fortunately, we can use the condition `any of them` to match for _any_ variant of the string:

```
rule helloworld_checker {
  strings:
    $hello_world = "Hello World!"
    $hello_world_lowercase = "hello world"
    $hello_world_uppercase = "HELLO WORLD"

  condition:
    any of them
}
```

This way, we don't have to rewrite all the variable names in the `condition` section.

There are other conditions that we can apply in Yara, aside from `true` and `any of them`... as in programming, we can use operators like `<=` (less than or equal to), `>=` (more than or equal to), and `!=` (not equal to) for different purposes. The rule below will check to see if a file has less than or equal to ten occurrences of the string "Hello World!" Note the use of the `#` operator in the condition instead of `$` - this is used when you want to search for a number of occurrences:

```
rule helloworld_checker {
  strings:
    $hello_world = "Hello World!"

  condition:
    #hello_world <= 10
}
```

And we can use the keywords `and`, `not`, and `or` to combine multiple conditions. If we want to check if a file has a string and is of a certain file size, then we can combine some operators as below:

```
rule helloworld_checker {
  strings:
    $hello_world = "Hello World!"

  condition:
    $hello_world and filesize < 10KB
}
```

The keywords work as you would expect from standard programming; in this case, the condition only applies if the file contains "Hello World!" AND if the filesize is less than 10 KB. If one (or both) of these conditions is false, then the rule will not apply.

There are cheatsheets that you can refer to for constructing Yara rules - see [here](https://medium.com/malware-buddy/security-infographics-9c4d3bd891ef#18dd). However, the process work ssomething like this:
1. Import any modules that you may need - more on this in a moment.
2. Identify your Yara rule with a meaningful name, determine the scope of the rule (whether it applies to all rules in the file or to just one rule's condition), and provide tags if needed.
3. Provide metadata - additional information - about your rule.
4. Identify strings that you would like to match on - thees can be text, hexadecimal, or regular expressions (regex).
5. Supply conditions, potentially using operators to create more advanced rules.

### More on Yara Conditions and Other Things

Yara conditions (and its general syntax) can look like the C language if you squint and look at it far away. Here's a few things to be on the lookout for:
- Comments can be done as you would in C. These may be single line comments denoted with `//` or multi line comments denoted with `/* ... */`.
- Hex strings can be denoted with `{ }`, and you can use `??` as a placeholder. You can leave one nibble in a byte as a placeholder, e.g. `?A`.
- You can specify that you're looking for a byte that isn't a specific value by using the `~` operator, or you can specify an arbitrary hex string of arbitrary length with a jump operator, such as `[4-6]`, which will look for arbitrary hex strings between 4 and 6 bytes. Jumps may be unbounded. You can also provide alternatives for a given fragment of a hex string like this: `(?? ?? | ... | ??)`.
- Text strings: You can append modifiers after a text string to apply different things. `nocase` allows searches to be done in case-insensitive mode, `wide` looks for strings encoded with two bytes per character (common in executables), `xor` looks for strings with a single-byte XOR applied to them, `base64` looks for strings that ahve been base64-encoded, and `fullword` looks for strings delimited by non-alphanumeric characters. Strings marked as `private` will not show in the output, and strings prefaced with `_` in their variable name don't need to be referenced in the condition.
- Regex: You would use forward-slashes to denote these, instead of quotation marks, similar to Perl: `/(regex string here)/`.
- String offsets and virtual addresses: You can use `at` to locate a string put at a particular virtual address. The `in` operator lets you search for the string in a range of offsets and addresses.
- Match length: The `*` wildcard, as used in regex, can be used to search for an arbitrary match length. If the match length is needed in a condition, use `!` in front of the string identifier.
- The `pe.entry_point` variable, from the PE module, is a special variable that holds the raw offset of a PE or ELF executable's entry point. It only applies to PE or ELF files and will evaluate to false when used with other files. This supercedes the `entrypoint` variable in older versions of Yara.
- You can read data from a file using `int8`, `int16` or `int32` - these are functions that take an offset or virtual address. The `uint` variants of these functions take in unsigned integers. Both 16- and 32-bit integers are little-endian, though if you need to use big-endian integers you can append `be` to the name of the function.
- You can require that at least a certain number of strings be present for a rule to apply. The syntax is `N of ($var1,$var2,...)`, where N is the number of strings that you'd like to match. You can use wildcards to simplify this if needed, e.g. `$var*`. Additionally, `N of them` will reference all strings in your rule. You should use `none of them` if you want the rule to match when none of the strings are found.
- To apply the same condition to many strings, you use `for (expression) of (set of string) : ( boolean_expression )`.

## [Task 6] Yara Modules

You can integrate Yara with different libraries; the room describes integrations with Cuckoo and Python PE.

Cuckoo is a sandbox that can be used for automated malware analysis, and it lets you generate Yara rules based on behaviors discovered from Cuckoo. You can create rules on specific behaviors like runtime strings and the like.

Python's PE lets you create module lets you create Yara rules from sections and elements of the Windows Portable Executable structure. This structure is standard for executables and DLL files on Windows, including programming libraries that are used. This is essential for malware analysis, which is covered elsewhere.

## [Task 7] Other Tools and Yara

Fortunately you don't really need to create custom Yara rules anymore - many resources and tools and products have been used to leverage Yara in hunt operations and incident response.

One such tool is LOKI, which is an indicator of compromise (IoC) scanner. Detection is based on checking for filenames, Yara rules, hashes, and C2 back-connections.

Another tool is THOR, which is a multi-platform IoC and Yara scanner. It comes with features like scan throttling to limit CPU usage. THOR is geared more for corporate customers.

Yet anotehr tool is FENRIR, which addresses issues from the previous tools, where requirements must be met for them to function. This guy runs with bash, which means it'll run on anything that can run bash... which means that it can run on anything in the modern day.

Last but not least, we have YAYA, or Yet Another Yara Automaton, which helps with managing multiple Yara rule repositories. YAYA imports a set of high-quality Yara rules, and then researchers could add their own rules and disable rulesets and run scans of files.

## [Task 8] Using LOKI and its Yara Rule Set

Loki is particularly helpful if you don't have a series of IOCs that you can use to detect a given threat. With Loki, you can add your own rules based on your own findings. Loki has a set of Yara rules that we can benefit from immediately. In the THM room, we can find Loki in `tools`, and run it by issuing the command `python loki.py`. Typically we'd start by running `--update` to add to the `signature-base` directory, which Loki uses to scan for stuff.

The `yara` directory contains the different Yara files used by Loki. We can run Loki on the files in the directory by running `loki.py -p .`.

Doing so from `suspicious_files/file1` yields the following output. This gives us the answer to the first few questions:
![image](https://github.com/user-attachments/assets/2794d9e9-1e4d-4e10-aa34-d40e1c32e272)

Loki says this file is Suspicious.

**[Task 8, Question 1] Scan file 1. Does Loki detect this file as suspicious/malicious or benign? - Suspicious**

The rule is webshell_metaslsoft (the underscores do not show up in the THM VM).

**[Task 8, Question 2] What Yara rule did it match on? - webshell_metaslsoft**

Based on the conveniently descriptive name, this must be a web shell.

**[Task 8, Question 3] What does Loki classify this file as? - Web Shell**

According to the output, it matched Str1.

**[Task 8, Question 4] Based on the output, what string within the Yara rule did it match on? - Str1**

The FIRST BYTES line of the output has, at the very end, the line `b374k 2.2`. This seems like a tool with a version, so this is probably what they're looking for.

**[Task 8, Question 5] What is the name and version of this hack tool? - b374k 2.2**

Now we'll actually need to investigate the Yara rule that this matched on. We can find it in `~/tools/Loki/signature-base/yara/thor-webshells.yar`, and if we look around enough we see that there is only one string in the yara rule from earlier:
![image](https://github.com/user-attachments/assets/bbaa82f9-16bf-4df4-84b8-b18b2fcc283c)

**[Task 8, Question 6] Inspect the actual Yara file that flagged rule 1. Within this rule, how many strings are there to flag this file? - 1**

Scanning the second file gives us this odd result:
![image](https://github.com/user-attachments/assets/98fcb969-905e-4935-a72f-11e53de587fd)

It's benign!

**[Task 8, Question 7] Scan file 2. Does Loki detect this file as suspicious/malicious or benign? - Benign**

And that's odd because the VERY top of the file's got this in the comments:
![image](https://github.com/user-attachments/assets/7e2919c2-bafd-4a55-816c-c4ed9f93dd5f)


**[Task 8, Question 8] Inspect file 2. What is the name and version of this web shell? - b374k 3.2.3**

## [Task 9] Creating Yara Rules with yarGen

Now we have a file that Loki didn't flag on. If that particular file shows up on other servers, then we won't be able to find it with our current rule set. Fortunately for us, it is easy to develop a Yara rule that detects this specific webshell in the environment. This is typically done once an incident occurs.

We'll start by manually opening the file and seeing what kind of strings can be used in our potential rule. The fact that there are thousands of lines makes this a prohibitively daunting task, so let's not.

What we'll use instead is a tool called yarGen, which generates Yara rules. This tool creates yara rules from strings in malware files while removing strings that appear in non-malicious files - this means you'll have to extract the list of goodware strings first before making use of yarGen.

To use yarGen, you would first update it by running `python3 yarGen.py --update` to grab the new opcodes and strings. This takes a few minutes, and then you can use yarGen to generate a yara rule. Simply run the following:

`python3 yarGen.py -m /PATH/TO/FILE --excludegood -o /OUTPUT/YARA/PATH`

We'll create a Yara rule for the file we found earlier. In our case we'd run `python3 ../../tools/yarGen/yarGen.py -m . --excludegood -o file2.yar`. If we wanted to test our Yara rule against file 2, we'd run the command `yara file2.yar file2/1ndex.php` from the `suspicious_files` directory.

**[Task 9, Question 1] From within the root of the suspicious files directory, what command would you run to test Yara and your Yara rule against file 2? - yara file2.yar file2/1ndex.php**

Running this command in the right location shows us a hit!
![image](https://github.com/user-attachments/assets/aca3ef24-45b0-42e1-bfc7-5dfab5e53bee)


**[Task 9, Question 2] Did Yara rule flag file 2? - Yay**

We now copy this file into the `signature-base` and re-scan the file with Loki. Running the rescan, we see that it works!
![image](https://github.com/user-attachments/assets/84d577f3-e3ef-4afe-82c1-ff07623ad117)


**[Task 9, Question 3] Test the Yara rule with Loki, does it flag file 2? - Yay**

According to this output, we see that the variable in question is `Zepto`.

**[Task 9, Question 4] What is the name of the variable for the string that it matched on? - Zepto**

If we check the actual Yara rule, we see that there are 20 strings:
![image](https://github.com/user-attachments/assets/5816ea5a-aebd-4ab5-b6ad-94fb5b7dd4fc)


**[Task 9, Question 5] Inspect the Yara rule. How many strings were generated? - 20**

As we see above, the last string has the condition that the filesize is less than 700KB.

**[Task 9, Question 6] One of the conditions to match on the Yara rule specifies the file size. The file has to be less than what amount? - 700KB**

### yarAnalyzer

yarAnalyzer lets us create statistics on a yara rule set and files in a directory. This generates information rule and file statistics, and you can spit it out as a CSV file for examination later. You can find it [here](https://github.com/Neo23x0/yarAnalyzer/).

### How to Write Simple, but Sound, Yara Rules

Your end goal should be to develop rules that don't generate too many false positives, but also aren't so specific to the sample that they might as well be hash values. In other words, we'd like sufficiently-generic rules. Here's some ways to make that happen:
1. Use automatic rule generators such as yarGen or the online Yara Rule Generator.
2. Check the strings and categorize them into very specific strings (hard indicators for a malicious sample) and rare strings (likely that they do not appear in goodware, but possible). You can also look for strings that look common - strings that don't appear to be specific, but don't show up in the goodware string database.
3. Test the rules against malware samples as well as a big goodware archive.
4. Handle very specific strings differently. Ideally you should have signatures that match a certain sample in a condensed way while trying to match on similar samples created by the same author or group. In the worst case scenario, consider examining the magic header, filesizes, string locations, and exclude strings that occur in false positives.
5. Define a range in whihc strings occur in a match, such as metasploit meterpreter payload. Determine any abnormal string locations and frequencies, as well as any distances between strings.
6. Aim for less CPU cycles - avoid CPU-intensive checks and use condition-checking shortcuts.
7. Use yarGen's opcode feature.
8. Use yarAnalyzer to check the coverage of your rules.
9. Use string extraction and colorization tools to spot malware strings, offsets, and more.
10. Use Binarly to search for arbitrary byte patterns as needed.
11. Use the PE module as needed to investigate executable files and patterns in them.

## [Task 10] Valhalla

Valhalla is an online Yara feed, providing many hand-crafted Yara rules. We can conduct searches based on keywords, ATT&CK techniques, SHA256 hashes, and more. We'll get information about the rule, what it does, the date it was created, and a reference link for more information about hte rule. Let's use Valhalla to investigate the two files we found earlier.

The SHA256 hash of the first file is `5479f8cd1375364770df36e5a18262480a8f9d311e8eedb2c2390ecb233852ad`. Popping this into Valhalla gives us this output:
![image](https://github.com/user-attachments/assets/ac68b364-be21-4d8d-a8f5-fbef65009b8f)

We see that there are references to an Chinese APT group, so...there's that!

**[Task 10, Question 1] Enter the SHA256 hash of file 1 into Valhalla. Is this file attributed to an APT group? - Yay**

The SHA256 hash of the second file is `53fe44b4753874f079a936325d1fdc9b1691956a29c3aaf8643cdbd49f5984bf`. Popping that into Valhalla gives us the output below:
![image](https://github.com/user-attachments/assets/c86ef724-6a1f-4cf8-87e8-e8c2920c699f)

The first Yara rule that detects file2 (perhaps by date) would be `Webshell_b364k_rule1`.

**[Task 10, Question 2] Do the same for file 2. What is the name of the first Yara rule to detect file 2? - Webshell_b374k_rule1**

If we examine the VirusTotal information (the VT link) we can find that the signature matches come from the THOR APT Scanner:
![image](https://github.com/user-attachments/assets/81d0bc18-ab02-4f5f-b2d3-f8aa023b20bc)


**[Task 10, Question 3] Examine the information for file 2 from Virus Total (VT). The Yara Signature match is from what scanner? - THOR APT Scanner**

Examining the hash with VirusTotal tells us that not all antiviruses detected this as malicious.
![image](https://github.com/user-attachments/assets/79542cef-b25d-4768-8eef-040ee78b4432)


**[Task 10, Question 4] Enter the SHA256 hash of file 2 into Virus Total. Did every AV detect this as malicious? - Nay**

There are a lot of different names for this file as noted in the Details tab, but the one we're interested in is the `exe` file:
![image](https://github.com/user-attachments/assets/ca1478c9-1fa2-4dd8-a33c-ac52266d25bc)


**[Task 10, Question 5] Besides .php, what other extension is recorded for this file? - exe**

Investigating the file itself again shows us that Zepto is, indeed, a JavaScript library:
![image](https://github.com/user-attachments/assets/d84ec78b-8ee2-49b5-983a-81f7b84ac81a)


**[Task 10, Question 6] What JavaScript library is used by file 2? - Zepto**

If we grep for `b374k`, the shell name, in the `thor-webshells.yar` file, then we see that it is indeed there by default:
![image](https://github.com/user-attachments/assets/2bc7a84c-455d-4439-9577-314bcae55b8f)


**[Task 10, Question 7] Is this Yara rule in the default Yara file Loki uses to detect these type of hack tools? - Nay**
