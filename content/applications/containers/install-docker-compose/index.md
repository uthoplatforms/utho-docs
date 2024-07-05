---
slug: "Docker Compose Unleashed: Simplify Your Setup"
description: "Streamline Your Setup: A Quick Guide to Docker Compose Installation"
keywords: ["docker", "docker compose", "container"]
tags: ["container", "docker"]
modified: 2024-04-08
modified_by:
title: "How to Install Docker Compose"
published: 2024-04-08
headless: true
authors: ["Pawan Kumar"]
---

<!--- Installation instructions for Docker Compose -->

Docker Compose comes in two versions: plugin and standalone. Docker's official documentation emphasizes the plugin version. Installing the plugin is simple and seamlessly integrates with previous Docker Compose commands.

Follow these steps to install the Docker Compose plugin. If you prefer the standalone application, refer to Docker's [official installation guide](https://docs.docker.com/compose/install/other/#on-linux).

Note: Many tutorials still use the standalone command format, like this:

```command
docker-compose [command]
```

Remember to switch to the plugin command format when using this installation method. Usually, this just involves replacing the hyphen with a space, like so:

```command
docker compose [command]
```

1. To enable the Docker repository for your system's package manager, you usually need to install the Docker engine, which automatically enables the repository. Refer to our installation guide for Docker to ensure the repository is enabled on your system.

1. After enabling the repository, update your package manager and install the Docker Compose plugin using the following commands:

- For **Debian** and **Ubuntu** systems:

    ```command
    sudo apt update
    sudo apt install docker-compose-plugin
    ```

- For **CentOS**, **Fedora**, and other RPM-based distributions:

    ```command
    sudo yum update
    sudo yum install docker-compose-plugin
    ```
