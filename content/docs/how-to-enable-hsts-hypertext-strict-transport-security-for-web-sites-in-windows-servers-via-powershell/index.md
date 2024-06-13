---
title: "How to enable HSTS (Hypertext Strict Transport Security) for Web sites in Windows Servers via PowerShell"
date: "2023-06-13"
---

![](images/How-to-enable-HSTS-Hypertext-Strict-Transport-Security-for-Web-sites-in-Windows-Servers-via-PowerShell-1024x576.jpg)

## INTRODUCTION

**[HTTP Strict Transport Security](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)** (**HSTS**) is a policy mechanism that helps to protect websites against man-in-the-middle attacks such as protocol downgrade attacks and cookie hijacking. It allows web servers to declare that web browsers (or other complying user agents) should automatically interact with it using only HTTPS connections, which provide Transport Layer Security (TLS/SSL), unlike the insecure HTTP used alone. HSTS is an IETF standards track protocol. In this tutorial, we will learn, how to enable HSTS (Hypertext Strict Transport Security) for Web sites in Windows Servers via PowerShell.

#### Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![](images/Screenshot_11-22.png)

**Step 3. Run the following command to _get site collection_**

```
$sitesCollection = Get-IISConfigSection -SectionPath "system.applicationHost/sites" | Get-IISConfigCollection
```

**Step 4. Run the following command to _get website you'd like to set HSTS_**

Specify the name of the site for "name"="\*\*\*"

```
$siteElement = Get-IISConfigCollectionElement -ConfigCollection $sitesCollection -ConfigAttribute @{"name"="yourdomain.com"}
```

**Step 5. Run the following command to _get setting of HSTS for target site_**

```
$hstsElement = Get-IISConfigElement -ConfigElement $siteElement -ChildElementName "hsts" 
```

**Step 6. Run the following command to _enable HSTS for target site_**

```
Set-IISConfigAttributeValue -ConfigElement $hstsElement -AttributeName "enabled" -AttributeValue $true 
```

set \[max-age\] of HSTS as 31536000 sec (365 days)

set \[max-age\], refer to https://hstspreload.org/

```
Set-IISConfigAttributeValue -ConfigElement $hstsElement -AttributeName "max-age" -AttributeValue 31536000 
```

**Step 7. Run the following command to _set \[includeSubDomains\] of HSTS as enabled_**

```
Set-IISConfigAttributeValue -ConfigElement $hstsElement -AttributeName "includeSubDomains" -AttributeValue $true
```

_NOTE: this option applies to all sub-domains_

**Step 8. Run the following command to set _\[redirectHttpToHttps\] of HSTS as enabled_**

```
Set-IISConfigAttributeValue -ConfigElement $hstsElement -AttributeName "redirectHttpToHttps" -AttributeValue $true
```

Thank You!
