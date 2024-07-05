---
slug: Beginner's Guide to Rasa
title: "Exploring Rasa: An Introductory Guide to Automated Chat Framework"
description: "Discover Rasa: A Free, Machine Learning Framework for Automated Text and Voice Conversations. Dive into this tutorial to learn the fundamentals and get started with Rasa."
keywords: ['rasa chatbot tutorial','rasa ai demo','rasa open source']
authors: ["Pawan Kumar"]
published: 2024-03-14
modified_by:
  name: Pawan Kumar
---

Rasa is a user-friendly machine learning framework, leveraging a story-driven method to build automated text and voice chat assistants. With Rasa, crafting and training chatbots is straightforward, offering seamless integration points for automated assistants.

In this tutorial, you'll embark on your Rasa journey. Discover how to install the framework, work with models, and even deploy a Rasa instance to a Kubernetes cluster. Let's get started.

## Before You Begin

If you haven't yet, make sure to set up a Utho account and create a Compute Instance. Refer to our guides on Getting Started with Utho and Creating a Compute Instance for detailed steps.

Next, follow our guide on Setting Up and Securing a Compute Instance. This will help you update your system and perform additional tasks like setting the timezone, configuring your hostname, creating a limited user account, and strengthening SSH access.

Note: This guide is intended for non-root users. Commands that need elevated privileges are prefixed with sudo.

## Installing Rasa Open Source: A Step-by-Step Guide

You can install Rasa Open Source using the Python 3 Pip package manager. The following sections will guide you through setting up the prerequisites and installing Rasa.

Once Rasa Open Source is installed, you can create, build, and run Rasa projects as required. Continue reading to learn how to start a Rasa project, understand its components, and deploy it to a Kubernetes cluster.

Note: While the official documentation for Rasa Open Source specifies Ubuntu as the supported Linux distribution, the steps outlined in this tutorial have proven successful for installing Rasa Open Source on other distributions such as CentOS Stream, AlmaLinux Rocky Linux and Debian.

### Setting Up Prerequisites for Rasa

Rasa operates with Python 3, Pip, and a Python virtual environment. Before you begin, ensure that your system has the required software installed with supported versions.

Follow these steps to install the necessary software and set up the initial virtual environment.

To ensure compatibility, install Python 3 and Pip 3 using the commands provided below. If these packages are already installed, the commands will simply update them.

-   **Debian 11** and **Ubuntu 22.04 LTS**:

        ```command
        sudo apt install python3-dev python3-pip python3-venv
        ```

-   **AlmaLinux 9**, **CentOS Stream 9**, and **Rocky Linux 9**:

        ```command
        sudo dnf install python3-devel
        ```

Create a Python virtual environment for your Rasa project and activate it. In this tutorial, we'll name the virtual environment "rasa-venv" and store it in the home directory of the current user.

    ```command
    python3 -m venv ~/rasa-venv
    source ~/rasa-venv/bin/activate
    ```

    You'll notice that the command line now begins with (rasa-venv).

Note: For reference, exit the virtual environment using the command shown here:

    ```command
    deactivate
    ```

Note: To re-enter the virtual environment, just repeat the source command as shown above.

### Installing Rasa Open Source: Step-by-Step Guide

Now, let's proceed with installing Rasa Open Source. Rasa is distributed as a Pip package and can be installed once the package manager is set up.

Upgrade the Pip installation to make sure it has the latest available packages:

    ```command
    pip3 install --upgrade pip
    ```

Install Rasa Open Source:

    ```command
    pip3 install rasa
    ```

## Building a Chatbot with Rasa: Step-by-Step Guide

Now that Rasa Open Source is installed, you can begin creating a basic Rasa project. This section will walk you through the steps to create the project and provide a detailed overview of its structure. After that, you'll learn how to run the project and operate a fully functional Rasa chatbot.

### Starting a Rasa Project: Step-by-Step Guide

To create a new Rasa project, run the initialization script. In this example, we start in the current user's home directory, but you can choose any directory to house the Rasa project subdirectory.

    ```command
    cd ~/
    rasa init
    ```

During the setup, you'll be prompted to specify the location for the Rasa project. In this tutorial, we'll use rasa-example, resulting in a ~/rasa-example directory. Since this directory doesn't exist yet, press <kbd>Y</kbd> (for "Yes") to create it. The following prompt will inquire whether you wish to initiate training for an initial model. Select <kbd>n</kbd> (for "No") here, as the tutorial will cover training later on.

Navigate to the newly created directory for your Rasa project. In the example provided, this directory would be ~/rasa-example.

    ```command
    cd ~/rasa-example
    ```

### Understanding the Structure of a Rasa Project

Upon creation, the project directory includes a basic structure. This section will dissect the default contents of the project to facilitate understanding and navigation of Rasa's components.

The structure typically resembles the following outline, excluding files like __init__.py, which may not be necessary for developing the example Rasa assistant.

-   `actions/`

-   In actions.py, you define custom actions for the Rasa assistant using Python code that can be triggered under specific conditions.

-   You can find comprehensive information about actions and their usage in Rasa's [documentation](https://rasa.com/docs/rasa/actions)

-   The data/ directory houses the core models for the Rasa assistant, where most of the assistant's development typically occurs.

-   In nlu.yml, Natural Language Understanding (NLU) models are defined for the Rasa assistant. This provides structures for the assistant to identify user intentions and communicated information.

For more details on these models, refer to the official documentation's page on [NLU Training Data](https://rasa.com/docs/rasa/nlu-training-data/).

-   In rules.yml, a set of specific actions is defined based on particular conditions. These should outline rule-like behavior or actions to be taken consistently when certain intentions or information are provided by the user.

For further context, refer to the official documentation's [Rules](https://rasa.com/docs/rasa/rules/).

-   In stories.yml, dialogues that the Rasa assistant is expected to engage in are modeled. These models are utilized for training the assistant for conversation and comprise annotations of user intention and/or information, along with sequences of assistant actions.

For more details on the roles and specifics of stories, refer to the official documentation's [Stories](https://rasa.com/docs/rasa/stories/) page.

-   `tests/`

-   In test_stories.yml, test stories are defined to verify that the Rasa assistant responds as expected.

For information on constructing effective Rasa test stories, refer to the official documentation's page on [Testing Your Assistant](https://rasa.com/docs/rasa/testing-your-assistant).

-   In config.yml, the configuration for training the Rasa assistant is specified. If not explicitly defined, Rasa uses a default approach.

To learn more about model configuration, visit [Model Configuration](https://rasa.com/docs/rasa/model-configuration/) in the official documentation.

-   In credentials.yml, the Rasa assistant's credentials for interacting with text and voice chat platforms are stored. The default file contains placeholders for various platforms, such as Facebook, Slack, and Socket.IO.

For more information, refer to the [Connecting to Messaging and Voice Channels](https://rasa.com/docs/rasa/messaging-and-voice-channels).

-   In domain.yml, you define which components from the configurations to include in the Rasa assistant's "world". For instance, use this to include intents defined in the nlu.yml file or any created actions. This file is also where you define responses.

For a more comprehensive explanation and an additional example, refer to the official documentation [Domains](https://rasa.com/docs/rasa/domain/).

-   In endpoints.yml, you specify the various endpoints that the Rasa assistant connects to. This includes defining an endpoint for Rasa to periodically fetch a model from a remote server.

Follow the links provided in the default file contents to explore the various types of endpoints available.

### Running the Rasa Assistant: Step-by-Step Guide
Before interacting with the assistant, it needs to be trained with a functional model. While you can modify the files in the data/ subdirectory at this stage, the default Rasa project includes enough content to showcase its capabilities.

Discover how to create effective models for developing a Rasa assistant by exploring the best practices guides available in the official documentation. These include a guide on [Conversation-driven Development](https://rasa.com/docs/rasa/conversation-driven-development) and a guide on [Generating NLU Data](https://rasa.com/docs/rasa/generating-nlu-data)ready for production.

Train the assistant using the default models (or custom models) with a single command using the Rasa CLI tool:

```command
rasa train
```

Rasa proceeds with the training process, creating a comprehensive machine learning model from the provided domain, NLU, story, and rule models.

Once the training finishes, users can start interacting with the assistant directly from the command line. Use the command below to start a command line chat session with the assistant:

```command
rasa shell
```

Explore the chat to experience a sample of how the assistant responds and learns from interactions. Below is an example exchange using Rasa's default models:

```output
Your input ->  Hello!
Hey! How are you?
Your input ->  Great!
Great, carry on!
Your input ->  Thanks!
Bye
Your input ->
```

When you're done, simply enter /stop to end the conversation.

Comparing these responses with the model contents in the /data directory can provide insights into how Rasa interprets and utilizes these models.

## Deploying a Rasa Chatbot: Step-by-Step Guide

For some scenarios, running Rasa locally might suffice. For instance, the chat application example in the Socket.IO tutorial mentioned above can utilize a local Rasa instance to incorporate Rasa chat features.

However, in most cases, deploying the Rasa instance becomes necessary. There are various methods to achieve this, some of which are detailed in Rasa's documentation [documentation](https://rasa.com/docs/rasa/deploy/introduction#alternative-deployment-methods).

The recommended deployment approach for Rasa is via Kubernetes. To start, the following sections show how to create a Utho Kubernetes cluster and deploy a simple Rasa project on it.

### Deploying Rasa

To deploy Rasa, you'll need a Kubernetes cluster with kubectl configured to interact with it. Additionally, Helm must be installed as the Rasa deployment uses a Helm Chart configuration.

Make sure to exit the Python virtual environment:

    ```command
    deactivate
    ```

Deploying and Managing a Cluster on Utho Kubernetes Engine to set up a Kubernetes cluster and configure kubectl.

Create a namespace for the Rasa Kubernetes cluster. In this example, we'll use the namespace rasacluster.

    ```command
    kubectl create namespace rasacluster
    ```

Follow our tutorial on [Installing Apps on Kubernetes with Helm 3](/docs/guides/how-to-install-apps-on-kubernetes-with-helm-3/#install-the-helm-client) to learn how to install the Helm CLI client.

Note: Users on  CentOS Stream, AlmaLinux, and Rocky Linux might need to install git and tar before installing Helm.

    ```command
    sudo dnf install git tar
    ```
    {{< /note >}}

Create a directory named rasa-chart in your home directory and navigate into it:

    ```command
    mkdir ~/rasa-chart
    cd ~/rasa-chart
    ```

Create a file named rasa-values.yaml within the newly created ~/rasa-chart directory:

    ```command
    nano ~/rasa-chart/rasa-values.yaml
    ```

Add the following contents to the file:

    ```file{title="rasa-values.yaml" lang="yaml"}
    applicationSettings:
      initialModel: "https://github.com/RasaHQ/rasa-x-demo/blob/master/models/model.tar.gz?raw=true"
      trainInitialModel: true
      credentials:
        enabled: true
        additionalChannelCredentials:
          rest:
    ```

    To save the file and exit the nano text editor, press <kbd>CTRL</kbd>+<kbd>X</kbd>, then confirm with <kbd>Y</kbd>, and press <kbd>Enter</kbd>.

    The provided configuration sets up a Rasa instance with a basic example model and configuration. It downloads an initial training model during the Rasa deployment, trains that model, and enables Rasa's REST API endpoints.

For further details on Rasa's Helm configurations, refer to the [default Rasa Chart configuration](https://raw.githubusercontent.com/RasaHQ/helm-charts/main/charts/rasa/values.yaml). This includes most of the settings for customizing a Rasa deployment, along with helpful comments.

For a more advanced and practical approach to obtaining an initial model, check out one of Rasa's [example Helm Chart configurations](https://github.com/RasaHQ/helm-charts/blob/main/examples/rasa/train-model-helmfile.yaml). This configuration downloads model files from a Git repository and trains an initial model from those files. A similar approach can be used to deploy a custom-developed model.

To add the Rasa repository to Helm, use the following command:

    ```command
    helm repo add rasa https://helm.rasa.com
    ```

    ```output
    "rasa" has been added to your repositories
    ```

Deploy the Rasa Helm Chart configuration to the cluster's namespace with the following command. The rasarelease portion of the example command provides a name for the deployment, which can be used to access the deployment later on:

    ```command
    helm install --namespace rasacluster --values rasa-values.yaml rasarelease rasa/rasa
    ```

    ```output
    [...]
    rasa 3.2.6 has been deployed!
    [...]
    ```

### Accessing the Rasa Assistant: How to Interact with Your Deployed Chatbot

Now, a Rasa instance with a basic model should be running on the Kubernetes cluster. To access the instance, use the commands provided here to forward the instance's port:

```command
export SERVICE_PORT=$(kubectl get --namespace rasacluster -o jsonpath="{.spec.ports[0].port}" services rasarelease)
kubectl port-forward --namespace rasacluster svc/rasarelease ${SERVICE_PORT}:${SERVICE_PORT}
```

```output
Forwarding from 127.0.0.1:5005 -> 5005
Forwarding from [::1]:5005 -> 5005
```

The output will indicate the port on which the Rasa instance is available. Open a new terminal window and witness it in action with a command like this:

```command
curl localhost:5005
```

```output
Hello from Rasa: 3.2.6
```

The Helm Chart configuration mentioned earlier also enables the Rasa assistant's REST API, facilitating convenient access to the model from various applications. Here's an example of using the API with cURL:

```command
curl -X POST localhost:5005/webhooks/rest/webhook -d '{ "sender": "A User", "message": "Hello, Rasa!" }'
```

```output
[{"recipient_id":"A User","text":"Hey! How are you?"}]
```

The /webhooks/rest/webhook endpoint allows chatting with the assistant, much like using the command line. The accepted data structure allows specifying message senders, enabling the assistant to keep track of conversations across multiple users.

### Updating the Rasa Deployment: How to Modify and Upgrade Your Rasa Assistant

As your setup evolves, you may need to adjust the Rasa Helm Chart configuration. The example above uses a sample model, but you can use the [Rasa API](https://rasa.com/docs/rasa/pages/http-api) to manually create and train models. You can replace the example URL with a URL for another model, and later adapt the deployment to use a [model server](https://rasa.com/docs/rasa/model-storage/#load-model-from-server).

In such cases, utilize Helm's upgrade command to apply updates according to changes made in the rasa-values.yaml file. Following the example designations provided in this tutorial, the command should resemble this:

```command
helm upgrade --namespace rasacluster --reuse-values -f rasa-values.yaml rasarelease rasa/rasa
```

## Exploring Rasa's Capabilities and Best Practices

This guide provides all the essentials for starting to create automated assistants and chatbots using Rasa. Rasa's models are versatile and can be tailored to specific requirements. To get started, it's important to plan your project, understand the capabilities of the models, and follow Rasa's best practices. The links provided in this guide and the additional resources in the Rasa documentation below can accelerate the development of a customized AI assistant.
