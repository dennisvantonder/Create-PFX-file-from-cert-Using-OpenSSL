# 🔐 Creating a PFX File from .CRT/.PEM + .KEY + .P7B (Windows + OpenSSL)

This guide explains how to create a `.pfx` file for IIS using:
- Certificate (`.crt` or `.pem`)
- Private Key (`.key`)
- Intermediate Chain (`.p7b`)

---

## 📦 Prerequisites

You must have:
- OpenSSL installed on Windows
- Access to Command Prompt or PowerShell
- Files:
  - `certificate.pem` or `.crt`
  - `private.key`
  - `gd-g2_iis_intermediates.p7b` (optional but recommended)

---

## Installing OpenSSL

- Go to the following link: [Download OpenSSL](https://slproweb.com/products/Win32OpenSSL.html)
- Install Win64 OpenSSL Light
- Choose "The OpenSSL binaries (/bin)" option
- Location C:\OpenSSL-Win64 or in program files
- During install Select "Copy OpenSSL DLLs to Windows system Directory" -> YES
- Add OpenSSL to Environment varibales (important)
    - Open Start and Search for `Environment Variables`
    - Click `Environment variables`
    - Under system Variables find `Path` -> EDIT
    - Add `C:\OpenSSL-Win64\bin`
    - click Ok and close everything
- verify installation with command `openssl version`

## 📍 Step 1 — Verify OpenSSL works

Run:

```cmd
openssl version
```

Else use

```cmd
.\openssl version
```

if that fails change directory to openssl installation location

## 📍 Step 2 — Extract intermediate certificates from P7B (optional but recommended)

If the Intermediate Chain file exist follow this step else it can be skipped

### Important File Paths with spaces must be in quotes

```cmd
openssl pkcs7 -print_certs -in "C:\Users\Denni\Downloads\Farmwise SSL Certs\gd-g2_iis_intermediates.p7b" -out "C:\Temp\chain.pem"
```

Output:

```
C:\Temp\chain.pem
```

## 📍 Step 3 — Verify your certificate and key match (IMPORTANT)

```cmd
openssl x509 -noout -modulus -in "C:\path\certificate.pem" | openssl md5
openssl rsa -noout -modulus -in "C:\path\private.key" | openssl md5
```

✔ The MD5 outputs MUST be identical.

## 📍 Step 4 — Create PFX file

Run the following command

```cmd
openssl pkcs12 -export -out "C:\Temp\mycert.pfx" -inkey "C:\path\private.key" -in "C:\path\certificate.pem" -certfile "C:\Temp\chain.pem"
```

## 📍 Step 5 — Set Export Password

When Promted for password choose a password, you will start to type but nothing will appear, just continue.

## 📍 Step 6 — Output file will be generated

Your pfx file will be saved to the -out location 'C:\Temp\mycert.pfx'

## 📍 Step 7 — Import to IIS

- Open IIS
- Go to server certificates
- Click import
- Choose pfx file
- Enter password 
- Click OK

## Common errors and fixes

❌ `Extra option: SSL`
- cause: missing quotes in path
- fix: wrap paths in quotes ""

❌ `unable to load private key`
- cause: wrong key file or corrupted file
- fix: ensure '.key' file matches the certificate (step 3)

❌ `uno certificate matches private key`
- cause: certificate and key are not a pair
- fix: ensure '.key' file matches the certificate (step 3)
