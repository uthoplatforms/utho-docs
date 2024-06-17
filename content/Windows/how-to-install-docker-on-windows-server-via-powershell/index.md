---
title: "How to install Docker on Windows Server via PowerShell"
date: "2023-06-13"
---

![](images/Install-Docker-on-Windows-Server-via-PowerShell-1024x576.jpg)

### INTRODUCTION induct Docker on Windows Server

[Docker](https://www.docker.com/) is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. The service has both free and premium tiers. The software that hosts the containers is called Docker Engine. It was first started in 2013 and is developed by Docker, Inc. In this tutorial, we will learn how to install Docker in a Windows Server via PowerShell.

#### Prerequisites install Docker on Windows Server

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![install Docker on Windows Server](images/Screenshot_11-17.png)

Step 3. Run the following command to install Containers feature

```
Enable-WindowsOptionalFeature -Online -FeatureName Containers
```

![](images/Screenshot_1-39-1024x327.png)

![install Docker on Windows Server](images/Screenshot_2-49-1024x212.png)

**Step 4. Run the following command to restart the server after installing Containers**

```
Restart-Computer -Force
```

![](images/Screenshot_3-37.png)

**Step 5. Run the following command to install Docker Repo**

```
Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
```

![install Docker on Windows Server](images/Screenshot_9-23.png)

![](images/Screenshot_10-15-1024x306.png)

**Step 6. Run the following command to install Docker**

```
Install-Package -Name docker -ProviderName DockerMsftProvider
```

![install Docker on Windows Server](images/Screenshot_11-16-1024x236.png)

![](images/Screenshot_12-17-1024x224.png)

![install Docker on Windows Server](images/Screenshot_13-13-1024x316.png)

**Step 7. Run the following command to restart the server after installing Docker**

```
Restart-Computer -Force
```

![](images/Screenshot_3-38.png)

**Step 8. Run the following command to check Docker version**

```
docker version
```

![install Docker on Windows Server](images/Screenshot_14-12-1024x325.png)

Thank You!
