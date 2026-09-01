# Lab: Unprotected Functionality - Security by Obscurity

## Objective

Discover a hidden administrator endpoint from client-side code and use it to delete the user `carlos`.

## Vulnerability

The application tries to protect sensitive functionality by using an unpredictable URL, but leaks that URL in client-side JavaScript.

## Analysis

This lab demonstrates a broken access control condition where the application relies on weak or missing authorization checks. The goal is to identify the trust boundary, manipulate the relevant request or identifier, and confirm that the server permits an action that should be restricted.

## Exploitation Steps

1. Open the lab home page.
2. Review the page source using the browser developer tools or inspect the response in Burp Suite.
3. Look for JavaScript that references administrator functionality.
4. Identify the hidden admin panel URL disclosed in the script.
5. Browse directly to that path.
6. Delete the user `carlos`.

## Why It Works

The application relies on the secrecy of the URL instead of enforcing authorization. Client-side code is visible to the user, so any hidden endpoint embedded in JavaScript can be discovered.

## Security Impact

Attackers can discover and access privileged functionality even when the URL is difficult to guess, potentially leading to administrative compromise.

## Remediation

Enforce authorization on the server for every privileged route. Treat client-side code and route names as public information.

## Key Takeaway

Security by obscurity may slow discovery, but it is never a substitute for server-side access control.

## Screenshots

Add your screenshots here after uploading them to the `images/` folder.

```markdown
![Example screenshot](./images/example.png)
```
