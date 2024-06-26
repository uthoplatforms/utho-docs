---
title: "How to install SSL through Cpanel ."
date: "2022-01-09"
---

![](images/How-to-install-SSL-through-Cpanel_utho.jpg)

SSL (Secured Sockets Layer) is used to establish  authenticated and encrypted links between network Computers  
Now for  Installing SSL is Server through Cpanel First of allLogin  in cpanel by  SERVER\_IP:2083    
Now login in cpanel and under the Security option select  SSL/TLS option

![](images/image-7-2.png)

In the right side of window click on CSR option

![](images/image-8-1.png)

After that fill required details and click on Generate option

![](images/image-9-1.png)

Now share this file with SSL team they will share one file.

![](images/image-10-1.png)

Open the file manager option in cpanel , Now Paste that file under this directory

publc\_html/.well-known/pki-validation/

Note:- If hidden files are Not showing Please follw this step

click on settings option then click on Show hidden files

![](images/image-11-1.png)

Now Again Open SSL/TLS option under security and select CERTIFICATES(CRT) option

![](images/image-13-1.png)

Now Click on choose file option then upload the Certificate and then click on save Certificate.

![](images/image-14-1.png)

![](images/image-15-1.png)

SSL is installed on server , Changes will reflect on server with In 2 hrs

Thank you :)
