# Lab: User Role Controlled by Request Parameter

## Objective

Escalate privileges by modifying a client-controlled role value and then access the admin panel to delete `carlos`.

## Vulnerability

The application stores the user's authorization state in a client-controlled cookie and trusts that value when deciding whether the user is an administrator.

## Analysis

This lab demonstrates a broken access control condition where the application relies on weak or missing authorization checks. The goal is to identify the trust boundary, manipulate the relevant request or identifier, and confirm that the server permits an action that should be restricted.

## Exploitation Steps

1. Browse to `/admin` and confirm that the admin panel is not accessible.
2. Go to the login page.
3. In Burp Proxy, turn interception on and enable response interception.
4. Submit the login form and forward the request.
5. Inspect the login response and find the cookie `Admin=false`.
6. Modify the response so the cookie becomes `Admin=true`.
7. Forward the response to the browser.
8. Browse to the admin panel.
9. Delete the user `carlos`.

## Why It Works

The server accepts a user-controlled authorization flag as trusted state. Because the attacker can modify the cookie, they can grant themselves administrative privileges.

## Security Impact

An attacker can escalate from a normal account to an administrator account and perform privileged actions.

## Remediation

Store role and privilege information in trusted server-side session state. Never trust a client-provided cookie, hidden field, or query parameter as proof of authorization.

## Key Takeaway

Any authorization decision based on a value the user can modify should immediately be treated as suspicious.

## Screenshots

Add your screenshots here after uploading them to the `images/` folder.

```markdown
![Example screenshot](./images/example.png)
```
