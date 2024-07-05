---
title: "How to enable RDP in Ubuntu OS (Tasksel)"
date: "2022-06-22"
keywords: ["enable RDP Ubuntu", "Ubuntu RDP setup", "Tasksel RDP", "remote desktop Ubuntu", "Ubuntu remote access", "RDP configuration Ubuntu", "Ubuntu Tasksel tutorial", "Linux remote desktop"]

title_meta: "Learn how to enable RDP (Remote Desktop Protocol) in Ubuntu using Tasksel with this detailed guide. Follow these steps to set up remote desktop access on your Ubuntu system."

---

![](images/How-to-enable-RDP-in-Ubuntu-OS-Tasksel-1024x576.png)

In this Article we will know the process to add XRDP service through Tasksel packages.

First we need to update the Ubuntu OS with help of below command- 
```
 # apt update 
```

After successfully updating the system, I need to install the tasksel packages on the system.

```
 # apt install tasksel 
```

Now need to deploy the tasksel in your system, you need to follow below screenshot steps.  
```
 # tasksel 
```

![](images/pasted-image-0-1-8.png)

![](images/pasted-image-0-28.png)

![](images/pasted-image-0-2-6.png)

After successfully install the ubuntu Desktop, you need to reboot the system.

```
 # reboot 
```

Now we have to install the XRDP in system to access the machine from the outside of world  
```
 # apt install xrdp 
```

Now we need to enable the xrdp service in our system  
```
 # systemctl enable xrdp 
```

:: After running the above command, you will get the below result. Now you are able to access your ubuntu machine from outside of the world through RDP access.

![](images/pasted-image-0-3-6.png)

Not recommended for all users...  

Some extra features which you may also run to give a look like Windows system to your Ubuntu Desktop.  
  
```
 # apt install gnome-shell-extensions gnome-shell-extension-dash-to-panel gnome-tweaks adwaita-icon-theme-full 
```

```
 # reboot 
```

Click on Activities, then search on search bar Tweaks

![](images/pasted-image-0-4-7.png)

Select Dash to panel and Desktop icons

![](images/pasted-image-0-5-6.png)
