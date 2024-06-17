---
linktitle: "NGINX - TLS/HTTPS"
title: "NGINX : Enable TLS or HTTPS Connections"
date: "2020-06-04"
---
![](images/NGINX-_-Enable-TLS-or-HTTPS-Connections_utho.jpg)

The Secure Socket Layer (SSL) is the successor to Transportation Layer Security (TLS). It provides stronger and more powerful HTPS and includes non-SSL improvements such as Forward Confidentiality, modern OpenSSL cipher suites, and HSTS-compatibility.

A single NGINX installation may host a number of websites and any number that use the same TLS certificate and key. This guide describes different scenarios for adding a TLS certificate to the NGINX setup of your domain.

## Before You Begin

- You need root user access`sudo` privileged user account.
- A TLS certificate and key are required for your site. If you are a private or internal location, or are simply experimenting, the certificate can be signed by yourself. If this is what your website wants, you can can use a commercial certificate chain. See our instructions to build a self-signed certificate or a signing certificate submission, if you don't already have a certificate and server key.
- Make sure the NGINX has been compiled with - http ssl module if you have compiled from source code. Check for nginx -V efficiency.

## Credentials Storage Location

No official or favorite place to store the TLS certificate and the key on your site is safe. The certificate is sent to each server computer, and it is not a private file. The secret is private, however.

You want them to remain untouched and safe against other device users, wherever you want to store your certificate / key pair. We're going to save them as an example in `/root/certs/` , but you can save that folder whatever place you want.

1\. Create storage folder:

```
mkdir /root/certs/abc.com/
```

2\. Shift into that folder your certificate(s) and key(s).

3\. Limit the following file permissions:

```
chmod 400 /root/certs/abc.com/abc.com.key
```

## Configure the http Block

Guidelines to extend NGINX to all web domains, like SSL / TLS instructions, would go to the `http` block in `nginx.conf`, The following instructions presume the same certificate and key for a particular website or all pages on the list.

```file {title="/etc/nginx/nginx.conf" lang="aconf"}
http { ssl_certificate /root/certs/abc.com/abc.com.crt; ssl_certificate_key /root/certs/abc.com/abc.com.key; ssl_ciphers EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH; ssl_protocols TLSv1.TLSv1.2;
```

## Configure a Single HTTPS Site

Scenarios: You have a domain certificate, and you would like NGINX to run over HTTPS on a single website. Scenario:

Just use the setup of the http block in the previous section with only one site to deal with. You should not include ssl \* instructions in the website configuration file in this case. Nonetheless, you will tell NGINX that port 443 for HTTPS connections should be listening instead of port 80. For further information, see the SSL module for NGINX docs.

1\. For example, below is a simple site configuration that works with the above-mentioned http block. This server block provides your site with IPv4 and IPv6 support, but you have no HTTP support only over HTTPS. In order to access your website, you need to type https:/ in the browser.

It is just a first step; without HSTS or by redirecting HTTP requests to port 443 you probably would not like to use this configuration.

```file {title="/etc/nginx/conf.d/abc.com.conf" lang="aconf"}
server { listen ssl default_server; listen [::]:ssl default_server ; server_name abc.com www.abc.com; root /var/www/abc.com; }
```

2\. After changes to the NGINX config files, reload your setup:

```
nginx -s reload
```

3\. Go to the web browser of your site or IP of cloud server to ensure that https:// is defined in the URL. Your HTTPS website will load. The browser warns you of an insecure link if you have a self-signed certificate. Activate and link the advertising.

## Configure Multiple Sites with a Single Certificate

Scenario: You have a multi-domain certificate, such as a Wildcard certificate or a SubjectAltName certificate.

In this case, the HTTP Server configure instructions are the same in the http domain. Two separate `/etc/nginx/conf.d/`, configuration files are needed, one for each site which is secured by credentials. In it, the IP address of each site with the `listen`  directive needs to be specified. If you have two different websites with different IPs, you don't want to use `default_server` .

The sites `abc1.com`, `abc2.com`, and `/root/certs/abc.com/`  are provided by the same certificate and key.

```file {title="/etc/nginx/conf.d/abc.com1.conf" lang="aconf"}
server { listen 203.0.113.30:ssl; listen [2001:DB8::5]:ssl; server_name abc1.com www.abc1.com; root /var/www/abc1.com; }
```

```file {title="/etc/nginx/conf.d/abc.com2.conf" lang="aconf"}
server { listen 203.0.113.30:ssl; listen [2001:DB8::5]:ssl; server_name abc2.com www.abc2.com; root /var/www/abc1.com; }
```

2\. Reload the configurations:

```
nginx -s reload
```

3\. HTTPS should now be accessible for both sites. You can see that the cert that supports both sites uses your browser to inform certificate property.

## Configure Multiple Sites with Different SSL Certificates

Scenario: You have two, or more, totally independent TLS certificate/key pairs websites that you want to serve on.

1\. Make sure that your storage certificate is well ordered. An example is given below:

```
/root/certs/ ├── abc1.com/ │ ├── abc1.com.crt │ └── abc1.com.key └── abc2.com/ ├── abc2.com.crt └── abc2.com.key
```

2\. Configure your `nginx.conf` http block as stated above but without the certificate and key locations. Instead, these are in the `server` block of the individual website because the positions on each site vary. The result should be:

```file {title="/etc/nginx/nginx.conf" lang="aconf"}
http { ssl_ciphers EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH; ssl_protocols TLSvTLSv1.TLSv1.2; ssl_session_cache shared:SSL:10m; ssl_session_timeout 10m; }
```

3\. Add to each server block the `ssl_certificate` and `ssl_certificate_key`  directive with the proper path of the certificate and key file at each domain .

```file {title="/etc/nginx/conf.d/abc1.com.conf" lang="aconf"}
server { listen 203.0.113.55:ssl; listen [2001:DB8::7]:ssl; server_name example1.com www.abc1.com; root /var/www/abc1.com;

```
ssl_certificate     /root/certs/abc.com/abc1.com.crt;
ssl_certificate_key /root/certs/abc.com/abc1.com.key;
}
```
```

```file {title="/etc/nginx/conf.d/abc2.com.conf" lang="aconf"}
server { listen 203.0.113.65:ssl; listen [2001:DB8::8]:ssl; server_name example2.com www.abc2.com; root /var/www/abc2.com;

```
ssl_certificate     /root/certs/abc2.com/abc.com.crt;
ssl_certificate_key /root/certs/abc2.com/abc.com.key;
}
```
```

4\. Reload your configuration:

```
nginx -s reload
```

5\. Both sites should be HTTPS-accessible, but by inspecting the certificates using your browser, `abc1.com` uses `abc1.com.crt` and `abc2.com` uses`abc2.com.crt`.

Thankyou
