# Access Control

Access control determines **who or what is allowed to perform an action or access a resource** in an application.

In web applications, access control depends on three closely related concepts:

- **Authentication** — verifies that a user is who they claim to be.
- **Session management** — associates later HTTP requests with the authenticated user.
- **Access control / authorization** — decides whether that user is allowed to perform the requested action or access the requested resource.

Broken access control is a common and potentially critical vulnerability because authorization logic is often distributed across many routes, parameters, roles, and application states.

---

## Vertical Privilege Escalation

Vertical privilege escalation happens when a lower-privileged user gains access to functionality intended for a more privileged user.

A typical example is a normal user reaching an administrator-only page and performing actions such as deleting accounts or changing roles.

### Unprotected Functionality

The simplest form of vertical privilege escalation occurs when sensitive functionality exists but the application does not properly enforce authorization checks on it.

For example, an application may expose an admin panel at a predictable path such as:

```text
/admin
```

The application may hide the link from ordinary users while still allowing direct access to the endpoint. Hiding a link in the interface is **not** an access control mechanism.

### Recon Tip: Check `robots.txt`

`robots.txt` is a file intended to tell search-engine crawlers which paths they should avoid indexing. It is **not** a security control.

It is commonly available at:

```text
/robots.txt
```

During reconnaissance, it is useful to check because developers sometimes list sensitive paths there, for example:

```text
Disallow: /administrator-panel
```

This can unintentionally disclose hidden administrative or internal functionality.

> Treat `robots.txt` as a source of hints, not as protection. Any path that must be restricted still needs server-side authorization checks.

### Lab

- [Lab: Unprotected admin functionality](./lab-unprotected-admin-functionality/)

---

## Security by Obscurity

Some applications try to protect sensitive functionality by giving it a difficult-to-guess URL, such as:

```text
/administrator-panel-yb556
```

This is known as **security by obscurity**. It may make discovery slightly harder, but it does not provide real access control.

Hidden paths can still leak through:

- JavaScript source files
- HTML source
- API responses
- application logs
- error messages
- client-side routing code

A common example is JavaScript that contains the admin URL but only displays the link when the current user is an administrator. The code itself is still sent to the browser, so any user can inspect it.

### Lab

- [Lab: Unprotected functionality - security by obscurity](./lab-unprotected-functionality-security-by-obscurity/)

---

## Parameter-Based Access Control Methods

Some applications store authorization information in values that the user can modify, for example:

- hidden form fields
- cookies
- query parameters

Examples might look like:

```text
?admin=true
```

```text
?role=1
```

or a cookie such as:

```text
Admin=false
```

If the server trusts these user-controlled values without independently verifying the user's privileges, an attacker may change them and gain unauthorized access.

Authorization decisions should be made using trusted server-side state, not values supplied by the client.

### Lab

- [Lab: User role controlled by request parameter](./lab-user-role-controlled-by-request-parameter/)

---

## Horizontal Privilege Escalation

Horizontal privilege escalation occurs when a user gains access to another user's resources while remaining at the same privilege level.

For example, a user might access their own account with:

```text
/myaccount?id=123
```

If changing the identifier to another user's value exposes that user's account, the application has a broken object-level authorization issue.

### IDOR

This pattern is commonly called an **Insecure Direct Object Reference (IDOR)** when user-controlled identifiers directly reference objects such as:

- user accounts
- invoices
- files
- API keys
- orders
- messages

For example:

```text
/account?id=123
```

Changing the identifier may expose another user's object if the server only checks whether the object exists and does not verify whether the current user is authorized to access it.

### Unpredictable IDs Do Not Fix Authorization

Applications sometimes use GUIDs or other unpredictable identifiers instead of sequential numbers. This can make IDs harder to guess, but it does **not** replace access control.

An attacker may still obtain another user's identifier from somewhere else in the application, such as:

- blog posts
- profile links
- comments
- messages
- API responses

The server must still verify ownership or permission for every requested object.

### Lab

- [Lab: User ID controlled by request parameter, with unpredictable user IDs](./lab-user-id-controlled-by-request-parameter-unpredictable-user-ids/)

---

## Horizontal to Vertical Privilege Escalation

A horizontal privilege escalation can sometimes be chained into a vertical privilege escalation.

For example, if an attacker can access another user's account page by modifying an identifier, they may target an administrator account. If that page exposes sensitive data such as a password, reset token, API key, or privileged functionality, the attacker can then take over the higher-privileged account.

This turns an object-level authorization issue into full privilege escalation.

### Lab

- [Lab: User ID controlled by request parameter with password disclosure](./lab-user-id-controlled-by-request-parameter-password-disclosure/)

---

## Testing Mindset

When testing access control, ask:

1. **What functionality should only certain roles be able to use?**
2. **What objects belong to specific users?**
3. **Can I access the same endpoint directly even if the UI hides it?**
4. **Can I change a role, cookie, ID, username, or other parameter?**
5. **Does the server independently verify authorization on every request?**
6. **Can information leaked elsewhere help me discover hidden endpoints or identifiers?**

A strong rule of thumb is:

> Never trust the absence of a button or link. Test the underlying HTTP request and server-side authorization directly.
