# Authentication

Authentication is the process of verifying that a user is really who they claim to be. Because authentication sits at the boundary between anonymous users and protected functionality, mistakes in authentication logic can expose sensitive data, accounts and privileged actions.

Authentication vulnerabilities are often caused by weaknesses in login flows, password handling, multi-factor authentication, account recovery or the way an application distinguishes valid from invalid users.

## Authentication vs Authorization

**Authentication** answers: **Who are you?**

**Authorization** answers: **What are you allowed to do?**

A user can be correctly authenticated but still gain unauthorized access if the application performs weak authorization checks. Likewise, strong authorization is not useful if an attacker can bypass authentication and impersonate another user.

## Brute-Force Attacks

A brute-force attack repeatedly tries possible credentials until a valid combination is found. These attacks are commonly automated using wordlists rather than purely random guesses.

The effectiveness of brute forcing depends on factors such as:

- Predictable usernames
- Weak or reused passwords
- Lack of rate limiting
- Different application responses for valid and invalid usernames
- Missing account lockout or throttling controls
- Predictable password patterns

### Brute-Forcing Usernames

Usernames are often easier to predict than passwords. They may follow recognizable patterns such as email addresses, employee names or common privileged names such as `admin` or `administrator`.

During testing, useful places to look for usernames include:

- Public profile pages
- Blog posts and comments
- Error messages
- HTTP responses containing email addresses
- Naming conventions used by the organization

## Brute-Forcing Passwords

Password brute forcing attempts multiple candidate passwords against a known or suspected username.

Password policies can make guessing harder, but users often create predictable variations to satisfy complexity rules. For example, a user may transform a simple password into something like `Password1!` rather than choosing a truly random password.

Strong defenses include rate limiting, MFA, monitoring, and secure password policies that encourage long, unique passwords.

## Username Enumeration

Username enumeration occurs when an application behaves differently depending on whether a submitted username exists.

For example, these two responses reveal different information:

```text
Invalid username
Incorrect password
```

If an attacker receives **Incorrect password**, they now know that the username is valid. This reduces the search space because they can focus only on finding the password for that account.

Differences can appear in:

- Error messages
- HTTP status codes
- Response lengths
- Redirect behavior
- Response timing

### Lab

- [Username enumeration via different responses](./lab-username-enumeration-via-different-responses/)

## Bypassing Two-Factor Authentication

Two-factor authentication is intended to require a second proof of identity after the password. However, the protection can fail if the application treats the user as fully authenticated before the second step has been completed.

A vulnerable flow might look like:

```text
Correct username + password
        ↓
Session becomes authenticated
        ↓
2FA page is displayed
        ↓
Protected pages are already accessible
```

In this case, the 2FA page exists in the interface, but the server does not enforce completion of the second factor before allowing access to protected resources.

A secure implementation must track whether the second authentication factor has been successfully verified and enforce that state on every protected request.

### Lab

- [2FA simple bypass](./lab-2fa-simple-bypass/)

## General Defensive Principles

Authentication mechanisms should be implemented with several layers of protection:

- Use generic login error messages to reduce username enumeration.
- Apply rate limiting or progressive delays to repeated failed logins.
- Monitor suspicious authentication attempts.
- Require strong, unique passwords and support password managers.
- Store passwords using secure one-way password hashing such as Argon2id, bcrypt, or scrypt.
- Enforce MFA server-side rather than relying only on the presence of a verification page.
- Regenerate sessions after successful authentication.
- Never expose sensitive authentication state through client-controlled values.

