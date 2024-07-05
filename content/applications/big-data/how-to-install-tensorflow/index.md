---
slug: "Installing TensorFlow: A Step-by-Step Guide"
description: "In this TensorFlow tutorial, you'll discover what TensorFlow is, how it operates, and how to install it. Additionally, you'll find some helpful resources to kickstart your journey"
keywords: ['TensorFlow','installation','machine learning','and key phrases']
tags: ['python', 'ubuntu', 'linux']
published: 2024-03-18
modified_by:
  name: Utho
title: "Setting Up TensorFlow on Ubuntu 20.04"
title_meta: "Installing TensorFlow on Ubuntu 20.04: A Step-by-Step Guide"
authors: ["Pawan Kumar"]
---

[TensorFlow](https://www.tensorflow.org/) is an open-source software library utilized for machine learning and [deep neural networks](https://en.wikipedia.org/wiki/Deep_learning). Initially developed by Google for research and production, TensorFlow is now released under the Apache license. It supports various operating systems, including popular Linux distributions. For educational purposes, installing TensorFlow within a Python virtual environment is recommended, especially for newcomers to machine learning.

This guide outlines the installation process for TensorFlow on Ubuntu 20.04, a platform fully supported by TensorFlow. However, the steps are similar for most Linux distributions.

## Before You Begin

1.  If you haven't already, create a Utho account and a Compute Instance.

1.  Follow our guide to update your system. Additionally, consider setting the timezone, configuring your hostname, creating a limited user account, and enhancing SSH access for better security.

Note: This guide is intended for a non-root user. Commands needing elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, refer to the [Linux Users and Groups](/docs/guides/linux-users-and-groups/)guide.

## Benefits of TensorFlow

- TensorFlow provides various levels of abstraction and complexity tailored to different tasks, along with user-friendly APIs to facilitate getting started.

- It is suitable for both production environments and research and experimentation purposes.

- TensorFlow boasts advanced computational graph visualization features, along with tools for library management and debugging.

- It is stable, scalable, and delivers high performance, backed by a robust community for support.

## System Requirements

- A reliable hosting environment with a minimum of 4GB of memory.

- For Ubuntu users, TensorFlow necessitates version 16.04 or higher.

## Prerequisites

Before installing TensorFlow, ensure you have the following:

- Python 3.8 or newer, along with its necessary libraries.
- Python virtual environment - used to run TensorFlow within a virtual environment.
- The latest version of pip (version 19 or newer).

The subsequent section will guide you through installing Python (if not already installed), setting up a Python virtual environment, and updating `pip` to the latest version.

### Verifying Python and Its Essential Libraries

Verify the current version of Python installed on your system.

    python3 --version

    {{< output >}}
    Python 3.8.5
    {{< /output >}}

If `pip` is not already installed, proceed to install it.

    sudo apt install python3-pip

### Setting Up Python Virtual Environment

1. If Python is already installed, update `apt` and install the Python virtual environment along with its necessary packages.

        sudo apt update
        sudo apt install python3-dev python3-pip python3-venv

1. Verify the versions of both Python and `pip`.

        python3 --version
        pip3 --version

Ubuntu will display the version for each module.

       {{< output >}}
       Python 3.7.5
       pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)
       {{< /output >}}

### Creating a Python Virtual Environment

Creating a virtual Python environment establishes a segregated environment for your TensorFlow projects. Within this environment, you can maintain a separate set of packages, ensuring that your TensorFlow projects do not interfere with other Python projects.

1. Start by creating a new directory dedicated to your TensorFlow development projects.

        mkdir ~/tensorflow-dev
        cd ~/tensorflow-dev

1. Generate the virtual environment using the command below:

        python3 -m venv --system-site-packages ./venv

The command above creates a directory named `venv`, which holds the necessary Python files. You can opt for any name for the virtual environment instead of `./venv`.

1. To activate your virtual environment, run the activate script.

        source ./venv/bin/activate

Note: This source command is compatible with `sh`, `bash`, and `zsh` shells. For `csh` or `tcsh` shells, activate the virtual environment with `source ./venv/bin/activate.csh`. You can identify the shell you're using with the command `echo $0`.

1. Once your virtual environment is activated, your shell prompt will begin with `(venv)` (or the name you chose for the virtual environment directory).

       {{< output >}}
       (venv) example-user@example-hostname:~/tensorflow-dev$
       {{< /output >}}

1. To install TensorFlow, ensure that `pip` version 19 or higher is used. Within the virtual environment, upgrade the `pip` package with the following command:

        pip install --upgrade pip

Your output confirms the successful upgrade of pip.

      {{< output >}}
      Successfully installed pip-21.0.1
      {{< /output >}}

Note: To exit the virtual environment, simply use the `deactivate` command. You can reactivate it later by using the `source` command. It's best to stay within the virtual environment while working with TensorFlow.

## Install TensorFlow

1. In the virtual environment, install TensorFlow using `pip`. The command below retrieves the latest stable version along with all required package dependencies.

        pip install --upgrade tensorflow

1. Use the following command to list Python packages and ensure that `tensorflow` is included.

        pip list | grep tensorflow

The tensorflow module should be included in the list.

           {{< output >}}
           tensorflow             2.4.1
           {{< /output >}}

Note: You can install TensorFlow without using a virtual environment, but this method is **NOT** recommended due to potential issues.

- Upgrade the Python-specific `pip` module with with `python -m pip install --upgrade` by running the command:

- Install TensorFlow by running the command `pip3 install --user --upgrade tensorflow`.

Note: Exercise caution to avoid upgrading your system's version of pip, as doing so may lead to unintended consequences.

## Verifying Your TensorFlow Installation

1. Once you've followed the steps above and installed TensorFlow, verifying the installation is straightforward. Simply use the following command to print the TensorFlow version:

        python -c 'import tensorflow as tf; print(tf.__version__)'

         {{< output >}}

       2021-04-30 10:34:32.450931: W tensorflow/stream_executor/platform/default/dso_loader.cc:60] Could not load dynamic library 'libcudart.so.11.0'; dlerror: libcudart.so.11.0: cannot open shared object file: No such file or directory
       2021-04-30 10:34:32.450973: I tensorflow/stream_executor/cuda/cudart_stub.cc:29] Ignore above cudart dlerror if you do not have a GPU set up on your machine.
       2.4.1
         {{< /output >}}

Note: If your Utho doesn't have a GPU, you might encounter a warning indicating that `libcudart` or a similar GPU library couldn't be loaded. This warning is normal for a CPU-powered Utho. In such cases, you should expect to see an `info` message advising you to disregard the warning in a non-GPU environment.

1. To suppress warnings or error messages, you can adjust the log warning level using the `os.environ` variable. The example below demonstrates printing the TensorFlow version without any warnings:

        python -c 'import os; os.environ["TF_CPP_MIN_LOG_LEVEL"]="3"; import tensorflow as tf; print(tf.__version__)'

       {{< output >}}
       2.4.1
       {{< /output >}}

{{< note respectIndent=false >}}

You can substitute different log levels for '3' as demonstrated below:

   `0` = all messages are logged (default behavior)

   `1` = INFO messages are not printed

   `2` = INFO and WARNING messages are not printed

   `3` = INFO, WARNING, and ERROR messages are not printed
    {{< /note >}}

1. To deactivate the virtual environment and return to the original non-virtual shell, execute the following command:

        deactivate

Note: You can always re-enter the virtual environment by running source `./venv/bin/activate`.

## Additional Resources

Machine learning is a complex field, and TensorFlow is a sophisticated application. To aid your understanding and get you started, TensorFlow offers several additional resources:

- [TensorFlow tutorials for beginners and experts](https://www.tensorflow.org/tutorials)
- [Essential TensorFlow documentation](https://www.tensorflow.org/guide)
- List of [TensorFlow modules & functions](https://www.tensorflow.org/api_docs/python/tf)
- Getting Started with [machine learning](https://www.tensorflow.org/resources/learn-ml)
- [Tools](https://www.tensorflow.org/resources/tools) to assist you in your TensorFlow workflows
- [TensorFlow community](https://www.tensorflow.org/community) which guides you on how to participate and contribute.
- As you work on your projects, we recommend exploring the comprehensive [TensorFlow site](https://www.tensorflow.org/).
