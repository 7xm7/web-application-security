# 2FA Broken Logic #



**Topic:** Authentication / Access Control

**Platform:** PortSwigger Web Security Academy 

 

---

## 1. What was the problem?

The application used a supposed strong mechanism for validating the authentication (2FA) but it allowed the client to modify request parameters using an intercepting proxy, like Burp Suite.

The first step was to watch what happens when I log in with my credentials. After I put my username and password, the website asked me for a code that I received. 

The next step was to log out of my account and switch my attention to Burp and start exploring the responses of the server if I change any value.

---

## 2. Why is this a security issue?

2FA is supposed to add an extra layer of security to users, so when a password is compromised, the system grants that only the real user can access his account. 

The problem is when users can change values during the application’s process of 2FA. In this case, I changed parameters to the user account that I wanted to gain access to.

---

## 3. What went wrong?

The application let me change parameters during the validation process, so in this case:

- It was possible to change the user that was trying to log in
- Make the application send a validation code even though the user didn’t try to log in
- Brute force the 2FA code of that user without any restriction using Burp Intruder

The issue was not cryptographic, but **logical**.

---

## 4. Attacker’s perspective

From an attacker's point of view:

- The authentication process was executed through different phases
- User verification was decided before 2FA validation
- The server trusted data that the client could change
- No limit was enforced on verification attempts

This combination allowed the authentication process to be abused.

---

## 5. Impact

A bad actor can easily gain total access to another’s user account without:

- User’s password
- User’s 2FA code

This completely breaks the purpose of 2FA.

---

## 6. Key lessons

- Authentication should be done on the server side.
- The client should never be able to change important parameters of authentication.
- Limit the number of times a user can try to access his account with wrong credentials.

---

## 7. Why this matters in real applications

Issues like this regularly appear in:

- Password reset mechanisms
- Account recovery features
- APIs relying on temporary verification states

The #1 OWASP 2021 vulnerability is again Broken Access Control. It shows that many real vulnerabilities don’t come from complex payloads, but from simple logic issues in how applications handle permissions.

---

## 8. Final takeaway

Security isn’t always about advanced exploits.
Sometimes it’s just about noticing where the application makes the wrong assumptions.
