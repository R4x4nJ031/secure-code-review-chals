# Challenge 8 – PHP Insecure Deserialization / Privilege Escalation

This challenge demonstrates **Insecure Deserialization (CWE-502)** in PHP when
user-controlled serialized data is passed directly into `unserialize()`.

## Vulnerability
The form accepts serialized data from the user:

- `$_POST['data']`

That value is deserialized directly:

- `$user = @unserialize($data);`

The resulting object is then stored in the session and later trusted by the admin
check:

- `$_SESSION['user'] = $user;`
- `return $_SESSION['user']->role === 'admin';`

An attacker can forge a serialized `User` object with `role = admin` and gain
access to the admin page.

## Files
- vuln.php – Vulnerable PHP app (unserializes attacker-controlled input)
- safe.php – Fixed version (uses JSON input and assigns role server-side)
- exploit – Example payload generation using `serialize()`

## Key idea
Never call `unserialize()` on untrusted input. Prefer safer data formats like JSON,
validate the decoded structure, and keep sensitive fields such as roles under
server-side control.
