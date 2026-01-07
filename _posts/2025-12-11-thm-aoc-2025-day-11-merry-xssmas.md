---
title: "THM AoC 2025 Day 11 – Merry XSSmas Writeup"
date: 2025-12-11 10:00:00 +0530
categories: [TryHackMe, Walkthroughs]
tags: [thm, aoc2025, web, xss, reflected-xss, stored-xss, beginner]
toc: true
---

## Overview

This post covers **TryHackMe Advent of Cyber 2025 – Day 11**, titled **Merry XSSmas**.  
The challenge focuses on identifying and exploiting **Reflected XSS** and **Stored XSS** vulnerabilities inside McSkidy’s message portal.

The lab combines log analysis, basic web exploitation, and JavaScript behavior to recover two flags hidden behind different XSS vectors.

---

## Lab Information

- **Room:** Merry XSSmas (Advent of Cyber 2025)
- **Category:** Web Security
- **Difficulty:** Easy

---

## Introduction

I’ve been solving every challenge in the **TryHackMe Advent of Cyber 2025** event, but I only started documenting them from Day 11 onward. This challenge was a fun mix of log analysis, web exploitation, and JavaScript behavior.

Day 11 takes place in McSkidy’s secure message portal, where unusual inputs and suspicious activity begin to appear in the server logs. Our job was to investigate how attackers were injecting JavaScript and identify the XSS vulnerabilities hiding in the system.

The website ended up being vulnerable to **Reflected XSS** and **Stored XSS**, and both flags were hidden behind these two attack vectors.

---

## Understanding the Vulnerabilities

Before exploiting the portal, it's important to understand the two types of XSS involved.

### What Is Reflected XSS?

Reflected XSS occurs when user input is immediately sent back in the server response without any sanitization.  
It usually appears in features like:

- Search bars  
- URL parameters  
- Query strings  

An attack is triggered when a victim clicks a crafted URL containing malicious JavaScript.

Example flow:
```

User enters: <script>alert(1)</script>
Server reflects it back → Browser executes it

````

---

### What Is Stored XSS?

Stored XSS is more dangerous because the malicious payload is saved on the backend and executed every time someone views the affected page.

Examples:
- Blog comments
- Message boards
- User profiles

This makes it persistent — a true "set and forget" attack.

---

## Task Overview: The Message Portal

The portal had two main features to investigate:

1. **Search Bar** → reflected user input → vulnerable to **Reflected XSS**  
2. **Message Submission Form** → stored user messages → vulnerable to **Stored XSS**

Both issues stemmed from the same root cause:  
**The application returned user-controlled data to the browser without escaping or sanitizing it.**

---

## Exploiting Reflected XSS

I started by testing the search bar with a simple payload:

```html
<script>alert('You Have been H4cked')</script>
````

This worked immediately, proving the vulnerability.

To capture the official flag, the challenge hinted at Base64-encoded payloads. I used:

```html
<script>alert(atob("VEhNe0V2aWxfQnVubnl9"))</script>
```

When executed in the browser, this decoded to:

```
THM{Evil_Bunny}
```

### Reflected XSS Flag ::

```
THM{Evil_Bunny}
```

---

## Exploiting Stored XSS

Next, I tested the message submission feature. Since messages are saved on the backend, any injected script would run every time the page loaded.

I used:

```html
<script>alert('H4cked by stored')</script>
```

But again, the challenge provided a Base64 payload, so I submitted:

```html
<script>alert(atob("VEhNe0V2aWxfU3RvcmVkX0VnZ30="))</script>
```

Every page refresh triggered the alert, confirming stored XSS.
Decoding the Base64 revealed the stored flag:

```
THM{Evil_Stored_Egg}
```

### Stored XSS Flag ::

```
THM{Evil_Stored_Egg}
```

---

## Mitigation

Day 11 also emphasized how to prevent XSS in real applications. The key points:

### 1. Escape & validate user input

Never trust user data. Encode characters like `<`, `>`, `"`, `'`, and `/`.

### 2. Use `textContent` instead of `innerHTML`

`innerHTML` allows injection of HTML and script tags.
`textContent` treats everything as literal text.

### 3. Sanitize HTML when necessary

If users need formatting (bold, italics), use a whitelist-based sanitizer like DOMPurify.

### 4. Harden cookies

Add these flags:

```
HttpOnly
Secure
SameSite=Strict
```

These prevent JavaScript access and reduce the risk of session theft.

---

## Conclusion

Day 11 was a great reminder of how small oversights in input handling can open the door to serious web vulnerabilities. Both reflected and stored XSS were easy to identify and exploit due to improper output handling.

This challenge was fun, educational, and a perfect way to continue my Cybersecurity journey.

**Flags obtained:**

* `THM{Evil_Bunny}`
* `THM{Evil_Stored_Egg}`

Happy hacking! 🎄🔐

Written on December 11, 2025

