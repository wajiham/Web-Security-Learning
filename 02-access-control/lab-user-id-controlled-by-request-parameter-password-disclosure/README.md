# Lab: User ID Controlled by Request Parameter with Password Disclosure

## Objective

Use a horizontal access control flaw to obtain the administrator's password, then escalate vertically and delete `carlos`.

## Vulnerability

The account page is selected using a user-controlled identifier and exposes sensitive data without verifying that the requester is allowed to view that account.

## Analysis

This lab demonstrates a broken access control condition where the application relies on weak or missing authorization checks. The goal is to identify the trust boundary, manipulate the relevant request or identifier, and confirm that the server permits an action that should be restricted.

## Exploitation Steps

1. Log in using the supplied credentials.
2. Open the account page.
3. Change the `id` parameter in the URL to `administrator`.
4. Inspect the response in Burp Suite.
5. Locate the administrator's password in the returned page or response data.
6. Log out of the normal account.
7. Log in using the administrator credentials.
8. Open the admin panel.
9. Delete the user `carlos`.

## Why It Works

The application allows one user to retrieve another user's account data by changing an identifier. Because the administrator account exposes a password, the horizontal privilege escalation can be chained into vertical privilege escalation.

## Security Impact

An attacker can compromise privileged accounts, gain administrative access, and perform high-impact actions across the application.

## Remediation

Apply strict object-level authorization checks, never expose passwords or secrets in page responses, and ensure privileged functionality requires independent server-side role verification.

## Key Takeaway

A horizontal access control issue can become much more severe when the targeted account has higher privileges or exposes sensitive credentials.

## Screenshots

Add your screenshots here after uploading them to the `images/` folder.

```markdown
![Example screenshot](./images/example.png)
```
