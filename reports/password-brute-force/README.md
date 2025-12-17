# Password brute-force via password change

**Topic:** Authentication / Access Control  
**Platform:** PortSwigger Web Security Academy  

---

## 1. What was the problem?

The application implemented an apparently legitimate password change mechanism.

The issue started when the application returned different responses depending on how the password fields were handled.

When I entered the **correct current password** and a **new password**, the application returned: `Password changed successfully!`.

If I entered a **wrong current password** but the **new password** and **confirm password** values were equal, the application simply **logged me out**.

However, when I entered a **wrong current password** and made the **new password** and **confirm password** fields different, the application returned: `New passwords do not match`.

---

## 2. Why is this a security issue?

Because the application returned different responses, it was possible to understand how the password change logic worked internally.

What I did:

- Using Burp Intruder, I changed the `username` parameter to Carlos
- I used the wordlist provided by the platform to brute-force the `current password` parameter
- For the `new password` field I used “qwerty” and for `confirm password` I used “qwert”, **so they would not match**
- I started the attack and analyzed the responses

**All 99 responses had a length of 4013, except one response with a length of 4010**.
When I checked that response, the message returned by the application was:
`New passwords do not match`.

**This indicated that the current password was correct**.

---

## 3. What went wrong?

The application validated the current password before validating whether the new password fields matched.

Because of this, it leaked information through different responses, allowing password enumeration.


---

## 4. Impact

An attacker could gain unauthorized access to another user’s account by discovering the correct password, without any additional protection.

---

## 5. What I would do to prevent this

- Return a generic error message for all failed password change attempts
- Apply rate limiting on password change functionality

---

## 6. Why this matters in real applications

This type of issue still exists in real world applications and can cause serious damage, especially when sensitive or personal information is involved.
