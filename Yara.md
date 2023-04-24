# Yara

## Purpose of the Room

## [Task 2] What is Yara?

**[Task 2, Question 1] What is the name of the base-16 numbering sytem that Yara can detect? - hex**

**[Task 2, Question 2] Would the text "Enter your Name" be a string in an application? - Yay**

## [Task 4] Introduction to Yara Rules

## [Task 5] Expanding on Yara Rules

### More on Yara Conditions

## [Task 6] Yara Modules

## [Task 7] Other Tools and Yara

## [Task 8] Using LOKI and its Yara Rule Set

**[Task 8, Question 1] Scan file 1. Does Loki detect this file as suspicious/malicious or benign? - Suspicious**

**[Task 8, Question 2] What Yara rule did it match on? - webshell_metaslsoft**

**[Task 8, Question 3] What does Loki classify this file as? - Web Shell**

**[Task 8, Question 4] Based on the output, what string within the Yara rule did it match on? - Str1**

**[Task 8, Question 5] What is the name and version of this hack tool? - b374k 2.2**

**[Task 8, Question 6] Inspect the actual Yara file that flagged rule 1. Within this rule, how many strings are there to flag this file? - 1**

**[Task 8, Question 7] Scan file 2. Does Loki detect this file as suspicious/malicious or benign? - Benign**

**[Task 8, Question 8] Inspect file 2. What is the name and version of this web shell? - b374k 3.2.3**

## [Task 9] Creating Yara Rules with yarGen

**[Task 9, Question 1] From within the root of the suspicious files directory, what command would you run to test Yara and your Yara rule against file 2? - yara file2.yar file2/1ndex.php**

**[Task 9, Question 2] Did Yara rule flag file 2? - Yay**

**[Task 9, Question 3] Test the Yara rule with Loki, does it flag file 2? - Yay**

**[Task 9, Question 4] What is the name of the variable for the string that it matched on? - Zepto**

**[Task 9, Question 5] Inspect the Yara rule. How many strings were generated? - 20**

**[Task 9, Question 6] One of the conditions to match on the Yara rule specifies the file size. The file has to be less than what amount? - 700KB**

### yarAnalyzer

### How to Write Simple, but Sound, Yara Rules

## [Task 10] Valhalla

**[Task 10, Question 1] Enter the SHA256 hash of file 1 into Valhalla. Is this file attributed to an APT group? - Yay**

**[Task 10, Question 2] Do the same for file 2. What is the name of the first Yara rule to detect file 2? - Webshell_b374k_rule1**

**[Task 10, Question 3] Examine the information for file 2 from Virus Total (VT). The Yara Signature match is from what scanner? - THOR APT Scanner**

**[Task 10, Question 4] Enter the SHA256 hash of file 2 into Virus Total. Did every AV detect this as malicious? - Nay**

**[Task 10, Question 5] Besides .php, what other extension is recorded for this file? - exe**

**[Task 10, Question 6] What JavaScript library is used by file 2? - Zepto**

**[Task 10, Question 7] Is this Yara rule in the default Yara file Loki uses to detect these type of hack tools? - Nay**
