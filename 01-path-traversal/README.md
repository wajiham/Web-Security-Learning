# Path Traversal

## What is Path Traversal?

Path traversal, also called **directory traversal**, happens when an application uses user-controlled input as part of a filesystem path without properly restricting it.

This can allow an attacker to access files outside the directory the application intended to expose.

Example:

```http
GET /loadImage?filename=218.png
```

If images are stored in:

```text
/var/www/images/
```

then the intended path is:

```text
/var/www/images/218.png
```

The sequence:

```text
../
```

means **move one directory up**.

So input such as:

```text
../../../etc/passwd
```

may cause the application to resolve a path outside the image folder.

---

## Common Obstacles and Bypass Techniques

Applications often try to block path traversal. Weak or incomplete validation can sometimes be bypassed.

### 1. Absolute Path Bypass

A developer may block `../` sequences but still allow an absolute path.

Example:

```text
/etc/passwd
```

**Why it works:** the application blocks traversal sequences but does not verify that the final path stays inside the intended directory.

**Lesson:** blocking `../` alone is not enough.

---

### 2. Nested Traversal Sequences

Some applications remove `../` only once.

An attacker may use a nested sequence such as:

```text
....//
```

If the inner traversal sequence is stripped, the remaining characters can become:

```text
../
```

Example:

```text
....//....//etc/passwd
```

**Why it works:** sanitization happens only once and the result is not checked again.

**Lesson:** removing dangerous strings is fragile.

---

### 3. URL Encoding and Double Encoding

The literal traversal sequence:

```text
../
```

can be URL encoded as:

```text
%2e%2e%2f
```

or double encoded as:

```text
%252e%252e%252f

decoded as:
%252e → %2e → .
%252e → %2e → .
%252f → %2f → /
```

**Why it can work:** different parts of the application may decode input at different stages.

A security check may inspect the encoded version, while the application later decodes it into a traversal sequence.

**Lesson:** validation should happen after decoding and normalization.

---

### 4. Required Base Directory Bypass

An application may require a path to start with:

```text
/var/www/images
```

An attacker may include that prefix and then traverse out:

```text
/var/www/images/../../../etc/passwd
```

**Why it works:** checking the beginning of a string is not the same as checking where the final path resolves.

**Lesson:** verify the canonical path, not just the path prefix.

---

### 5. Required File Extension and Null Byte Bypass

Some applications require filenames to end in an extension such as:

```text
.png
```

In some older or specific environments, a null byte could be used to terminate the filename before the extension:

```text
../../../etc/passwd%00.png
```

**Why it may work:** validation sees `.png`, but lower-level file handling may treat the filename as ending at the null byte.

**Important:** this is mainly relevant to older or specific technologies and is not universally effective in modern systems.

**Lesson:** extension checks alone are not sufficient protection.

---

## What These Bypasses Have in Common

The important idea is not to memorize every payload.

The common weakness is:

> The application validates the raw user input but does not securely verify the final filesystem path that will actually be accessed.

A safer flow is:

```text
User input
    ↓
Decode and normalize
    ↓
Resolve canonical path
    ↓
Verify path is inside allowed directory
    ↓
Access file
```

---

## Security Impact

Successful path traversal may expose:

- Application source code
- Configuration files
- Credentials or secrets stored in files
- Operating-system files
- Other sensitive server data

The impact depends on the permissions of the web application.

---

## Prevention

Developers should:

- Avoid using user-controlled input directly as filesystem paths
 ``` Bad:
  filename = request.args["filename"]
  open("/var/www/images/" + filename)
```
 ``` Safer:
  file_id = request.args["id"]
  filename = lookup_filename(file_id)
  open("/var/www/images/" + filename)
  The user supplies an ID, not a raw path. ```

- Prefer internal file IDs or allowlists
  Instead of:
  /download?filename=report.pdf
  use:
  /download?id=42

  Server-side:
  files = {
    "42": "report.pdf",
    "43": "invoice.pdf"
  }
  Only known files can be selected.

  An allowlist could also be:
  allowed = ["logo.png", "banner.png", "avatar.png"]
  if filename not in allowed:
  reject()
  
- Decode and normalize input before validation
- Resolve the final canonical path
- Verify the resolved path stays inside the intended directory
- Use least-privilege filesystem permissions

Below is an example of some simple Java code to validate the canonical path of a file based on user input:

File file = new File(BASE_DIRECTORY, userInput);
if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // process file
}

---

## Key Takeaway

> Path traversal defenses fail when they only look for specific strings such as `../`. Security checks should validate the final resolved path and ensure it remains inside the intended directory.

## Reference

- PortSwigger Web Security Academy — Path Traversal
