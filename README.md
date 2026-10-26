# 🛡️ Path Traversal — PortSwigger Web Security Academy Writeups

[![PortSwigger Academy](https://img.shields.io/badge/PortSwigger-Web%20Security%20Academy-orange)](https://portswigger.net/web-security/file-path-traversal)
[![Status](https://img.shields.io/badge/Labs-6%2F6%20Solved-success)]()
[![Topic](https://img.shields.io/badge/Vulnerability-Path%20Traversal-red)]()

Practical writeups for every lab in PortSwigger's **Path Traversal** module, documenting the vulnerability, the exploitation steps, the exact payloads used, and the underlying mitigation for each case — from the simplest bypass to more advanced filter-evasion techniques.

## 🎯 Objective

This repo is a hands-on record of solving all 6 labs in the [PortSwigger Path Traversal topic](https://portswigger.net/web-security/file-path-traversal), built as part of my practical training in web application penetration testing.

## 🧠 What is Path Traversal?

Path Traversal (also known as directory traversal) is a vulnerability that allows an attacker to read arbitrary files on the server hosting an application, by manipulating file paths supplied by the application (e.g. via `../` sequences) to access files outside the intended directory.

## 🛠️ Tools Used

- **Burp Suite** (Community/Pro) — intercepting and modifying requests
- **Browser DevTools** — inspecting application behavior
- PortSwigger's own lab environment

## ✅ All 6 Labs Solved

| Lab | Payload | Explanation |
|-----|---------|-------------|
| [1) Simple case](labs/01-simple-case) | `../../../etc/passwd` | No filters in place, allowing direct traversal from the images directory to the root. |
| [2) Traversal sequences blocked with absolute path bypass](labs/02-absolute-path-bypass) | `/etc/passwd` | The application blocks `../`, but doesn't validate absolute paths, so we can reference `/etc/passwd` directly. |
| [3) Traversal sequences stripped non-recursively](labs/03-sequences-stripped-non-recursively) | `....//....//....//etc/passwd` | The filter only removes `../` once. By using `....//`, stripping leaves behind `../`, allowing traversal. |
| [4) Traversal sequences stripped with superfluous URL-decode](labs/04-superfluous-url-decode) | `..%252f..%252f..%252fetc/passwd` | Double URL encoded. The first decode turns `%252f` into `%2f`, and the second turns it into `/`, reconstructing `../`. |
| [5) Validation of start of path](labs/05-validate-start-of-path) | `/var/www/images/../../../etc/passwd` | The app requires the path to start with `/var/www/images/`. We prepend it, then traverse up past it with `../../../` to reach root. |
| [6) Validation of file extension with null byte bypass](labs/06-null-byte-bypass) | `../../../etc/passwd%00.jpg` | The app forces a `.jpg` extension. A null byte (`%00`) terminates the string in vulnerable parsers, causing everything after it (including `.jpg`) to be ignored. |

Each lab folder contains a detailed `README.md` with objective, steps, payload, proof of concept, and mitigation, plus a `screenshots/` folder for the Burp Suite evidence.

## 🧯 Mitigation (General)

Across all 6 labs, the root cause is the same: **user-controlled input is used to build a file path without proper validation.** The reliable fix is:

1. **Whitelist** allowed filenames/extensions instead of trying to blacklist bad patterns
2. **Canonicalize** the path (e.g. `realpath()`) and verify it still falls inside the intended directory
3. Never trust single-pass sanitization — filters that strip `../` once can be bypassed with nested or encoded sequences
4. Avoid relying on filename extension or prefix checks alone, since both can be bypassed (null bytes, path tricks)
5. Validate input **after** all decoding/normalization is complete, never before

## 👤 Author

**Mohamed Ghonim**
Cybersecurity Trainee — Penetration Testing Track
[LinkedIn](https://linkedin.com/in/mohamed-g7onim)

## 🔗 Related

- Full article on Medium: *(link coming soon)*
- [PortSwigger Path Traversal Academy Topic](https://portswigger.net/web-security/file-path-traversal)
