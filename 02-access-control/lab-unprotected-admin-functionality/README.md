# Lab: Unprotected Admin Functionality

## Objective

Access an admin-only function that is exposed without proper authorization and use it to delete the user `carlos`.

## Vulnerability

The application exposes sensitive administrative functionality at a discoverable path and does not enforce server-side authorization before allowing access.

## Analysis

This lab demonstrates a broken access control condition where the application relies on weak/missing authorization checks. The goal is to identify the trust boundary, manipulate the relevant request and confirm that the server permits an action that should be restricted.

## Exploitation Steps

1. Open the lab.
2. Browse to `/robots.txt` by appending it to the lab URL.
3. Inspect the `Disallow` entry and identify the disclosed administrator path.
4. Replace `/robots.txt` in the URL with the disclosed admin path, for example `/administrator-panel`.
5. Open the admin panel.
6. Delete the user `carlos`.

## Why It Works

The application assumes that hiding the admin link is enough. Because the endpoint itself does not verify that the requester is an administrator, any user who discovers the URL can access it directly.

## Security Impact

An attacker can reach privileged administrative functionality, potentially allowing account deletion, data modification, user management or complete application compromise.

## Remediation

Protect every privileged endpoint with server-side authorization checks. Do not rely on hidden links, uncommon paths or `robots.txt` for protection.

## Key Takeaway

Always test sensitive endpoints directly. If an endpoint is accessible without an authorization check, the application is vulnerable even if the UI never exposes the link.

## Screenshots

Add your screenshots here after uploading them to the `images/` folder.

```markdown
![Example screenshot](./images/example.png)
```
