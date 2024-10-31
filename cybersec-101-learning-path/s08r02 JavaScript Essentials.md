# JavaScript Essentials

## [Task 1] Introduction

In the previous room, we briefly touched upon JavaScript, which is what web developers use to add interactive elements to websites with HTML and CSS. Once HTML elements are made, you can add interactivity with validation, onClick actions, animations, and more. This makes learning JavaScript as important as learning HTML and CSS. This is a very basic overview of JavaScript geared more towards a cybersecurity perspective, and how hackers can use legit functionalities to achieve malicious results.

The work in this room will be done in a virtual machine that opens in split-screen view. All work can be found in the `exercise` folder in the Desktop.

## [Task 2] Essential Concepts

The basic concepts of programming include:
- Variables, which are containers that let you store values in them. You label variables with names so you can reference them later. There are multiple ways to declare variables:
  - `var` lets you define variables in the context of a function.
  - `let` and `const` lets you define variables in the context of a block, offering more control over variable visibility in specific blocks of code.
- Data types, which define the type of value a variable can hold, including `string` (text), `number`, and `boolean` (true/false). Other data types include `null` and `object`, the latter which is used for more complex data structures.
- Functions, which are blocks of code designed to do some specific task. Group lines of code together to perform a task, and then use the function to perform tasks as often as you need to.
- Loops, which let you run a code block many times as long as the condition is true. Common loops include `for`, `while`, and `do...while`, which let you repeat tasks.
- The request-response cycle is the pattern in which a user sends a request to a web server and the server responds with the requested information. This can include webpages, data, and other resources.

Here's an example of functions and loops, among other things:
```js
<script>
  // functions: these are little blocks of code that do a task
  function PrintResult(rollNum) {
    alert("Username wiith roll number " +  rollNum + " has passed the exam");
    // additional logic to display results
  }

  // loops: these allow us to repeat tasks over and over again
  // note the "let" statement in the for loop - this means "i" will be treated as a number in this loop.
  for (let i = 0; i < 100; i++) {
    PrintResult(rollNumbers[i]); // our function gets called over and over again
  }
</script>
```

**[Task 2, Question 1] What term allows you to run a code block multiple times as long as it is a condition?** - Loop

## [Task 3] JavaScript Overview

We'll use JavaScript to create a program. We say JS is _interpreted_, since code is executed directly in the browser without prior compilation. Here's some sample code:

```js
// Hello World program
console.log("Hello, World!");

// variable, data type
let age = 25; // numeric type

// control flow
if (age >= 18) {
  console.log("You are an adult.");
} else {
  console.log("You are a minor.");
}

// function
function greet(name) {
  console.log("Hello, " + name + "!");
}

// calling function
greet("Bob");
```

JavaScript is exected mainly on the client side of things, making it easy to inspect and interact with HTML directly within the browser. Web browsers, like Google Chrome, have a console feature that let you write and execute JS code easily without additional tools. To do this:
1. Open the browser, e.g. Chrome.
2. Access the Inspect element tool (e.g. in Chrome, CTRL + Shift + I or right click anywhere and click Inspect). Basically, just get into the Developer Tools.
3. Go to the Console tab. Here you can type and run JavaScript code directly in the browser.

From here, we can run a simple program, like this:

```js
let x = 5; // variable holding first number
let y = 10; // variable holding second number
let result = x + 5; // result holds the sum of the two numbers
console.log("The result is: " + result); // displays (prints) results to the console
```

We can test this if we go into the browser. The next question asks us what the output becomes when we change the value of x to 10, and while we could easily predict what the answer is, we can confirm by actually pasting the code in and making the change. The answer should become 20:

![image](https://github.com/user-attachments/assets/0bf8bc87-833c-41b3-81f8-3431ad29eebb)

**[Task 3, Question 1] What is the code output if the value of x is changed to 10?** - `The result is: 20`

**[Task 3, Question 2] Is JavaScript a compiled or interpreted language?** - interpreted

## [Task 4] Integrating JavaScript in HTML

NOTE: This task requires a basic understanding of HTML. There are resources to catch up on this if needed; there's even one here in TryHackMe's Pre-Security learning path.

We can integrate JavaScript into HTML in two main ways: internally and externally. Internal JavaScript means that JS code is embedded directly in an HTML document. This is preferred for beginners since it allows them to see how the script interacts with the HTML. Scripts inserted this way will show up between `<script>` tags, either in the `<head>` section for scripts that need to load before page content is rendered or in the `<body>` section for those that need to interact with elements.

You can create an HTML document with internal JavaScript by creating a new `html` file. You can right-click the Desktop, click Create Document -> Empty File, and name it with a `.html` extension. Then you can right-click it and click Open With -> Pluma.

Here is some code that we can include. Once done, we can click File -> Save to save the file. Then just double-click the file to open it in the browser:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Internal JS</title>
</head>
<body>
    <h1>Addition of Two Numbers</h1>
    <p id="result"></p>

    <script>
    <!-- This is the code from earlier -->
        let x = 5;
        let y = 10;
        let result = x + y;
        <!-- Interacts with the <p> tag that has an ID of "result" and puts the result there -->
        document.getElementById("result").innerHTML = "The result is: " + result;
    </script>
</body>
</html>
```

External JavaScript involves creating and storing JS code in a separate `.js` file. This keeps HTML documents clean and organized. External JS files can be stored in the same web server as the HTML document or stored on an external web server like the cloud.

To set up external JS, create a file with a `.js` extension and save it in the same place as the HTML document.

In the HTML document, you'll need to set up the following:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>External JS</title>
</head>
<body>
    <h1>Addition of Two Numbers</h1>
    <p id="result"></p>

    <!-- Link to the external JS file -->
    <script src="script.js"></script>
</body>
</html>
```

There should be no difference in the output. The `src` attribte in the `<script>` tab tells the browser to load the JS from an external file. It'll load its content into the HTML document, making the HTML code more organized, cleaner, and easier to maintain.

It's a good idea to check whether a website is using internal or external JS in a pentesting engagement. You can check this by looking at the page's source code. We can see an example of this by going to `exercise` and going to `external_test.html`. We can right-click on the page and click `View Page Source`. If we see the `src` attribute in any `<script>` tags, we know that external JS is in use.

**[Task 4, Question 1] Which type of JavaScript integration places the code directly within the HTML document?** - internal

**[Task 4, Question 2] Which method is better for reusing JS across multiple web pages?** - external

We'll check the `external_test.html` source code. This lets us see the name of the JavaScript file used externally:

![image](https://github.com/user-attachments/assets/8e9a6e93-b3a7-4ebb-9d01-10226da95d8c)

**[Task 4, Question 3] What is the name of the external JS file that is being called by `external_test.html`?** - `thm_external.js`

**[Task 4, Question 4] What attribute links an external JS file in the `<script>` tag?** - `src`

## [Task 5] Abusing Dialogue Functions

JavaScript provides dialog boxes for interaction with users and to update content in web pages. To this end, JS provides the `alert`, `prompt`, and `confirm` functions to allow this. This lets developers display messages, gather input, and obtain user confirmation. If not implemented securely, an attacker may be able to execute attacks like Cross-Site Scripting (XSS).

The `alert()` function displays a message in a dialog box with an OK button. This is used just for conveying information and warnings to users.

The `prompt()` displays a dialog box that asks the user for input. It returns the entered value when the user clicks OK, or null if the user clicks Cancel. An example usage of these functions is

```js
name = prompt("What is your name?"); // the results of the prompt function can be stored in a variable...
alert("Hello " + name);  // ...which we can call later
```

The `confirm()` prompt displays a dialog box with a message and two buttons - OK and Cancel. If the user clicks OK, then this function returns `true`. If the user clicks Cancel, the function returns `false`.

Hackers may use these functions to disrupt the user experience; an alert message may be displayed many many times, and the user will be forced to close them all to continue using the web page. You should execute only JS files that come from trusted sources.

Launching `invoice.html` from the exercises gives us a bunch of alerts that we have to dismiss. The source code is this:

![image](https://github.com/user-attachments/assets/4df3348a-539f-4155-94b4-37dca6511e2e)

The for loop tells us that the code runs 5 times.

(As a heads up, this question actually refers to the example code provided where the loop has `i < 3`. Not sure why it would be set up differently in the VM.)

**[Task 5, Question 1] In the file `invoice.html`, how many times does the code show the alert Hacked?** - 3

**[Task 5, Question 2] Which of the JS interactive elements should be used to display a dialog box that asks the user for input?** - `prompt`

**[Task 5, Question 3] If the user enters `Tesla`, what value is stored in the `carName` variable, if `carName = prompt("What is your car name?")`?** - `Tesla`

## [Task 6] Bypassing Control Flow Statements

"Control flow" is the order in which statements and code blocks are executed, based on certain conditions. This includes control flow structures like `if-else` and `switch` statements for decision-making, and `for`, `while`, `do...while` to repeat actions.

Commonly, `if-else` is used to execute different blocks of code depending on whether some condition evaluates to `true` or `false`. At a very basic level, this is what happens in age verification, and this dictates what content will be displayed:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Age Verification</title>
</head>
<body>
    <h1>Age Verification</h1>
    <p id="message"></p>

    <script>
        age = prompt("What is your age")
        if (age >= 18) {
            document.getElementById("message").innerHTML = "You are an adult.";
        } else {
            document.getElementById("message").innerHTML = "You are a minor.";
        }
    </script>
</body>
</html>
```

A developer may implement authentication in JavaScript, where only users with a given username and password can log in.

**[Task 6, Question 1] What is the message displayed if you enter the age less than 18?** - `You are a minor.`

And here's the source code of `login.html`, which gives us the username and password:

![image](https://github.com/user-attachments/assets/ffcee3dc-2e77-4117-bd27-6b5703e7a559)

**[Task 6, Question 2] What is the password for the user `admin`?** - `ComplexPassword`

## [Task 7] Exploring Minified Files

We should have a basic understanding of how JavaScript code works now. One more thing worth exploring is the fact that some folks may minify code. Minification is the process of compressing JS files by removing all unnecessary characters (spaces, line breaks, comments), and this may even involve shortening variable names. This reduces the file size and improves load times, making the code more compact and harder to read but faster overall.

This is similar to the concept of obfuscation, where JS code is harder to understand because undesired code is added, or variables/functions are renamed to meaningless names, or dummy code is implemented, etc.

There are things online that allow you to obfuscate JavaScript code. Obfuscated code will still function correctly when put into a web browser. It may not look like it should, though!

There is also code to deobfuscate JavaScript code. This makes it easier to understand and analyze the original script.

This question refers to the following code snippet, which we can obfuscate and make extremely difficult to understand:

```js
function hi() {
  alert("Welcome to THM");
}
hi();
```

**[Task 7, Question 1] What is the alert message shown after running the file `hello.html`?** - Welcome to THM

For this question, we'll have to deobfuscate the snippet `age=0x1*0x247e+0x35*-0x2e+-0x1ae3;`. To do so, we'll go to `obf-io.deobfuscate.io`.

![image](https://github.com/user-attachments/assets/024d490e-e59a-4e87-9caa-92861948ef9b)

**[Task 7, Question 2] What is the value of the `age` variable in the following obfuscated code snippet?** - 21

## [Task 8] Best Practices

If you're developing a web app in modern day, you're probably going to use JavaScript. Here are some best practices that you should implement when developing or evaluating code for a web app:
1. Don't rely on client-side validation. JavaScript does provide this functionality, but it's possible for a user to disable or manipulate JavaScript on the client-side, so server-side validation is still necessary.
2. Don't add untrusted libraries. Bad actors can upload libraries on the Internet with names that resemble legitimate ones. Be careful about what you're including in your code.
3. Never hardcode sensitive data like API keys, access tokens, and credentials into your JavaScript code. It is easy to check the source code and get these.
4. Minify and obfuscate JavaScript code to reduce size, improve load time, and make it difficult for attackers to understand the logic of the code. An attacker can reverse-engineer it, sure, but it'll take just a tad bit more effort to do so.

**[Task 8, Question 1] Is it a good practice to blindly include JS in your code from any source (yea/nay)?** - nay

## [Task 9] Conclusion

We looked at how JavaScript works and how it gets integrated into HTML. We saw how JS provides some basic interactivity with the website and how attackers can abuse some of JavaScript's features including prompts and control flow. We then learned how JavaScript can be minified and obfuscated, as well as some best practices for keeping web app code secure.
