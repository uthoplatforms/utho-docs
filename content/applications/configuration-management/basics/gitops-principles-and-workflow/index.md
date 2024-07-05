---
slug: "Understanding GitOps Principles and Workflow"
description: 'This guide offers a comprehensive overview of GitOps, detailing its workflow, and explores its distinctions from DevOps and Kubernetes'
keywords: ['gitops vs devops', 'gitops and kubernetes', 'gitops workflow']
tags: ['kubernetes', 'container', 'monitoring']
published: 2024-03-21
modified_by:
  name: Utho
title: "GitOps: Principles and Workflow Explained"
title_meta: "An Overview of GitOps Principles and Workflow"
authors: ["Pawan Kumar"]
---
If you're a developer, chances are you're acquainted with [Git](/docs/guides/a-beginners-guide-to-github/). But what about GitOps? This guide breaks down GitOps, compares it to DevOps, outlines the GitOps workflow, and discusses the common tools used in this approach.

## What is GitOps?

GitOps is a paradigm that merges the responsibilities of developers and operations teams. Traditionally, operations handle tasks like technology management, quality assurance, and network administration. Developers typically work separately from operations. GitOps breaks down these silos by allowing operations to use the same tools and methods as developers, fostering collaboration. With GitOps, Git serves as the central source of truth for all code, including operations-related configurations. This approach is made possible by [Infrastructure as Code (IaC)](/docs/guides/introduction-to-infrastructure-as-code/) tools, which enable the creation and management of infrastructure using declarative configuration files.

## GitOps Principles

GitOps relies on version control tools like [Git](/docs/guides/how-to-use-git/), [GitHub](/docs/guides/a-beginners-guide-to-github/), [GitLab](/docs/guides/install-gitlab-on-ubuntu-18-04/), Bitbucket. These platforms serve as centralized repositories for your Infrastructure as Code (IaC) and orchestration files.

In GitOps, the desired state of a system is described using declarative specifications for every environment in the software development lifecycle, such as testing, staging, and production. Declarative configuration files reside within the same repository as the code, enabling access for all relevant project members.

Another crucial aspect of GitOps is observability. Observability refers to the ability to measure the internal state of a system by analyzing its output. While monitoring observes a system over time, observability assesses how well a system's state can be understood or inferred from external outputs. Monitoring focuses on predefined metrics, whereas observability allows users to determine what to monitor based on the system's performance over time.

The three fundamental components of GitOps include:

- [Infrastructure as Code](/docs/guides/introduction-to-infrastructure-as-code/): This methodology stores all infrastructure configuration as code, enabling consistent and reproducible infrastructure deployment.
- [Merge Requests](/docs/guides/resolving-git-merge-conflicts/): These serve as a mechanism for proposing and reviewing changes to infrastructure configurations, fostering collaboration and ensuring proper change management.
- [Continuous Integration/Continuous Delivery](/docs/guides/introduction-ci-cd/): CI/CD automates the building, testing, and deployment of applications and services, streamlining the software delivery process and ensuring reliability and efficiency.

## GitOps vs. DevOps

GitOps incorporates the best practices of DevOps into infrastructure automation, emphasizing version control, collaboration, compliance, and CI/CD. With tools like Kubernetes streamlining the software development lifecycle, businesses increasingly rely on container deployment for scalability, often leveraging cloud-based services for infrastructure hosting. This shift has spurred the adoption of infrastructure automation to achieve the elasticity required for modern applications.

While DevOps automates the software development lifecycle, GitOps focuses on automating infrastructure management. There are notable distinctions between GitOps and DevOps. GitOps utilizes Git for infrastructure provisioning and software deployment, whereas DevOps is tool-agnostic, emphasizing CI/CD. GitOps prioritizes the correctness of operations, unlike DevOps, which places less emphasis on correctness. Additionally, GitOps tends to be less flexible compared to DevOps. However, GitOps adoption is smoother in organizations already employing DevOps practices.

## GitOps and Kubernetes

GitOps is particularly well-suited for businesses utilizing Kubernetes, as it streamlines infrastructure automation. When combining GitOps with Kubernetes:

- GitOps guarantees that everything functions as intended.
- Kubernetes ensures stability and availability.

[Kubernetes](/docs/products/compute/kubernetes/get-started/) continually monitors deployed applications or services to maintain a stable state and adjusts scaling as necessary. With GitOps integrated into the workflow, it ensures the proper functioning of all components, including the infrastructure required for deployments. GitOps acts as the bridge between application development and delivery (Kubernetes) and the environment where the application operates.

## The GitOps Workflow

The conventional application lifecycle typically follows this pattern:

- Design
- Build
- Image
- Test
- Deploy

When GitOps is introduced, the lifecycle transforms into the following:

- Design
- Build
- Image
- Test
- Monitor
- Log changes/events
- Alert when a change has occurred
- Update

In a Kubernetes-based workflow guided by GitOps principles, all essential code resides within a Git repository. Through automation, individuals with Kubernetes management privileges can initiate pull requests, modify code, and merge changes into the repository. Upon completion of a merge request, the automated GitOps operator identifies the modifications. Another automation mechanism verifies the operational viability of the change, which is then automatically deployed to the cluster.

This GitOps approach not only fosters extensive automation but also ensures a greater likelihood that each deployment functions precisely as intended.

## GitOps Tools

There are several tools useful to GitOps, some of those tools include:

- [Git](https://git-scm.com/) - A version control system for managing your code.
- [GitHub](https://github.com/) - A popular code repository hosting service.
- [Cloud Build](https://cloud.google.com/build) - Executes build processes using pre-packaged Docker containers, facilitating deployment lifecycle steps.
- [CircleCI](https://circleci.com/) -  A cloud-based build engine simplifying CI/CD workflows.
- [Jenkins X](https://jenkins-x.io/) - An open-source automation server offering testing tools and Kubernetes integration.
- [Kubernetes](https://kubernetes.io/) -  container orchestration platform seamlessly integrated with GitOps.
- [Helm](https://helm.sh/) - Facilitates Kubernetes resource configuration.
- [Flagger](https://flagger.app/) - Automates error detection in code and prevents faulty deployments.
- [Prometheus](https://prometheus.io/) - A robust monitoring tool for GitOps, capable of generating alerts detected by Flagger.
- [Quay](https://quay.io/) - An application registry and container image manager.
- [Flux](https://fluxcd.io/) -  A GitOps operator for Kubernetes that automatically adjusts cluster configurations based on Git repository contents.

## Conclusion

GitOps represents the evolution of infrastructure management, complementing DevOps and Kubernetes to enhance stability, efficiency, and reliability throughout the software development lifecycle. By implementing GitOps, businesses can achieve greater predictability and repeatability in their software and deployment processes, ultimately leading to increased profitability.
