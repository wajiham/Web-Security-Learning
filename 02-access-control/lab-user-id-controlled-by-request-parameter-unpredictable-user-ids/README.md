# Lab: User ID Controlled by Request Parameter, with Unpredictable User IDs

## Objective

Access another user's account by obtaining their unpredictable identifier from elsewhere in the application and retrieve their API key.

## Vulnerability

The application uses unpredictable user IDs, but does not properly verify that the logged-in user is authorized to access the account identified by the `id` parameter.

## Analysis

This lab demonstrates a broken access control condition where the application relies on weak or missing authorization checks. The goal is to identify the trust boundary, manipulate the relevant request or identifier, and confirm that the server permits an action that should be restricted.

## Exploitation Steps

1. Find a blog post written by `carlos`.
2. Click the author's name or profile link.
3. Observe that the URL contains Carlos's user ID and save that value.
4. Log in with the supplied credentials.
5. Open your account page.
6. Replace your own `id` parameter with the saved ID belonging to Carlos.
7. Load the modified URL.
8. Retrieve Carlos's API key.
9. Submit the API key to solve the lab.

## Why It Works

The application assumes that an unpredictable identifier is enough to protect the object. Once the attacker learns another user's ID, the server fails to verify ownership before returning that user's data.

## Security Impact

An attacker can access sensitive information belonging to other users, including credentials, API keys, personal data, or account functionality.

## Remediation

Perform object-level authorization checks on every request. The server should verify that the authenticated user is permitted to access the requested object regardless of how difficult the identifier is to guess.

## Key Takeaway

Unpredictable IDs reduce guessability, but they do not fix IDOR. Authorization must still be enforced on the server.

## Screenshots

Add your screenshots here after uploading them to the `images/` folder.

```markdown
![Example screenshot](./images/example.png)
```
