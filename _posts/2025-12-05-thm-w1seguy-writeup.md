---
title: "THM Writeup: W1seGuy – Breaking Weak XOR Encryption"
date: 2025-12-05 10:00:00 +0530
categories: [TryHackMe, Cryptography]
tags: [thm, cryptography, xor, ctf, walkthrough]
author: sonajit
toc: true
image:
  path: /assets/img/blogsimage/w1seguy.png
  lqip: data:image/webp;base64,UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoEAAQAAVAfCWkA
  alt: W1seGuy TryHackMe XOR challenge
---

## Overview

This writeup covers the **TryHackMe W1seGuy** room, which demonstrates a classic cryptography mistake: using **XOR encryption with a short repeating key**.

The challenge highlights how predictable plaintext combined with XOR allows attackers to:

- Recover the encryption key  
- Decrypt sensitive data  
- Retrieve multiple flags with minimal effort  

---

## Lab Information

- **Room:** W1seGuy  
- **Category:** Cryptography  
- **Difficulty:** Easy  

---

## Introduction

The W1seGuy room demonstrates a fundamental cryptographic weakness: using XOR encryption with a short repeating key. This writeup explains how the challenge was solved, how the first flag was recovered using known-plaintext attacks, and how the second flag was obtained by deriving the correct encryption key used by the server.

The challenge is intentionally designed to show why XOR must never be used with predictable plaintext and short keys.

---

## How the Challenge Works

When connecting to the server on port `1337`, it displays:

```text
This XOR encoded text has flag 1: <hex string>
What is the encryption key?
````

The server expects a five-character key. If the correct key is provided for that specific connection, it returns the second and final flag.

Each new connection generates a new random five-character key, but the encryption logic remains the same:

```text
cipher[i] = plaintext[i] XOR key[i % 5]
hex_output = cipher.encode().hex()
```

Because the XOR key repeats and the plaintext has a predictable format, the encryption can be reversed.

---

## Understanding XOR Weakness

XOR encryption is reversible using the same operation:

```text
cipher = plaintext XOR key
plaintext = cipher XOR key
key = cipher XOR plaintext
```

If any part of the plaintext is known or can be guessed, the corresponding key bytes can be recovered.

All TryHackMe flags begin with:

```text
THM{
```

This gives the first four plaintext bytes. The closing brace `}` allows recovery of the final key byte. With these five bytes, the entire key can be reconstructed.

---

## Recovering Flag 1

Given the following hex-encoded XOR output:

```text
110e752a3f7427543f3b003e4c103b31725b3a2c04284a622e290a41391a373241613a373e772332
```

The following Python script was used to recover the key and decrypt the message:

```python
import string

xor_output = "110e752a3f7427543f3b003e4c103b31725b3a2c04284a622e290a41391a373241613a373e772332"
key = ''
key_length = 5
target_letters = ["T", "H", "M", "{", "}"]
decrypted_msg = ''

decode_xored = bytes.fromhex(xor_output).decode()
options = list(string.ascii_letters + string.digits)

def key_gen(encrypted_char, target):
    for c in options:
        if chr(ord(encrypted_char) ^ ord(c)) == target:
            return c

for i in range(key_length):
    if i < key_length - 1:
        key += key_gen(decode_xored[i], target_letters[i])
    else:
        key += key_gen(decode_xored[-1], target_letters[i])

for i in range(len(decode_xored)):
    decrypted_msg += chr(ord(decode_xored[i]) ^ ord(key[i % len(key)]))

print(f"The encryption key is: {key}")
print(f"The decrypted message is: {decrypted_msg}")
```

### Output

```text
The encryption key is: EF8QO
The decrypted message is: THM{p1alntExtAtt4ckcAnr3alLyhUrty0urxOr}
```

### Flag 1

```text
THM{p1alntExtAtt4ckcAnr3alLyhUrty0urxOr}
```

---

## Recovering Flag 2

To retrieve the second flag, the correct five-character key must be derived for the current connection and submitted to the server.

Given the XOR output:

```text
0e2c230e3c6b05021b381f1c1a34382e500d1e2f1b0a1c462d3628171d192810174539281c210731
```

Using the same known-plaintext technique, the derived key was:

```text
ZdnuL
```

Submitting this key during the same connection:

```text
nc <IP> 1337
What is the encryption key? ZdnuL
```

Server response:

```text
Congrats! That is the correct key!
Here is flag 2: THM{BrUt3_ForC1nG_XOR_cAn_B3_FuN_nO?}
```

### Flag 2

```text
THM{BrUt3_ForC1nG_XOR_cAn_B3_FuN_nO?}
```

---

## Lessons Learned

This room demonstrates several common cryptographic mistakes:

1. Using XOR with a short repeating key
2. Encrypting predictable plaintext
3. Returning raw hex output that leaks XOR relationships
4. No randomness or salting applied

Once plaintext patterns are known, XOR encryption collapses quickly.

---

## Conclusion

The W1seGuy room provides a beginner-friendly but realistic example of why XOR should never be used for secure encryption. By exploiting predictable plaintext and a short repeating key, both flags can be recovered with minimal effort.

Thanks for reading.
