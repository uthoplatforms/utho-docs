---
slug: "Unleash the Power: Dive into Crossplane Essentials"
title: "Mastering Crossplane: Your Ultimate Guide to Seamless Control"
description: "Discover the transformative capabilities of Crossplane, the ultimate extension of Kubernetes as a universal control plane. Dive into this tutorial to uncover its endless possibilities and kickstart your journey with Crossplane"
keywords: ['crossplane kubernetes','crossplane examples','crossplane vs terraform']
authors: ['Pawan Kumar']
published: 2024-03-26
modified_by:
  name: Pawan Kumar

---

Crossplane is an open-source extension designed to expand Kubernetes into a universal control plane. It enables you to orchestrate and oversee your entire infrastructure using familiar Kubernetes tools. With Crossplane, you gain the ability to interact with virtually any cloud platform API, including Utho Cloud's, while enjoying features like API abstractions and access control akin to Kubernetes.

This guide provides insights into what Crossplane is and offers comparisons with similar tools. Additionally, it walks you through setting up your own Crossplane instance with easy-to-follow, step-by-step instructions.

## What Is Crossplane?

[Crossplane](https://www.crossplane.io/) revolutionizes cloud management by creating control planes across virtually any cloud platform. By extending Kubernetes into a universal control plane, Crossplane enables you to provision and oversee your entire infrastructure using familiar Kubernetes manifests and APIs.

Thanks to its seamless integration with external APIs, Crossplane effortlessly interacts with various platforms, whether you're deploying to a major cloud provider, [ordering a pizza](https://blog.crossplane.io/providers-101-ordering-pizza-with-kubernetes-and-crossplane/), or harnessing other orchestration tools [like Terraform](https://github.com/upbound/provider-terraform).

### What Are Control Planes?

In the realm of cloud computing, a control plane serves as a gateway for creating resources and overseeing their lifecycles. It essentially handles resource orchestration within cloud infrastructures.

An exemplary instance is Kubernetes. In a Kubernetes environment, resources are deployed and monitored, with their states managed to align with a specified configuration.

Now, Crossplane steps in to broaden the scope of Kubernetes' control plane. While Kubernetes operates as a control plane for resources within its own cluster, Crossplane expands this concept into a universal control plane. Essentially, Crossplane serves as the interface for managing resources across almost any cloud platform imaginable.

### Crossplane vs. Terraform: Exploring the Contrasts

[Terraform](https://www.terraform.io/) stands out as a leading tool for infrastructure-as-code, offering robust management capabilities across various services, much like Crossplane. But what distinguishes Crossplane from Terraform, and when should you opt for one over the other?

At its core, the disparity lies in the intended purposes of the tools. Terraform serves as a command-line utility for deploying infrastructure via declarative configurations. In contrast, Crossplane acts as a control plane, utilizing declarative configurations to create and manage infrastructure continuously.

Moreover, two key functional differences emerge between the tools, significantly impacting their integration within organizational and team structures:

- Configuration Management: Terraform configurations do not automatically update with changes in the deployed infrastructure. Updates to Terraform configurations necessitate reapplying the entire deployment. On the other hand, Crossplane's control plane actively maintains and adjusts the infrastructure state based on its declarative configurations, avoiding configuration "drift."

- Developer Collaboration: While Terraform requires collaborating developers to possess knowledge of both Terraform and the underlying API, along with granular access to resources, Crossplane offers self-service interfaces. Through operators handling credentials and abstracted interfaces, Crossplane simplifies access for developers, requiring familiarity only with the interface itself.

Additionally, Crossplane's integration with Kubernetes makes it an attractive option for teams already leveraging Kubernetes infrastructure, leveraging existing familiarity and infrastructure.

## Installing Crossplane: A Step-by-Step Guide

Setting up Crossplane on a Kubernetes cluster follows a similar process to deploying many other Kubernetes applications. After installing Crossplane on your cluster, you can leverage `kubectl` to create and administer Crossplane resources.

This section outlines the steps to establish a Kubernetes cluster and deploy Crossplane onto it. Follow along to get Crossplane up and running in no time.

### Establishing a Kubernetes Cluster: Step-by-Step Guide

To begin using Crossplane, you'll need a functioning Kubernetes cluster up and running.

If you're using Utho, you can swiftly deploy a Kubernetes cluster via the Cloud Manager. Simply follow the steps outlined in our guide Utho Kubernetes Engine - Getting Started. Once completed, you'll have a fully operational Kubernetes cluster and a configured `kubectl` instance ready for management.

In addition to having an active Kubernetes cluster, ensure that `kubectl` is configured correctly for managing it. You can find detailed instructions on this in the LKE guide mentioned above.

Furthermore, you'll need to install Helm on your system. Refer to the relevant section of our guide [Installing Apps on Kubernetes with Helm 3](/docs/guides/how-to-install-apps-on-kubernetes-with-helm-3/#install-helm) for instructions.

### Deploying Crossplane with Helm

Now that Kubernetes is up and running and Helm is installed, you're ready to proceed with the installation of Crossplane.

1.  Include the Crossplane repository in your Helm instance by executing the following command:

    ```command
    helm repo add crossplane-stable https://charts.crossplane.io/stable
    helm repo update
    ```

    ```output
    Hang tight while we grab the latest from your chart repositories...
    ...Successfully got an update from the "crossplane-stable" chart repository
    Update Complete. ⎈Happy Helming!⎈
    ```

If you want to personalize your installation, click on the link provided to make a 'values.yaml' file with your preferred settings. After that, add -f values.yml at the end of the installation command to use your custom configuration.

1. To install Crossplane in its own Kubernetes namespace, use the following command as recommended by the official guidelines:

    ```command
    helm install crossplane --namespace crossplane-system --create-namespace crossplane-stable/crossplane
    ```

2. To make sure Crossplane is properly installed, check the Crossplane pods in your cluster. It might take a little time for the pods to be ready, so you might need to wait until you see the `Running` status.

    ```command
    kubectl get pods --namespace crossplane-system
    ```

    ```output
    NAME                                      READY   STATUS    RESTARTS   AGE
    crossplane-766d6647bc-b57lz               1/1     Running   0          44s
    crossplane-rbac-manager-f94699c7c-zvvtb   1/1     Running   0          44s
    ```

## How to Use Crossplane

Now that Crossplane is up and running on your cluster, you're all set to begin using its control planes to deploy your infrastructure. With Crossplane being a versatile control plane, it can manage a wide array of external APIs.

To kick things off, this section presents a complete example. It demonstrates how to utilize the Utho provider for Crossplane to deploy a new Utho Compute instance.

Although this example is straightforward, it serves as a solid foundation. You can expand upon these configurations effortlessly to tailor Crossplane for various infrastructure requirements.

{{< note type="warning" >}}
The setups and commands outlined in this guide will add one or more Utho instances to your account. Make sure to keep a close eye on your account through the Utho Cloud Manager to prevent any unexpected charges.
{{< /note >}}

1. The provider enables you to deploy Utho Cloud instances using Crossplane.

1. Make a deployment manifest, like `provider.yml`, to install the Utho provider (`provider-utho`) onto your Crossplane instance.

    ```file{title="provider.yml" lang="yaml"}
    apiVersion: pkg.crossplane.io/v1
    kind: Provider
    metadata:
      name: provider-utho
    spec:
      package: xpkg.upbound.io/utho/provider-utho:v0.0.10
    ```

Explore the extensive selection of providers compatible with Crossplane on the [Upbound Marketplace](https://marketplace.upbound.io/providers). Upbound, the creators of Crossplane, oversee this repository of providers.

1. Use the newly created manifest to install `provider-utho` onto your Kubernetes cluster.

    ```command
    kubectl apply -f provider.yml
    ```

    ```output
    provider.pkg.crossplane.io/provider-utho created
    ```

    Verify the installation with a `kubectl` command to list `provider` resources:

    ```command
    kubectl get providers
    ```

    ```output
    NAME              INSTALLED   HEALTHY   PACKAGE                                         AGE
    provider-utho   True        True      xpkg.upbound.io/utho/provider-utho:v0.0.7   15s
    ```

1.  To deploy a new Utho Compute instance using Crossplane and the Utho provider, create a Kubernetes manifest file (e.g., `deployment.yml`). This is where you begin utilizing Crossplane's capabilities.

In this example manifest, you'll need to replace specific values with your own credentials and preferences:

- Replace `${ROOT_PASSWORD}` with the root password you want for the new Utho Compute instance.

- Replace `${UTHO_API_TOKEN}` with your Utho API personal access token, which you can generate by following the relevant section of our guide on Getting Started with the Utho API.

- Replace `${SSH_PUBLIC_KEY}` with a public SSH key to access the Utho Compute instance. Learn more about SSH keys in our guide Using SSH Public Key Authentication.

    ```file{title="deployment.yml" lang="yaml" hl_lines="8,11,37"}
    apiVersion: v1
    kind: Secret
    metadata:
      name: crossplane-secrets
      namespace: crossplane-system
    type: Opaque
    stringData:
      uthoRootPass: ${ROOT_PASSWORD}
      uthoCredentials: |
        {
          "token": "${LINODE_API_TOKEN}"
        }
    ---
    apiVersion: utho.upbound.io/v1beta1
    kind: ProviderConfig
    metadata:
      name: default
    spec:
      credentials:
        source: Secret
        secretRef:
          name: crossplane-secrets
          namespace: crossplane-system
          key: uthoCredentials
    ---
    apiVersion: instance.utho.upbound.io/v1alpha1
    kind: Instance
    metadata:
      annotations:
        meta.upbound.io/example-id: instance/v1alpha1/instance
      labels:
        testing.upbound.io/example-name: web
      name: web
    spec:
      forProvider:
        authorizedKeys:
        - ssh-rsa ${SSH_PUBLIC_KEY}
        image: utho/ubuntu20.04
        type: g6-standard-1
        label: crossplane-deployment-example
        region: us-southeast
        rootPassSecretRef:
          key: uthoRootPass
          name: crossplane-secrets
          namespace: crossplane-system
    ```

This manifest contains three sections, each serving a specific purpose:

- The Secret resource named crossplane-secrets stores essential variables like your instance's root password and Utho API token. Other resources can access and utilize these variables later.

- The ProviderConfig resource offers initial configurations for subsequent resources. For the Utho provider, it mainly involves specifying the secret containing the Utho API token.

- The Instance resource named web outlines the specifications for the new Utho Compute instance to be generated. In this example, it creates a 2GB shared instance in Atlanta, running Ubuntu 20.04 LTS.

        Use the API endpoints to learn more about available instance [types](https://api.utho.com/v4/utho/types), [regions](https://api.utho.com/v4/regions), and [images](https://api.utho.com/v4/images).

1.  Apply the deployment manifest to the Kubernetes cluster in the same way you deploy standard resources.

    ```command
    kubectl apply -f deployment.yml
    ```

    ```output
    secret/crossplane-secrets created
    providerconfig.utho.upbound.io/default created
    instance.instance.utho.upbound.io/web created
    ```

1.  Confirm that the instance has been deployed by using `kubectl` to retrieve list of `instance` resources.

    ```command
    kubectl get instances
    ```

    At first, you should see output like this, indicating that the instance provisioning has not yet completed:

    ```output
    NAME   READY   SYNCED   EXTERNAL-NAME   AGE
    web    False   True                     33s
    ```

Wait briefly and then try the command again. You should observe the instance status change to `READY`, indicating that your Compute instance has been successfully provisioned.

    ```output
    NAME   READY   SYNCED   EXTERNAL-NAME   AGE
    web    True    True     45521497        2m46s
    ```

To double-check the successful deployment, go to the Utho Cloud Manager. Navigate to the Uthos section, where you should find the new Compute instance listed. Referring to the example deployment.yml provided earlier, the new instance would be named crossplane-deployment-example.

## Conclusion

Now you're all set to begin using Crossplane as a universal control plane. Deploying the Utho instance in the example above gives you a glimpse of how Crossplane manages infrastructure. Exploring further with the deployment manifest can spark more ideas about Crossplane's capabilities.

Crossplane is highly adaptable and offers features to meet various infrastructure needs. One valuable aspect to explore is Kubernetes's [role-based access control (RBAC)](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) features, which Crossplane can leverage for its control planes.

To deepen your understanding of your Crossplane instance, check out Crossplane's [introduction](https://docs.crossplane.io/v1.12/getting-started/introduction/) documentation. It covers essential concepts for maximizing your control planes. For additional information, refer to the full Crossplane documentation linked below.
