---
slug: "Podman: Your Pathway to Portable Perfection"
description: "Podman has risen as a compelling alternative to Docker for deploying and managing containers. Podman stands out for its daemonless architecture, which gives it true rootless containers and heightened security. In this tutorial, find out all you need to get started installing and using Pdoman for running containers."
keywords: ['what is podman','podman docker','podman tutorial']
published: 2024-05-06
modified_by:
  name: Pawan Kumar
title: "Install Podman for Running Containers"
title_meta: "How to Install Podman for Running Containers"
authors: ["Pawan Kumar"]
tags: ["saas"]
---
Podman, an open-source containerization tool, offers a robust alternative to Docker. Much like Docker, Podman facilitates container creation, execution, and administration. However, Podman distinguishes itself by employing a secure, daemonless approach, enabling container operation in rootless mode.

Curious about how Podman stacks up against Docker? Explore our comprehensive guide, [Podman vs Docker](/docs/guides/podman-vs-docker/), which delves into the nuances of both tools, providing a thorough comparison.

This tutorial is your gateway to mastering Podman on your Linux system. From installation to execution, you'll acquire the skills needed to efficiently run and manage containers with Podman.

## Before You Begin

Utilize `sudo` for enhanced security measures. Follow the sections detailed in our How to Secure Your Server guide to establish a standard user account, reinforce SSH access, and eliminate any redundant network services.

Ensure your system is up to date.

    -   **Debian or Ubuntu:**

            sudo apt update && sudo apt upgrade

    -   **AlmaLinux, CentOS Stream, Fedora, or Rocky Linux:**

            sudo dnf upgrade

Note: This guide assumes the use of a non-root user. Commands necessitating elevated permissions are preceded by sudo. If you're unfamiliar with the sudo command, refer to our documentation for guidance.

## How to Install Podman

Podman can be accessed via the default package managers on the majority of Linux distributions.

    -   **AlmaLinux, CentOS Stream, Fedora, or Rocky Linux:**

            sudo dnf install podman

    -   **Debian or Ubuntu:**

            sudo apt install podman

Note: For Debian 11 or Ubuntu 20.10 and later versions, Podman is exclusively accessible through the APT package manager.

Following installation, confirm the success of your Podman setup by examining the installed version:

        podman -v

The displayed output might differ from the example provided; however, your objective is to ensure the successful installation of Podman.

         {{< output >}}
         podman version 4.1.1
         {{< /output >}}

### Configuring Podman for Rootless Usage

Podman typically operates with root privileges, necessitating the use of the `sudo` prefix for commands. However, it also offers a rootless mode, which is particularly attractive for securely delegating container actions to limited users.

While Docker permits commands to be executed by limited users, its daemon still operates as root, posing potential security risks. This setup could potentially allow limited users to execute privileged commands via the Docker daemon.

Podman addresses this concern by providing the option for a completely rootless configuration, ensuring containers operate within a non-root environment. Below, you'll find the steps to configure your Podman instance for rootless usage:

- Install the `slirp4netns` and `fuse-overlayfs` tools to support rootless Podman operations.

    -   **AlmaLinux, CentOS Stream, Fedora, or Rocky Linux:**

            sudo dnf install slirp4netns fuse-overlayfs

    -   **Debian or Ubuntu:**

            sudo apt install slirp4netns fuse-overlayfs

- Assign `subuids` and `subgids` ranges for your limited user. In this example, we'll do so for the user `example-user`, granting them a sub-UID and sub-GID of `100000`, each with a range of `65535` IDs.

        sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 example-user

With Podman installed, everything is ready for you to start running containers with it. These next sections walk you through the major features of Podman for finding container images and running and managing containers.

## Getting an Image

With Podman installed and configured, you're ready to start running containers. The following sections will guide you through Podman's major features, including container image acquisition and container management. Additionally, you'll find instructions for obtaining container images and examples for further exploration.

### Searching for Images

One of the simplest ways to begin working with a container is by discovering an existing image in a registry. Utilizing Podman's `search` command, you can explore matching images across any container registries you've configured.

Note: While Podman may come pre-configured with some registries, manual configuration may be required on certain systems. You can achieve this by editing the `/etc/containers/registries.conf` file using your preferred text editor and appending a line similar to the following:

    unqualified-search-registries=['registry.access.redhat.com', 'registry.fedoraproject.org', 'docker.io', 'quay.io']

Feel free to replace the listed registries with those you prefer to search for container images on.

Note: Additionally, Podman's GitHub provides a registries.conf file [here](https://raw.githubusercontent.com/containers/podman/main/test/registries.conf) for initial reference.

For instance, the following example demonstrates a search for images matching the term `buildah`:

    podman search buildah

Please be aware that your search results may vary based on the registries configured within your Podman instance:

         {{< output >}}
          NAME                                                            DESCRIPTION
          registry.access.redhat.com/ubi8/buildah                         Containerized version of Buildah
          registry.access.redhat.com/ubi9/buildah                         rhcc_registry.access.redhat.com_ubi9/buildah
          registry.redhat.io/rhel8/buildah                                Containerized version of Buildah
          registry.redhat.io/rhel9/buildah                                rhcc_registry.access.redhat.com_rhel9/builda...
          [...]
          {{< /output >}}

### Downloading an Image

Once you've identified a desired image through registry search, Podman facilitates downloading, or pulling, that image. This is achieved using Podman's `pull` command, followed by the name of the container image:

          podman pull buildah

As the search output shows, there may be multiple registries matching a given container image:

          {{< output >}}
          Resolved "buildah" as an alias (/etc/containers/registries.conf.d/shortnames.conf)
          Trying to pull quay.io/buildah/stable:latest...
          Getting image source signatures
          [...]
          {{< /output >}}

You can also opt for a more precise approach by specifying the complete image name, including the registry path, to pull from a specific location.

For instance, in the following example, the Buildah image is pulled from the `docker.io` registry:

          podman pull docker.io/buildah/buildah

As illustrated, this method bypasses the process of resolving the shortname alias, directly fetching the Buildah image from the specified source:

          {{< output >}}
          Trying to pull docker.io/buildah/buildah:latest...
          Getting image source signatures
          [...]
          {{< /output >}}

### Building an Image

Similar to Docker, Podman empowers users to create container images from files. Typically, this building process utilizes the Dockerfile format, although Podman also supports the Containerfile format.

For comprehensive insights into crafting Dockerfiles, delve into our guide [How to Use a Dockerfile to Build a Docker Image](/docs/guides/how-to-use-dockerfiles/). This resource also provides links to additional tutorials offering in-depth coverage of Dockerfiles.

As an illustrative example to showcase Podman's build capabilities, consider the following Dockerfile:

        {{< file "Dockerfile" >}}

## Base on the most recently released Fedora
        FROM fedora:latest
        MAINTAINER ipbabble email buildahboy@redhat.com # not a real email

## Install updates and httpd

        RUN echo "Updating all fedora packages"; dnf -y update; dnf -y clean all
        RUN echo "Installing httpd"; dnf -y install httpd && dnf -y clean all

## Expose the default httpd port 80

        EXPOSE 80

## Run the httpd

        CMD ["/usr/sbin/httpd", "-DFOREGROUND"]
        {{< /file >}}

Save the contents provided in a file named `Dockerfile`. Then, from the directory containing the file, execute the following Podman command to build an image from it:

        podman build -t fedora-http-server .

The `-t` option allows you to assign a tag or name to the image, such as `fedora-http-server` in this instance. The . at the end of the command designates the directory where the Dockerfile is located, with . representing the current directory.

Continue reading in the section below titled [Running a Container Image](/docs/guides/using-podman/#running-a-container-image) to explore how you can execute a container from the image built using the aforementioned process.

Podman's `build` command mirrors Docker's functionality but is essentially a subset of Buildah's build capabilities. Notably, Podman leverages a segment of Buildah's source code to implement its build functionality.

Buildah offers an array of features and precise control for building containers. Consequently, many perceive Podman and Buildah as complementary tools. While Buildah serves as a robust solution for crafting container images from various sources, including container files like Dockerfiles, Podman excels in managing and executing the resultant containers.

For further insights into Buildah, encompassing setup procedures and usage guidelines, consult our guide [How to Use Buildah to Build OCI Container Images](/docs/guides/using-buildah-oci-images/).

### Listing Local Images

After acquiring one or more images on your local system, you can inspect them using Podman's `images` command. This command provides a comprehensive list of images that have either been created or downloaded onto your system:

    podman images

Following the aforementioned procedures of downloading and building container images, you can anticipate an output resembling the following:

       {{< output >}}
       REPOSITORY                         TAG         IMAGE ID      CREATED       SIZE
       localhost/fedora-http-server       latest      f6f5a66c8a4d  2 hours ago   328 MB
       quay.io/buildah/stable             latest      eef9e8be5fea  2 hours ago  358 MB
       registry.fedoraproject.org/fedora  latest      3a66698e6040  2 hours ago  169 MB{{< /output >}}

## Running a Container Image

Once you have images available, you can commence utilizing Podman to execute containers.

This process can be quite straightforward with Podman's `run` command, which simply requires the name of the image to initiate a container:

Below is an example utilizing the previously downloaded Buildah image. This command executes the `Buildah` image, specifically invoking the buildah command within the resulting container:

    podman run buildah buildah -v

The `-v` option is appended to display the application's version:

Note: buildah version 1.26.2 (image-spec 1.0.2-dev, runtime-spec 1.0.2-dev)

Container operations can become increasingly intricate, and Podman offers a plethora of features to cater to diverse needs in container execution.

Consider the `fedora-http-server` example, generated from the previously discussed Dockerfile. This instance initiates an HTTP server on the container's port `80`. The following command exemplifies how Podman empowers users to regulate container behavior:

Executing this command launches the container, triggering the automatic startup of an HTTP server. The `-p` option facilitates the publication of the container's port `80` to the local machine's port `8080`, while the `--rm` option ensures automatic cessation of the container upon completion—a convenient solution for rapid testing.

    podman run -p 8080:80 --rm fedora-http-server

Subsequently, on the machine hosting the image, employ a cURL command to confirm the provisioning of the default web page on port `8080`:

    curl localhost:8080

Upon execution, you should observe the HTML content of the Fedora HTTP Server Test Page:

       {{< output >}}
      <!doctype html>
      <html>
      <head>
      <meta charset='utf-8'>
      <meta name='viewport' content='width=device-width, initial-scale=1'>
      <title>Test Page for the HTTP Server on Fedora</title>
      <style type="text/css">
      /*<![CDATA[*/

      html {
        height: 100%;
        width: 100%;
      }
        body {
      [...]
      {{< /output >}}

## Managing Containers and Images

Podman is designed to efficiently oversee container operations, offering a multitude of commands for monitoring and managing containers.

The following sections outline some of the most essential Podman functionalities, empowering users to optimize container utilization.

### Listing Containers

Often, individuals working with containers may maintain one or more containers, occasionally even several, operating in the background.

To monitor these containers, Podman's `ps` command comes in handy. This command provides a comprehensive list of currently running containers on your system.

For example, if you are currently running the `fedora-http-server` container as demonstrated earlier, your output might resemble:

       podman ps

      {{< output >}}
      CONTAINER ID  IMAGE                                COMMAND               CREATED        STATUS            PORTS                 NAMES
      daadb647b880  localhost/fedora-http-server:latest  /usr/sbin/httpd -...  8 seconds ago  Up 8 seconds ago  0.0.0.0:8080->80/tcp  suspicious_goodall
      {{< /output >}}

To list all containers, including those not currently running, simply add the `-a` option to the command:

      podman ps -a

The output of this command also encompasses the `buildah` command executed earlier using `podman run`.

      {{< output >}}
      CONTAINER ID  IMAGE                                COMMAND               CREATED             STATUS                     PORTS                 NAMES
      db71818eda38  quay.io/buildah/stable:latest        buildah -v            12 minutes ago      Exited (0) 12 minutes ago                        exciting_kowalevski
      daadb647b880  localhost/fedora-http-server:latest  /usr/sbin/httpd -...  About a minute ago  Up About a minute ago      0.0.0.0:8080->80/tcp  suspicious_goodall
      {{< /output >}}

### Starting and Stopping Containers

Podman enables individual control over container stoppage and initiation through the `stop` and `start` commands, respectively. Both commands accept either the container ID or name as arguments, which can be obtained using the `ps` command, as demonstrated earlier.

For instance, you can halt the `fedora-http-server` container mentioned earlier using:

       podman stop daadb647b880

Had the container been launched without the `--rm` option, which automatically removes the container upon cessation, restarting the container would be as simple as:

       podman start daadb647b880

For either command, you have the flexibility to substitute the container's name with its ID, as shown:

       podman stop suspicious_goodall

### Removing a Container

You can manually delete a container using Podman's `rm` command, which, akin to the `stop` and `start` commands, accepts either a container ID or name as input.

       podman rm daadb647b880

### Creating an Image from a Container

Podman facilitates the transformation of a container into an image via the `commit` command. This enables the creation of an updated container image manually, following modifications to its components.

Similar to other container-related commands, this operation requires the container's ID or name as input. It's advisable to include an author name alongside the commit using the `--author` option:

    podman commit --author "Example User" daadb647b880

As discussed earlier in the section on image creation with Podman, while Buildah offers more advanced features and control in crafting container images, Podman is adept in many scenarios and may suffice for your specific requirements.

## Conclusion

Podman not only serves as a straightforward alternative to Docker but also stands as a robust containerization tool, boasting secure, rootless operations. Armed with this tutorial, you're equipped to leverage Podman for container execution and management.

Continue expanding your proficiency in container tools by exploring resources on Podman, Buildah, and Dockerfiles referenced throughout this tutorial. Enhance your understanding of Podman through the supplementary links provided at the conclusion of this tutorial.
