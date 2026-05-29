
# Path Traversal

## What is Path Traversal?

Path traversal (also called directory traversal) is basically when an attacker tricks a web application into accessing files it was never supposed to touch.

Normally, an app should only read files from a specific safe directory. But if the developer gets lazy (which happens a lot), user input gets directly used in file paths… and boom — now the attacker can start walking around the server’s file system like it’s their own house.

And yeah, it’s as bad as it sounds.

With this vulnerability, an attacker can:

- Read sensitive files (most common)
    
- Sometimes write or overwrite files (even worse)
    
- Potentially take full control of the server if things go really wrong
    

## Why this is dangerous (Impact)

This isn’t just “oh cool, I can read a file” — this can escalate fast.

An attacker might get access to:

- Application source code → now they understand how everything works
    
- Credentials → database passwords, API keys, etc.
    
- System files → like `/etc/passwd` on Linux
    
- Config files → which often contain secrets
    

If writing is possible:

- Modify app behavior
    
- Upload malicious scripts
    
- Gain remote code execution (game over)
    

## How it actually works (The core idea)

Let’s break it down with a simple example.

You’ve got a shopping website that shows product images:

```html
<img src="/loadImage?filename=218.png">
```

Seems harmless, right?

Behind the scenes, the server does something like this:

```
/var/www/images/ + filename
```

So if the user requests:

```
filename=218.png
```

The server reads:

```
/var/www/images/218.png
```

All good so far.

## Where things go wrong

The problem starts when the app blindly trusts user input.

An attacker can change the `filename` parameter like this:

```
../../../etc/passwd
```

So now the server builds this path:

```
/var/www/images/../../../etc/passwd
```

And here’s the trick:

`../` means “go up one directory”.

So:

- `/var/www/images/` → go up → `/var/www/`
    
- go up again → `/var/`
    
- go up again → `/`
    

Now we’re at the root of the filesystem.

Final result:

```
/etc/passwd
```

Congrats, the attacker just escaped the intended directory and accessed a system file.


## What is `/etc/passwd` and why should you care?

On Linux systems, `/etc/passwd` contains a list of all users on the system.

It’s not always super sensitive by itself, but:

- It confirms the vulnerability
    
- It helps attackers understand the system
    
- It’s often the first step before deeper attacks
    

Think of it like “Hello World” for path traversal.


## Windows version (same idea, different flavor)

Windows does the exact same thing, just with slightly different syntax.

Both of these work:

- `../`
    
- `..\`
    

Example attack:

```
..\..\..\windows\win.ini
```

So the attacker is now reading:

```
C:\windows\win.ini
```

Different OS, same mistake by the developer.


## Key takeaway (burn this into your brain)

If user input touches file paths without proper validation, it’s probably vulnerable.

And anytime you see something like:

- `file=`
    
- `filename=`
    
- `path=`
    
- `download=`
    

Your brain should immediately go:

“Can I try `../` here?”


## Quick mental model

Normal user:

```
/images/cat.png
```

Attacker:

```
/images/../../../etc/passwd
```

Same endpoint. Completely different outcome.



# Lab 1 : File path traversal, simple case

![](../../../assets/Pasted%20image%2020260408105902.png)

This is the first lab in this portswigger academy  series.


![](../../../assets/Pasted%20image%2020260408110440.png)

As i have discussed in my notes earlier this challenge is about file path traversal. which mean there are files (in our case images) are stored somewhere in the server that we can navigate to see what sensitive information are being stored without proper protection.

First we are going turn on foxy proxy or maybe use the built burp browser, if you don't wanna setup.

![](../../../assets/Pasted%20image%2020260408111524.png)

First turn on the intercept thou.
After opening the browser and refreshing it you will the fetched endpoints here.

The above screen shows that by default burp does not show images and other files so you should turn it on by clicking the Filter settings.


![](../../../assets/Pasted%20image%2020260408111817.png)

Next we should send the one of the images that starts filename.. to the repeater.

we first send it to the repeater can see this screen.

![](../../../assets/Pasted%20image%2020260408112152.png)

Next we will modify the payload to **/etc/passwd** 
- **/etc/passwd**  this command is used in linux OS or server to view someones password 
- for windows it looks like this **C:\windows\win.ini**

But this showed nothing because usually the etc password could be stored inside layered of directories so we should navigate till we reach the exact location where its stored

![](../../../assets/Pasted%20image%2020260408112520.png)

So we added ../../../ layers or this characters to help us navigate to the exact location where the credentials were stored.

As you can see it returned with the credentials we should not view hence path traversal Vulnerability.

![](../../../assets/Pasted%20image%2020260408112818.png)

As you can see the screen we have solved this lab!

## What is access control?


# Access Control

## Overview
Access control is about determining what actions a user is allowed to perform after they are authenticated.

## Core Concepts

- Authentication → verifies identity (who you are)
- Session Management → keeps track of the user across multiple HTTP requests
- Access Control (Authorization) → determines what actions the user can perform

## Important Concept: HTTP is Stateless

- Each request is independent
- Server does not remember users by default

## Sessions & Subsequent Requests

- After login, server creates a session
- A session ID is stored in a cookie
- Every request includes this session ID
- Server uses it to identify the user

## Security Impact

If session or access control is weak:
- Session hijacking
- Unauthorized actions
- Privilege escalation

## Key Takeaway
Authentication proves identity, sessions maintain identity, and access control enforces permissions.

## What is Vertical privilege escalation

This type of privilege escalation actually occurs when a regular user gains administrative rights hence can you operations like deleting other users accounts and more. 

## Unprotected functionality

in its basic form this type of privilege escalations occur when the application could not enforce protection sensitive functionality like administrative rights.

for example the website might host sensitive data in the following url.

`https://insecure-website.com/admin`

This shows that any other user can access it and often some other sensitive information might be stores this this common url. 

`https://insecure-website.com/robots.txt`

- `robots.txt` are one of the most common place to define what or what not to look on the website when the SEO tries to read or scrap the website. This might be good for website because it stops the SEO(Search Engines optimization) to not look into private data but it never stops humans thou. 
- Even if there is no information leaked inside the `robots.txt` there will always another option which is brute-force with crafted wordlists. 


## Lab 2 : Unprotected admin functionality

![](../../../assets/Pasted%20image%2020260430114806.png)

This Challenge describes that the lab have unprotected admin panel which implies that we can get vertical privilege escalation and perform whatever the admin could, in our case deleting user carlos.

The first thing to do is that to look in `robots.txt` url and as you can see the image down below we found a leak.
 - `Disallow: /administrator-panel` The Disallow command allows the SEO to not crawl the site but for human user it revealed something it should not be revealing which the admin page and there was role check every user just navigate at that url and go bananas. 

![](../../../assets/Pasted%20image%2020260430115140.png)

- as you can see down below once i navigated to the leaked url that i found in the `robots.txt`, i get to see the actual admin page which should not be possible if it was protected. 

![](../../../assets/Pasted%20image%2020260430115341.png)

- since our challenge was to delete carlos i did that and it worked and solved the challenge as you can see down below.

![](../../../assets/Pasted%20image%2020260430115559.png)

bye bye carlos 


## Unprotected functionality - Continued

In some cases, sensitive functionality is concealed by giving it a less predictable URL. This is an example of so-called **"security by obscurity"**. However, hiding sensitive functionality does not provide effective access control because users might discover the obfuscated URL in a number of ways.

Imagine an application that hosts administrative functions at the following URL:

`https://insecure-website.com/administrator-panel-yb556`

This might not be directly guessable by an attacker. However, the application might still leak the URL to users. The URL might be disclosed in JavaScript that constructs the user interface based on the user's role:
```
<script>

var isAdmin = false; 
if (isAdmin) { 
...
var adminPanelTag = document.createElement('a'); adminPanelTag.setAttribute('href', 'https://insecure-website.com/administrator-panel-yb556'); adminPanelTag.innerText = 'Admin panel'; ... } 

</script>
```
`
This script adds a link to the user's UI if they are an admin user. However, the script containing the URL is visible to all users regardless of their role.

## Lab 3 : Unprotected admin functionality with unpredictable URL

![](../../../assets/Pasted%20image%2020260430122336.png)

This challenge is basically similar to the previous challenge but instead of leaking the information in the `robots.txt` or have guessable url, this one expose it the frontend or basically we can inspect the page see where the admin page is located. 

![](../../../assets/Pasted%20image%2020260430123041.png)

As you can see down below the code leaked some information.
first is checks if the user is admin or not and if its it will kinda generate some weird character after `admin-` hence making it hard to brute force or guess. 
while this is good idea at some degree but hard coding it like this is kinda mid thou coz anyone can see and abuse it.

![](../../../assets/Pasted%20image%2020260430123223.png)

so i found the hardcoded url to the admin page and i pasted it in the url and bam got the admin page.

![](../../../assets/Pasted%20image%2020260430123337.png)

since again our goal is to delete carlos again, we had to let go carlos 

![](../../../assets/Pasted%20image%2020260430123443.png)

Moral of the story use proper protection.


## Parameter-based access control methods

## Concept
The application stores user roles or privileges in user-controllable parameters like cookies, URL parameters, or hidden fields, and uses them to make access control decisions.

## How It Works
1. User logs in normally
2. Server assigns role (e.g., user/admin)
3. Role is stored in a client-controllable location
4. Server trusts this value for authorization decisions

## Example
https://site.com/home?role=admin

OR

Cookie: role=admin

## Why It's Vulnerable
- Client-side data can be modified
- Server incorrectly trusts user-controlled input
- No server-side verification of privileges

## Attack Method
- Intercept request
- Modify role parameter (user → admin)
- Access restricted functionality

## Key Insight
Never trust data from the client for authorization decisions.

## Lab 3 : User role controlled by request parameter

![](../../../assets/Pasted%20image%2020260430133735.png)

This challenge is rather a bit different because it does not involve url guessing and finding where the cookie is stored and changing the parameters but again it is access control problem.

![](../../../assets/Pasted%20image%2020260430134209.png)

we enter the provided credential wiener:peter

![](../../../assets/Pasted%20image%2020260430134324.png)

we will be presented with this interface and our goal is to reveal the admin page by modifying the cookie that decides the roles after login.

![](../../../assets/Pasted%20image%2020260430141628.png)

I am using foxy proxy for this challenge but burp built in browser will work as well.
we see the request made by the user wiener( highlighted in green). 

When we the request below the `admin=flase` for the user wiener which means that user is not a admin.
we will send to repeater to modify and the parameter to true.


![](../../../assets/Pasted%20image%2020260430140651.png)

- we can use burp or dev tools for this but i am just using both at a time for this challenge to visualize it better.
- On the left side you see down below in the dev tool i set the cookie for the admin true and refreshed the page and it revealed the admin panel as you can see down below.
- On the right side i doing the exact same thing in burp. i modified the cookie, i set the admin parameter to be true, as its highlighted in pink on the image. i sent the request and it returned 200 status code, which mean it worked. I searched down below and it showed that i am an admin but since the screen was collapsed it was not possible to show that.


![](../../../assets/Pasted%20image%2020260430140805.png)

As you can see highlighted in pink that the admin panel became visible to us. and we get to delete carlos again since the challenge Says we should delete carlos. sup with carlos thou he is being canceled a lot.

![](../../../assets/Pasted%20image%2020260430140849.png)

so just like that we gained the admin panel.

The takeway in this challenge is that we should not verify the roles of any user in the client side since it is visible to anyone with the right tools. we should always implement access control on the server not the client side.



# Horizontal Privilege Escalation

## Definition
Occurs when a user accesses another user’s data or functionality at the same privilege level.

## Example
GET /myaccount?id=123 → change to → id=124

## Root Cause
- Application trusts user-controlled input
- No verification of resource ownership

## IDOR (Insecure Direct Object Reference)
- Direct use of user input to access objects
- No proper access control checks

## Attack Steps
1. Identify parameter (id, userId, etc.)
2. Modify value
3. Observe response

## Non-Predictable IDs
- Apps may use GUIDs/UUIDs
- Harder to guess but can be leaked elsewhere

## Key Takeaway
Always verify that the user is authorized to access the requested resource.

## Lab 4 : User ID controlled by request parameter, with unpredictable user IDs

![](../../../assets/Pasted%20image%2020260501065547.png)

Here in this challenge the user ID can not be guessed since it being generated by GUID but the id is leaked in some of the posts by carlos.

![](../../../assets/Pasted%20image%2020260501065814.png)

First we need to log in with the credentials provided `wiener : peter`

![](../../../assets/Pasted%20image%2020260501071300.png)

The next step is to find the post that carlos posted since our target is to find carlos ID.
As its shown in the image above first we need to click carlos and then in our burp highlighted in yellow color is the request generated when we click carlos.

As you can you see down below inside burp the id is revealed but that should not suppose to happen.

![](../../../assets/Pasted%20image%2020260501071839.png)

Then we went to our account and refresh and we got another request highlighted in Aqua or light blue color. 

as you can see down the request highlighted in blue, that is our id but we send it to repeater and then replace it with carlos id.

![](../../../assets/Pasted%20image%2020260501072306.png)

After we sent to repeater, we swapped our ID with carlos and sent the request and then as you can see it returned 200 status code ( even thou it is not visible here) but i searched with the name carlos and we got the API key.

![](../../../assets/Pasted%20image%2020260501072408.png)

Finally i submitted the API key and solved the issue.

## Takeaway

This Challenge is sort of different from the previous ones because it access similar level access role hence Horizontal privilege escalation. While the methods are kinda similar but instead of getting admin rights and performing another operations rather it focuses exploiting or stealing API keys of similar level user.

## Lab 5 : User ID controlled by request parameter with password disclosure

![](../../../assets/Pasted%20image%2020260528040450.png)

This challenge basically have client side masked password it broken access control. 

We can solve this challenge with burp only the by viewing the source page we will see both methods. 

## Method 1 (Browser)

We first login with given credentials ``wiener:peter``
the ID parameter will be showing the user wiener first but change it to Administator. 

![](../../../assets/Pasted%20image%2020260528041005.png)

Next step would be viewing the source page of the site. 

![](../../../assets/Pasted%20image%2020260528041350.png)

so the next step is just logging as admin with the password the client leaked.

## Method 2 Using Burp

![](../../../assets/Pasted%20image%2020260528041838.png)

Its similar step here as well. we will intercept the request when the user (wiener) logs in and send the same request to repeater to change the ID parameter to view the pass.

![](../../../assets/Pasted%20image%2020260528042243.png)

After we modified the ID parameter and send it, we found the same password. The next step would be logging in as an Admin and delete the user Carlos to complete the challenge.

![](../../../assets/Pasted%20image%2020260528042847.png)

Now we are in the admin panel so our target is to ka boom Carlos

![](../../../assets/Pasted%20image%2020260528042939.png)

As you can see Deleted Carlos and Solved the Lab.

# Authentication Vulnerabilities

Authentication vulnerabilities are security weaknesses that happen during the process of verifying a user's identity. These vulnerabilities are usually very serious because authentication is directly connected to account access and sensitive functionality.

If an attacker successfully exploits an authentication vulnerability, they may gain access to user accounts, private information, or administrative features. In many cases, this can also lead to further attacks inside the application.

In this section, I learned about:

- Common authentication mechanisms used by websites
    
- Weaknesses found in authentication systems
    
- Vulnerabilities caused by poor implementation
    
- Different methods attackers use to bypass authentication
    
- Ways to make authentication systems more secure
    

## Authentication vs Authorization

Authentication and authorization are related, but they are not the same thing.

### Authentication

Authentication is the process of verifying whether a user is really who they claim to be.

Example:  
If someone logs in using the username `Carlos123`, authentication checks whether that person actually owns the account.

### Authorization

Authorization determines what an authenticated user is allowed to do inside the application.

Example:  
After Carlos123 successfully logs in, authorization decides whether the user can:

- View sensitive data
    
- Access admin pages
    
- Modify other users' accounts
    
- Delete resources
    

In short:

- Authentication = proving identity
    
- Authorization = controlling permissions
    

# Brute-Force Attacks

A brute-force attack is a method where attackers repeatedly try different username and password combinations until they find valid credentials.

These attacks are usually automated using tools and wordlists, allowing attackers to send a large number of login attempts very quickly.

Brute-force attacks are not always random. Attackers often use logic, common password patterns, leaked credentials, or publicly available information to make smarter guesses. This makes the attack more effective and reduces the time needed to find valid credentials.

Applications that rely only on username and password authentication can become highly vulnerable if they do not implement proper brute-force protections.

## Brute-Forcing Passwords

The difficulty of brute-forcing a password depends on how strong the password is.

Many websites use password policies to force users to create stronger passwords. These policies often require:

- A minimum password length
    
- Uppercase and lowercase letters
    
- Numbers
    
- Special characters
    

Even though these rules improve password strength, users often create predictable passwords so they can still remember them.

Examples:

- `mypassword` becomes `Mypassword1!`
    
- `password123` becomes `P@ssword123`
    
- `hello123` becomes `Hello123!`
    

Users also tend to make small changes when forced to update passwords regularly.

Examples:

- `Mypassword1!` becomes `Mypassword2!`
    
- `Winter2024!` becomes `Winter2025!`
    

Because human behavior is predictable, attackers can create more targeted and efficient brute-force attacks instead of trying every possible character combination.

# Username Enumeration

Username enumeration happens when an application reveals whether a username exists based on differences in its responses or behavior.

This usually occurs on:

- Login pages
    
- Registration forms
    
- Password reset pages
    

For example:

- Entering a valid username with the wrong password may return:  
    `Incorrect password`
    
- Entering an invalid username may return:  
    `User does not exist`
    

These different responses help attackers identify valid usernames.

Registration forms can also expose usernames if they display messages like:  
`This username is already taken`

Username enumeration makes brute-force attacks easier because attackers no longer need to guess both usernames and passwords. They can first collect valid usernames and then focus only on guessing passwords.


## Lab 6 : Username enumeration via different responses

![](../../../assets/Pasted%20image%2020260529090147.png)

This Challenge involves enumerating usernames and passwords.

![](../../../assets/Pasted%20image%2020260529090303.png)

First we access the page as you can see it above.

![](../../../assets/Pasted%20image%2020260529090505.png)

I inserted dummy username and password to test and capture the request. so in this stage we just send it to intruder to the bruteforce from the list of usernames and passwords that were given.
After sending it to burp intruder we shall add the username and then start the attack.

![](../../../assets/Pasted%20image%2020260529090646.png)

If you are going to use the community version of burp like me it is gonna take a minute and not ideal for large list of enumerations but since this very small list its okay. Another alternative would be using `FFUF` or `Turbo intruder` in burp extension.

As you can see above we found a username that is highlighted and we know that because the length of that particular username is higher than the rest. 

![](../../../assets/Pasted%20image%2020260529091032.png)

here we replace the dummy username with the real username we found in the intruder and then add (We select the password and click the add button above to mark it for bruteforcing). Then we practically wait until we found the pass.

![](../../../assets/Pasted%20image%2020260529091203.png)

For the Password we don't see the length of the pass rather we check unique status code. In our case we are looking for re direct status code `302`, this would basically imply that we logged in.


![](../../../assets/Pasted%20image%2020260529091246.png)

Just like that we Solved the Lab.