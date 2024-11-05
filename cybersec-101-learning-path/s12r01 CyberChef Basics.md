# CyberChef: The Basics

## [Task 1] Introduction

CyberChef is a web-based application designed to help with various "cyber" operation tasks in the browser. It's like a Swiss Army Knife for data, a toolbox with a bunch of different tools designed to do a specific task. These range from encodings like XOR/Base64 to complex AES/RSA operations. CyberChef operates on recipes, a series of operations executed in order.

Familiarity with hashing and cryptography is helpful for this room but not required.

## [Task 2] Accessing the Tool

The tool can be accessed online by going to `gchq.github.io/CyberChef`, or you can download the release file from the same link to use it offline. If downloading, you should use the most stable version.

## [Task 3] Navigating the Interface

There are four areas in CyberChef, each with different components or features. The following areas are:
- Operations
- Recipe
- Input
- Output

The Operations area is a repository of all the operations CyberChef can perform. These operations are meticuluosly categorized so users have convenient access to various capabilities. You can search for these specific operations as needed. Some operations include:
- From Morse Code: Translate morse code into uppercase alphanumeric characters.
- URL Encode: Encode problematic characters into percent-encoding, which is supported by URIs and URLs
- To Base64: Encode raw data into an ASCII base64 string
- To Hex: Convert input string to hex bytes separated by a specified delimiter
- To Decimal: Convert input data to ordinal integer array
- ROT13: Caesar substitution cipher, rotate alphabet characters by specified amount, 13 by default

You can hover over a specific operation for more information and links to additional resources.

The Recipe Area is the heart of the tool. You can select, arrange, and fine-tune operations to fit your needs - you can define arguments and options precisely and purposefully. You can arrange operations and specify arguments as needed. Features include saving the recipe (saving selected operations), loading previously-saved recipes, and clearing recipes. At the bottom of the Recipe Area is the BAKE! button, which processes the data. You can also tick the Auto Bake checkbox to automatically cook with the recipe(s) selected.

The Input Area lets you input text and files by pasting, typing, or dragging them in to perform operations. You can also do the following:
- Add a new input tab to use different values/data
- Open a folder as input
- Open a file as input
- Clear input and output values altogether
- Reset the pane layout, bringing every pane back to default size

The Output Area shows the data processing results. There's also options to
- Save output to file: Saves results into a .dat file
- Copy raw output to the clipboard: Copy raw output directly to the clipboard for use in other apps and documents
- Replace input with output: Overwrite input data based on operations' results quickly
- Maximize output pane: Brings tool's interface to its default window sizes

**[Task 3, Question 1] In which area can you find "From Base64"?** - Operations

**[Task 3, Question 2] Which area is considered the heart of the tool?** - Recipe

## [Task 4] Before Anything Else

There is a process for using CyberChef effectively:
1. Set a clear objective
2. Put your data into the input area
3. Select the Operations you might want to use
4. Check the output to see if it is the intended result. Hence repeat the process either from step 1 or 3.

Having a clear objective in mind is essential - that is, you need to have specific and achievable goals before you start diving into CyberChef. There's a lot of stuff you can do with CyberCHef, so you should have something in mind that you want to accomplish. Maybe you want to figure out what a gibberish string decodes to, if anything - that would be a good starting point for this step.

Once you have your objective, put your data into the Input Area - that is, use your data. If you have the gibberish string, you'd put it there.

Then, select the Operations you want to use, which can be tricky if you're not familiar with what you're dealing with yet. There's a lot of ways that a string could become gibberish, so as a result you have a lot of ways to decode the gibberish string. If you find information that the string might be using something related to encryption, you might check the Encryption/Encoding category and use operations like ROT13, Base64, Base85, and ROT47.

Once done, you can check the output to see if it's the intended result, i.e., did you accomplish your objective? If you're able to decode the gibberish string into something readable, then you're all set. Otherwise, you'll need to repeat previous steps - perhaps selecting different operations or even restarting the entire process if encryption isn't somehow involved.

**[Task 4, Question 1] At which step would you determine "What do I want to accomplish?"** - 1

## [Task 5] Practice, Practice, Practice

Here are some of the most common operations. Under Extractors:
- Extract IP Addresses: Will extract all IPv4 and IPv6 addresses.
- Extract URLs: Extract Uniform Resource Locators (URLs) from the input. The protocol (HTTP, FTP, etc.) is required; otherwise there will be many false positives.
- Extract Email Addresses: Will extract all email addresses from the input, i.e., anything in the form `something@domain[.]com`.

Under Date and Time:
- From UNIX Timestamp: Converts a UNIX timestamp to a date-time string. UNIX timestamps are 32-bit values representing the number of seconds since January 1, 1970 UTC, or the "UNIX epoch."
- To UNIX Timestamp: Parses a date-time string in UTC and returns the corresponding UNIX timestamp.

Under Data Format:
- From Base64: Decode data from an ASCII base64 string back into its raw format.
- URL Decode: Converts URI/URL percent-encoded characters back into their raw values.
- From Base85: Notation for encoding arbitrary byte data, usually more efficient than base64. Decode data from an ASCII string with an alphabet of your choosing. Presets are included.
- From Base58: Notation for encoding arbitrary byte data, differing from base64 in how it efficiently removes misread characters (e.g. l vs. I, O vs. 0) to improve readability.
- To Base62: Notation for encoding arbitrary byte data via a restricted set of symbols that humans can conveniently use and process. High number base results in shorter strings than with decimal/hex.

We'll be using these against a file provided in the task. We'll just have to download it to our machine. Our goal is to extract specific pieces of information from the text file, so we'll be focusing on operations found in the Extractors category, among others.

### More on Base Encoding

The base64, 58, 85, and 62 encoding operations are _base encodings_. These take binary data, or strings of 0s and 1s, and transform them into text-based representations using ASCII (American Standard Code for Information Interchange) characters. There are ASCII tables out there that we can refer to in case we want to learn how they work.

To manually perform base encoding (in this case, to base64):
1. Convert to binary and merge: Use the ASCII tables to find binary values for each letter, and write the entire string as binary. For instance, THM would be 01010100 01001000 01001101 -> 010101000100100001001101.
2. Divide and convert to decimal: Separate the binary string into 6 characters each: 010101 000100 100001 001101 in this case. From there, we convert each binary portion into a decimal number. You can either use a working knowledge of binary, or you can use a binary converter. Either way, the decimal version of this string is 21 4 33 13.
3. From there, you can convert to base64 manually. There are tables for different base encodings - for base64, 21 4 33 13 becomes V E h N. Thus "THM" becomes "VEhN" in base64.

### More on URL Encoding

The URL Decode feature converts percent-encoded characters back to their raw values. There are lists of percent-encoded values present on the Internet - this room specifically refers you to the Wikipedia page on percent encoding. The default character set in HTML5 is UTF-8. Some commonly-encoded characters include:
- Colon (`:`) = %3A
- Slash (`/`) = %2F
- Period (`.`) = %2E
- Equals (`=`) = %3D
- Pound/Hashtag (`#`) = %23

Now let's take a look at that sample file. We're going to examine its contents in CyberChef, so let's go ahead and read the file as input to CyberChef. For the first question, we need to extract a hidden email address, so let's use the Extract Email Addresses operation.

![image](https://github.com/user-attachments/assets/8c455961-a91e-487d-8ff1-e7f264b02836)

**[Task 5, Question 1] What is the hidden email address?** - `hidden@hotmail.com`

And for this next question we'll swap the Extract Email Address operation with the Extract IP Addresses operation. This yields two IP addresses:

![image](https://github.com/user-attachments/assets/fe64fd07-5194-4728-be70-332519db150e)

**[Task 5, Question 2] What is the hidden IP address that ends in `.232`?** - `102.20.11.232`

And then we'll use the Extract Domains operation to grab the domain that starts with a T:

![image](https://github.com/user-attachments/assets/e217083a-1b2f-48a6-904d-e7794170d63f)

**[Task 5, Question 3] Which domain address starts with the letter "T"?** - `TryHackMe.com`

For these last few questions, we'll use a different input tab since we're just working with assorted numbers and values. In this case, we'd like to write 78 in binary; while there are some online tools that can be used to do this easily, let's try setting it up in CyberChef. It requires a little more legwork.

We first grab the From Decimal operation so that CyberChef treats 78 as an entire decimal (it will convert it to the letter N - this is OK). Then we apply the To Binary operation to convert it to binary. Note that only using the "To Binary" operation will cause CyberChef to display the binary value of each digit separately.

![image](https://github.com/user-attachments/assets/7e60100e-0c08-4627-ba1e-cd88c8ebda62)

**[Task 5, Question 4] What is the binary value of the decimal number 78?** - `01001110`

Now we have to URL encode something. To do so, we copy and paste the address into CyberChef as input, apply the URL Encode operation, and then check the Encode All Special Chars option:

![image](https://github.com/user-attachments/assets/b3139f72-b37f-4ac7-a89a-60b2e69e27ca)

**[Task 5, Question 5] What is the URL encoded value of `https://tryhackme.com/r/careers`?** - `https%3A%2F%2Ftryhackme%2Ecom%2Fr%2Fcareers`

## [Task 6] Your First Official Cook

Now let's get to cooking. For the first question, we can simply refer to our discussion for Task 5, Question 2:

**[Task 6, Question 1] Using the file you downloaded in Task 5, which IP starts and ends with "10"?** - `10.10.2.101`

To encode a string in base64, we write the string into CyberChef's input, and then we apply the "To Base64" operation. Note that we must type the string _exactly_ or else we'll get the answer wrong. In this case, we get the following:

![image](https://github.com/user-attachments/assets/189f7408-6137-4e62-9462-61e46fb8c747)

**[Task 6, Question 2] What is the base64 encoded value of the string "Nice Room!"?** - TmljZSBSb29tIQ==

To decode a URL, we simply paste the URL into CyberChef and apply the "URL Decode" operation:

![image](https://github.com/user-attachments/assets/9d77e600-baef-4d84-a921-3b09e31820fc)

**[Task 6, Question 3] What is the URL decoded value for `https%3A%2F%2Ftryhackme%2Ecom%2Fr%2Froom%2Fcyberchefbasics`?** - `https://tryhackme.com/r/room/cyberchefbasics`

To convert a UNIX timestamp into a date-time string, we can paste the timestamp into CyberChef and apply the "From UNIX Timestamp" operation:

![image](https://github.com/user-attachments/assets/07bb2b20-df0b-4c39-952f-41fc1a11af49)

**[Task 6, Question 4] What is the datetime string for the Unix timestamp `1725151258`?** - `Sun 1 September 2024 00:40:58 UTC`

And lastly, to decode something from base85, we apply the "From Base85" operation:

![image](https://github.com/user-attachments/assets/e22072f0-6c9a-4e99-a3fa-12b32ee50dd7)

**[Task 6, Question 5] What is the base85 decoded string of the value `<+oue+DGm>Ap%u7`**? - This is fun!

## [Task 7] Conclusion

CyberChef is a powerful tool that you can use whenever you need to transform and decode data, among other things. It's useful for working with base64, binary, and URLs. There's many more operations that we haven't touched upon in this room, and it might be worthwhile to just explore some of them. That being said, if you're in need of large-scale processing solutions, you may want to consider using CyberChef in conjunction with other tools more suited for it - CyberChef's good if you've got something you need to investigate relatively quickly.
