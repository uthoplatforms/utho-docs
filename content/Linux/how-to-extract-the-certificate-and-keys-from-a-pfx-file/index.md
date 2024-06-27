---
title: "How to extract the certificate and keys from a .pfx file"
date: "2023-07-18"
title_meta: "How to extract the certificate and keys from a .pfx file"
description: "How to extract the certificate and keys from a .pfx file"
keywords:  ['linux', '.pfx ', 'certificate']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-extract-the-certificate-and-keys-from-a-pfx-file']
---

INTRODUCTION

A Personal Information Exchange (. pfx) Files, is **password protected file certificate commonly used for code signing your application**. It derives from the [PKCS 12](https://en.wikipedia.org/wiki/PKCS_12) archive file format certificate, and it stores multiple cryptographic objects within a single file: X. 509 public key certificates. In this tutorial, we will learn how to extract the certificate and keys from a .pfx in Windows Servers.

Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- .pfx file

- Internet connectivity

Step 1. Open the command prompt and go to the folder that contains your .pfx file.

Step 2. Run the following command to extract the private key:

`openssl pkcs12 -in [yourfile.pfx] -nocerts -out [drlive.key]`

Step 3. Run the following command to extract the certificate:

`openssl pkcs12 -in [yourfile.pfx] -clcerts -nokeys -out [drlive.crt]`

Step 4. Run the following command to decrypt the private key:

`openssl rsa -in [drlive.key] -out [drlive-decrypted.key]`

Thank You!
