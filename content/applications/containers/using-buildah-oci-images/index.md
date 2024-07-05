---
slug: "Crafting Next-Level OCI Images with Buildah"
description: "Unlock the Potential of Container Creation with Buildah! Dive into this comprehensive tutorial to explore the versatility and power of Buildah, your go-to open-source tool for crafting containers and container images. Whether you're building from Dockerfiles, Containerfiles, or starting from scratch, discover the robust feature set that Buildah offers to streamline your containerization journey"
keywords: ['buildah run','what is buildah','install buildah']
published: 2024-05-03
modified_by:
  name: Pawan Kumar
title: "Use Buildah to Build OCI Container Images"
title_meta: "How to Use Buildah to Build OCI Container Images"
authors: ["Nathaniel Stickman"]
tags: ["saas"]
---

Buildah, an open-source containerization tool, empowers users to build images from scratch, Dockerfiles, or Containerfiles. Adhering to the Open Container Initiative (OCI) specifications, Buildah ensures that its images are both versatile and open.

Embark on your journey with Buildah by learning how to install and utilize it effectively through this tutorial. Below, discover step-by-step instructions for creating containers and converting them into images.

## Before You Begin
- Before delving into Buildah, ensure you're acquainted with our Getting Started with Utho guide.
- Throughout this guide, we leverage `sudo` whenever necessary. Refer to our [How to Secure Your Server](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to establish a standard user account, fortify SSH access, and eliminate redundant network services for enhanced security.

1.  Update your system.

    -   **AlmaLinux**, **CentOS Stream**, **Fedora**, or **Rocky Linux**:

            sudo dnf upgrade

    -   **Ubuntu**:

            sudo apt update && sudo apt upgrade

Note: This guide assumes you are using a non-root user account. Commands requiring elevated privileges are prefixed with `sudo`. If you are unfamiliar with the sudo command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## What Is Buildah?

[Buildah](https://buildah.io/) is a powerful open-source tool designed for constructing container images compliant with the Open Container Initiative (OCI) standards.

The OCI aims to establish an open standard for containerization, defining specifications for container runtimes and images. This initiative not only promotes interoperability but also enhances the security and efficiency of operating system virtualization.

Buildah offers robust capabilities for creating and managing OCI-compliant images. While Dockerfiles are a widely used format for container images, Buildah fully supports them and can directly generate images from them.

Moreover, Buildah enables users to construct container images from scratch. This means you can initiate the container creation process entirely from the command line, starting with a clean slate and adding only the necessary components. Buildah can then convert your work into an OCI container image.

### Buildah vs Docker

Although Buildah shares some functionality with Docker, it distinguishes itself in several ways. Notably, it mitigates the security risks associated with the Docker daemon. Unlike Docker, which runs the daemon with root-level access, Buildah operates without a daemon, enabling rootless containerization.

Buildah also empowers users to craft lightweight images by allowing them to build containers from scratch. This capability is invaluable when striving for minimalistic and efficient container deployments.

Furthermore, Buildah provides fine-grained control over images and their layers, catering to users seeking advanced containerization features.

However, Buildah is primarily focused on the creation and management of container images rather than their execution and deployment. Consequently, users often employ tools like Podman for running and managing containers. To delve deeper into the comparison between Podman and Docker, refer to our guide [Podman vs Docker: Comparing the Two Containerization Tools](/docs/guides/podman-vs-docker/).

## How to Install Buildah

Utilize your distribution's package manager to install Buildah.

    -   **AlmaLinux**, **CentOS Stream** (8 or later), **Fedora**, or **Rocky Linux**:

            sudo dnf install buildah

    -   **Ubuntu** (20.10 or later):

            sudo apt install buildah

Confirm the successful installation of Buildah by executing the following command to check the installed version:

        buildah -v

The output may vary, but ensure that Buildah is installed without errors:

      {{< output >}}
      buildah version 1.26.1 (image-spec 1.0.2-dev, runtime-spec 1.0.2-dev)
      {{< /output >}}

### Configuring Buildah for Rootless Usage

By default, Buildah commands are executed with root privileges, typically preceded by the `sudo` command. However, one of Buildah's standout features is its capability to run containers in rootless mode, enabling limited users to work securely with Buildah.

While Docker also supports running commands as a limited user, the Docker daemon still operates as root. This poses a potential security risk, as it may allow limited users to execute privileged commands through the daemon.

Buildah's rootless mode addresses this concern by running containers entirely in a non-root environment, without the need for a root daemon. Below are the steps required to configure your Buildah instance for rootless usage:

1.  Install the `slirp4netns` and `fuse-overlayfs` tools to facilitate rootless Buildah operations.

    -   **AlmaLinux**, **CentOS Stream**, **Fedora**, or **Rocky Linux**:

            sudo dnf install slirp4netns fuse-overlayfs

    -   **Ubuntu**:

            sudo apt install slirp4netns fuse-overlayfs

2.  Define `subuids` and `subgids` ranges for your limited user. The following example illustrates how to do this for the user `example_user`, assigning a sub-UID and sub-GID of `100000` each, with a range of `65535` IDs:

        sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 example_user

## How to Use Buildah

Buildah primarily serves as a tool for creating container images. Similar to Docker, Buildah can assemble containers from Dockerfiles, but it distinguishes itself by also allowing users to build images from scratch.

The subsequent sections detail how to build container images using both of these methods.

### Creating an Image from a Dockerfile

Utilizing Dockerfiles with Buildah offers a user-friendly method for creating containers, particularly for individuals already accustomed to Docker or Dockerfiles.

Buildah seamlessly interprets Dockerfile scripts, simplifying the process of building Docker container images with Buildah.

In this tutorial, we'll employ an example Dockerfile sourced from one of the official Buildah tutorials. This Dockerfile configures a container equipped with the latest Fedora version and the Apache HTTP server (`httpd`). Additionally, it exposes the HTTP server via port `80`.

1.  Begin by creating a new file named `Dockerfile` within your user's home directory:

        nano Dockerfile

1.  Populate the file with the following content:

       {{< file "Dockerfile" >}}

# Base on the most recently released Fedora

     FROM fedora:latest
     MAINTAINER ipbabble email buildahboy@redhat.com # not a real email

# Install updates and httpd

     RUN echo "Updating all fedora packages"; dnf -y update; dnf -y clean all
     RUN echo "Installing httpd"; dnf -y install httpd && dnf -y clean all

# Expose the default httpd port 80

     EXPOSE 80

# Run the httpd

     CMD ["/usr/sbin/httpd", "-DFOREGROUND"]
     {{< /file >}}

To exit nano, press **CTRL+X**, then confirm by pressing `Y`, and finally hit `Enter` to quit the editor.

If you're still in the directory containing the Dockerfile (typically your user's home directory), you can proceed to build the container's image without delay.

Execute the following command to build the image, naming it `fedora-http-server`:

        buildah build -t fedora-http-server

Upon successful completion, the output should resemble the following:

    {{< output >}}
    STEP 1/6: FROM fedora:latest
    Resolved "fedora" as an alias (/etc/containers/registries.conf.d/000-shortnames.conf)
    Trying to pull registry.fedoraproject.org/fedora:latest...
    Getting image source signatures
    Copying blob 75f075168a24 done
    Copying config 3a66698e60 done
    Writing manifest to image destination
    Storing signatures
    STEP 2/6: MAINTAINER ipbabble email buildahboy@redhat.com # not a real email
    STEP 3/6: RUN echo "Updating all fedora packages"; dnf -y update; dnf -y clean all
    [...]
    {{< /output >}}

You can run the image with Podman, a popular container management tool often used alongside Buildah.

Begin by installing Podman:

    -   **AlmaLinux**, **CentOS Stream**, **Fedora**, or **Rocky Linux**:

            sudo dnf install podman

    -   **Ubuntu**:

            sudo apt install podman

Then, execute the following command. The `-p` option "publishes" port `80` of the container to port `8080` on your local machine. Additionally, the `--rm` option ensures the container is removed after it finishes running, ideal for quick tests.

        podman run -p 8080:80 --rm fedora-http-server

To verify the server is running, open another Terminal session on the same machine and use the following `cURL` command:

        curl localhost:8080

You should receive the raw HTML of the Fedora HTTP Server test page as output.  

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

1.  When done, stop the container, but first, determine your container's ID or name:

        podman ps

You should see an out put like this:

         {{< output >}}

         CONTAINER ID  IMAGE                                COMMAND               CREATED        STATUS            PORTS                 NAMES
         daadb647b880  localhost/fedora-http-server:latest  /usr/sbin/httpd -...  8 seconds ago  Up 8 seconds ago  0.0.0.0:8080->80/tcp  suspicious_goodall
         {{< /output >}}

To stop the container, first, identify its ID or name:You will see output similar to this:

        podman stop container-name-or-id

Stop the container using its name or ID:Since we set this container to automatically remove with the `--rm` flag, stopping it also removes it.

You can now log out, close the second Terminal session, and return to the original one.

         exit

Explore more about Podman in our guide [How to Install Podman for Running Containers](/docs/guides/using-podman/).

Additionally, dive deeper into crafting Dockerfiles in our guide [How to Use a Dockerfile to Build a Docker Image](/docs/guides/how-to-use-dockerfiles/). This guide provides links to further tutorials for a more comprehensive understanding of Dockerfiles.

### Creating an Image from Scratch

As highlighted earlier, one of Buildah's standout features is its capability to construct container images from scratch. This segment guides you through an illustrative example of this process.

Note: Buildah's commands for container manipulation often involve several keywords, prompting the use of environment variables. For instance, to create a new Fedora container, you might encounter a command similar to:

       fedoracontainer=$(buildah from fedora)

Learn more about the functionality of environment variables in our guide [How to Use and Set Environment Variables](/docs/guides/how-to-set-linux-environment-variables/).

The upcoming example starts with an empty container and gradually incorporates Bash and other fundamental utilities. This showcases how you can augment programs to curate a minimalistic container image.

Note: This guide assumes a preference for running Buildah in rootless mode, a key differentiator from Docker. However, it's worth noting that using the Ubuntu package manager, APT, presents challenges when installing packages onto a non-root container. Therefore, the instructions provided below are tailored for RHEL-derived distributions like AlmaLinux, CentOS Stream, Fedora, and Rocky Linux.

If you opt to run `Buildah` in regular root mode under Ubuntu, simply prefix each buildah command with `sudo`.

To operate Buildah in rootless mode, you must first execute the `unshare` command. This command places you in a shell within the user namespace. The subsequent steps assume that you are in the user namespace shell until otherwise noted; otherwise, the `buildah mount` command below will fail.

Enter the user namespace shell:

        buildah unshare

Create an empty container using Buildah's `scratch` base:

        scratchcontainer=$(buildah from scratch)

Mount the container as a virtual file system:

        scratchmnt=$(buildah mount $scratchcontainer)

Install Bash and coreutils to the empty container.

    -   **AlmaLinux**, **CentOS Stream**, **Fedora**, or **Rocky Linux**:

Replace the value 36 below with the version of your RHEL-derived distribution:

            dnf install --installroot $scratchmnt --releasever 36 bash coreutils --setopt install_weak_deps=false

    -   **Debian** or **Ubuntu**:

Replace the value `bullseye` below with the codename of your Debian-based distribution:

            sudo apt install debootstrap
            sudo debootstrap bullseye $scratchmnt

You can now test Bash in the container. The following command places you in a Bash shell within the container:

             buildah run $scratchcontainer bash

You can then exit the Bash shell using:

              exit

Now you can securely manage the container from outside the user namespace shell initiated with `unshare`:

              exit

From now on, we'll use `$scratchcontainer` to refer to the container's name, which should be `working-container`. However, if you have more than one container, the container's name may vary. You can confirm the container name using the `buildah containers` command.

Let's recreate the test `script file`. From your user's home directory, create the script-files folder and the `example-script.sh` file in the `script-files` folder:

        mkdir script-files
        nano script-files/example-script.sh

Give it the following contents:

        {{< file "script-files/example-script.sh" >}}
         #!/bin/bash
        echo "This is an example script."
        {{< /file >}}

When finished, press **CTRL+X** to exit, **Y** to save, and Enter to quit.

The following command copies that file to the container's `/usr/bin` directory:

        buildah copy working-container ~/script-files/example-script.sh /usr/bin

To verify the file's delivery, run the `ls` command on the container for the `/usr/bin` directory:

        buildah run working-container ls /usr/bin

Your `example-script.sh` file should be listed among the files:

        {{< output >}}
        [...]
        example-script.sh
        [...]
        {{< /output >}}

For a functional example of executing scripts on a Buildah container, grant this file executable permissions:

        buildah run working-container chmod +x /usr/bin/example-script.sh

You can now execute the script using the `run` command:

        buildah run working-container /usr/bin/example-script.sh

Your output should resemble the following:

       {{< output >}}
       This is an example script.
       {{< /output >}}

Once you are content with the container, commit the changes to an image:

        buildah commit working-container bash-core-image

Your output should be similar to this:

        {{< output >}}
        Getting image source signatures
        Copying blob a0282af9505e done
        Copying config 9ea7958840 done
        Writing manifest to image destination
        Storing signatures
        9ea79588405b48ff7b0572438a81a888c2eb25d95e6526b75b1020108ac11c10
        {{< /output >}}

You can now unmount and remove the container:

        buildah unmount working-container
        buildah rm working-container

### Managing Images and Containers

Buildah is primarily designed for creating container images, but it also offers features for managing containers and images. Here's a quick overview of the commands associated with these functionalities:

- To view a list of images created using Buildah, use the following command:

        buildah images

If you've followed the earlier sections on creating Buildah images, your image list might resemble this:

    {{< output >}}

    REPOSITORY                  TAG      IMAGE ID       CREATED              SIZE
    localhost/fedora-http-server        latest   c313b363840d   8 minutes ago    314 MB
    localhost/bash-core-image           latest   9ea79588405b   20 minutes ago   108 MB
    registry.fedoraproject.org/fedora   latest   3a66698e6040   2 months ago     169 MB    {{< /output >}}

- To list the containers currently running under Buildah, execute the following command:

        buildah containers

If you run this command while the container created in the previous section is still active, you might see an output similar to:

         {{< output >}}
         CONTAINER ID  BUILDER  IMAGE ID     IMAGE NAME                       CONTAINER NAME
         68a1cc02025d     *                  scratch                          working-container
         {{< /output >}}

- You can retrieve the details of a specific image with a command like the following, replacing `9ea79588405b` with your image's ID. You can obtain the image's ID either during the image creation process or from the output of the `buildah images` command mentioned earlier: command show above:

           buildah inspect 9ea79588405b

The image details consist of a JSON document that fully represents the contents of the image. All container images are essentially JSON documents containing instructions for building their corresponding containers.

Here's an example of the initial portion of a container image JSON resulting from the previous section on creating an image from scratch:

    {{< output >}}
     {
    "Type": "buildah 0.0.1",
    "FromImage": "localhost/bash-core-image:latest",
    "FromImageID": "9ea79588405b48ff7b0572438a81a888c2eb25d95e6526b75b1020108ac11c10",
    "FromImageDigest": "sha256:beee0e0603e62647addab15341f1a52361a9684934d8d6ecbe1571fabd083dca",
    "Config": "{\"created\":\"2022-07-20T17:34:55.16639723Z\",\"architecture\":\"amd64\",\"os\":\"linux\",\"config\":{\"Labels\":{\"io.buildah.version\":\"1.26.1\"}},\"rootfs\":{\"type\":\"layers\",\"diff_ids\":[\"sha256:a0282af9505ed0545c7fb82e1408e1b130cad13a9c3393870c7c4a0d5cf06a62\"]},\"history\":[{\"created\":\"2022-07-20T17:34:55.72288433Z\",\"created_by\":\"/bin/sh\"}]}",
    "Manifest": "{\"schemaVersion\":2,\"mediaType\":\"application/vnd.oci.image.manifest.v1+json\",\"config\":{\"mediaType\":\"application/vnd.oci.image.config.v1+json\",\"digest\":\"sha256:9ea79588405b48ff7b0572438a81a888c2eb25d95e6526b75b1020108ac11c10\",\"size\":324},\"layers\":[{\"mediaType\":\"application/vnd.oci.image.layer.v1.tar\",\"digest\":\"sha256:a0282af9505ed0545c7fb82e1408e1b130cad13a9c3393870c7c4a0d5cf06a62\",\"size\":108421632}],\"annotations\":{\"org.opencontainers.image.base.digest\":\"\",\"org.opencontainers.image.base.name\":\"\"}}",
    "Container": "",
    "ContainerID": "",
    "MountPoint": "",
    "ProcessLabel": "",
    "MountLabel": "",
    "ImageAnnotations": {
        "org.opencontainers.image.base.digest": "",
        "org.opencontainers.image.base.name": ""
    },
    [...]
     {{< /output >}}

## Conclusion

Buildah offers a straightforward yet powerful solution for constructing container images. It goes beyond being merely a Docker alternative, serving as a secure containerization tool for creating open containers and images. With this guide, you're equipped with all the essentials to begin crafting your own images and harnessing the full potential of Buildah.
