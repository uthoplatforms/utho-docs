---
weight: 20
title: "Loadbalancer"
title_meta: "Manage kubernetes on the Utho Platform"
description: "Manage your kubernetes instance using simply clicks on utho platform"
keywords: ["kubernetes", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","kubernetes"]
date: "2024-11-19T17:25:05+01:00"
lastmod: "2024-11-19T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/kubernetes/manage-kubernetes/utho-kubernetes-controller-loadbalancer']
icon: "vpc"
tab: true
---
# How to Add Load Balancers to Kubernetes Clusters

The Utho Cloud Controller supports provisioning Utho Load Balancers in a cluster’s resource configuration file.

The Utho Load Balancer Service routes load balancer traffic to all worker nodes on the cluster. Only nodes configured to accept the traffic pass health checks.

Utho Application Operator is a [Kubernetes Operator](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/ "https://kubernetes.io/docs/concepts/extend-kubernetes/operator/") that is used to manage various resources required by your Kubernetes Based Application like Load Balancer, etc.

With this Operator, you can do the following:

* Create a CRD called **UthoApplication** and provide networking parameters.
* Manage your resources from the CRD
* Hassle Free Netwokr Resource Provisioning.

## Install the controller

### Prerequisites

* You need to have [Helm CLI](https://helm.sh/docs/helm/helm_install/ "https://helm.sh/docs/helm/helm_install/") installed on your machine.
* You must have an [Utho API Key](https://console.utho.com/api "https://console.utho.com/api").

### Install the Cloud Controller Helm Chart

To install the chart with the release name `utho-app-operator`:

Add the Utho Operator Repository to your Helm repositories:

```
helm repo add utho-operator https://uthoplatforms.github.io/utho-app-operator-helm/
```

```
helm repo update
```

Install the Utho Operator Chart:

Note: make sure to set the Utho API Key

```shell
helm install <release_name> utho-operator/utho-app-operator-chart --set API_KEY=<YOUR_API_KEY> -n <namespace> --create-namespace
```

Example:

```shell
helm install utho-app-operator utho-operator/utho-app-operator-chart --set API_KEY=################## -n dev --create-namespace
```

## Create a Configuration File for Network Loadblancer

You can update the backend port that the Loadblancer will listen to

```
apiVersion: apps.utho.com/v1alpha1
kind: UthoApplication
metadata:
  name: nl-lb
spec:
  loadBalancer:
    name: nl-lb
    dcslug: innoida
    # Node exposed port the loadblancer will forwad request to
    backendPort: 30088
    frontend:
      name: nl-lb
      algorithm: roundrobin
      protocol: tcp
      # public port the Loadblancer wil listen to
      port: 80
    type: network
```

## Show Network Load Balancers

Once you apply the config file to a deployment, you can see the load balancer in the Resources tab of your cluster in the control panel.

Alternatively, use kubectl get services to see its status:

``kubectl get UthoApplication``

| NAME  | PHASE   | LOAD-BALANCER-IP | LOAD-BALANCER-TYPE | FRONTEND-PORT | AGE |
| ----- | ------- | ---------------- | ------------------ | ------------- | --- |
| nl-lb | RUNNING | 111.111.111.111  | network            | 80            | 2h  |

When the load balancer creation is complete, the LOAD-BALANCER-IP column displays the external IP address instead of empty. In the PORT column.

## Show Details for One Network Load Balancer

If the provisioning process for the load balancer is unsuccessful, you can access the service’s event stream to troubleshoot any errors. The event stream includes information on provisioning status and reconciliation errors.

To get detailed information about the load balancer configuration of a single load balancer, including the event stream at the bottom, use kubectl’s describe service command:

``kubectl describe UthoApplication <LB-NAME>``

## More examples

You can find more examples at [Examples](https://github.com/uthoplatforms/utho-cloud-controller-manager/blob/main/README.md)

## Create a Configuration File for Application Loadblancer

You can update the backend port that the Loadblancer will listen to

```
apiVersion: apps.utho.com/v1alpha1
kind: UthoApplication
metadata:
  name: ap-lb
  namespace: dev
spec:
  loadBalancer:
    advancedRoutingRules:
      - aclName: ap-lb
        routeCondition: true
        targetGroupNames:
          - ap-lb
    frontend:
      name: ap-lb
      algorithm: roundrobin
      protocol: tcp
      # public port the Loadblancer wil listen to
      port: 80
    type: application
    dcslug: innoida
    name: ap-lb
  targetGroups:
    - health_check_timeout: 5
      health_check_interval: 30
      health_check_path: /
      health_check_protocol: TCP
      healthy_threshold: 2
      name: ap-lb
      protocol: TCP
      unhealthy_threshold: 3
      # Node exposed port the loadblancer will forwad request to
      port: 30088

```

## Show Application Load Balancers

Once you apply the config file to a deployment, you can see the load balancer in the Resources tab of your cluster in the control panel.

Alternatively, use kubectl get services to see its status:

``kubectl get UthoApplication``

| NAME  | PHASE   | LOAD-BALANCER-IP | LOAD-BALANCER-TYPE | FRONTEND-PORT | AGE |
| ----- | ------- | ---------------- | ------------------ | ------------- | --- |
| ap-lb | RUNNING | 111.111.111.111  | network            | 80            | 2h  |

When the load balancer creation is complete, the LOAD-BALANCER-IP column displays the external IP address instead of empty. In the PORT column.

## Show Details for One Application Load Balancer

If the provisioning process for the load balancer is unsuccessful, you can access the service’s event stream to troubleshoot any errors. The event stream includes information on provisioning status and reconciliation errors.

To get detailed information about the load balancer configuration of a single load balancer, including the event stream at the bottom, use kubectl’s describe service command:

``kubectl describe UthoApplication <LB-NAME>``

## UthoApplication CRD Reference

<article id="mainspec"></article>

### UthoApplicationSpec

Defines the desired state of the UthoApplication.

| FIELD          | DESCRIPTION                                                   |
| -------------- | ------------------------------------------------------------- |
| `apiVersion` | `apps.utho.com/v1alpha1`                                    |
| `kind`       | `UthoApplication`                                           |
| `metadata`   | Refer to Kubernetes API documentation for fields of metadata. |
| `spec`       | `UthoApplicationSpec`                                       |

#### UthoApplicationSpec

Specifies the UthoApplication configuration.

| FIELD            | DESCRIPTION       |
| ---------------- | ----------------- |
| `loadBalancer` | `LoadBalancer`  |
| `targetGroups` | `[]TargetGroup` |

<article id="lb"></article>

#### LoadBalancer

Specifies the load balancer configuration.

| FIELD        | DESCRIPTION                          | EXAMPLE VALUES  |
| ------------ | ------------------------------------ | --------------- |
| `frontend` | `Frontend`, optional               |                 |
| `type`     | `string`, default: `application` | `application` |
| `dcslug`   | `string`                           | `innoida`     |
| `name`     | `string`                           | `my-lb`       |
| `aclRule`  | `[]ACLRule`, optional              |                 |

<article id="fe"></article>

#### Frontend

Specifies the frontend configuration.

| FIELD               | DESCRIPTION          | EXAMPLE VALUES                  |
| ------------------- | -------------------- | ------------------------------- |
| `name`            | `string`           | `test-fe`                     |
| `algorithm`       | `string`           | `roundrobin` or `leastconn` |
| `protocol`        | `string`           | `http` or `https`           |
| `port`            | `int64`            | `80` or `443`               |
| `certificateName` | `string`, optional | test-name                       |
| `redirectHttps`   | `bool`, optional   | `0` for no, `1` for yes     |
| `cookie`          | `bool`, optional   | `0` for no, `1` for yes     |

<article id="acl"></article>

#### ACLRule

Specifies an ACL rule.

| FIELD             | DESCRIPTION | EXAMPLE VALUES                                                                                          |
| ----------------- | ----------- | ------------------------------------------------------------------------------------------------------- |
| `name`          | `string`  | `test-rule`                                                                                           |
| `conditionType` | `string`  | `http_user_agent`, `http_referer`, `url_path`, `http_method`, `query_string`, `http_header` |
| `value`         | `ACLData` |                                                                                                         |

#### ACLData

Specifies the ACL data.

| FIELD    | DESCRIPTION  | EXAMPLE VALUES                                                                                          |
| -------- | ------------ | ------------------------------------------------------------------------------------------------------- |
| `type` | `string`   | `http_user_agent`, `http_referer`, `url_path`, `http_method`, `query_string`, `http_header` |
| `data` | `[]string` | `/`                                                                                                   |

<article id="tg"></article>

#### TargetGroup

Specifies a target group configuration.

| FIELD                     | DESCRIPTION | EXAMPLE VALUES                        |
| ------------------------- | ----------- | ------------------------------------- |
| `name`                  | `string`  | `test-tg`                           |
| `protocol`              | `string`  | `HTTP`, `TCP`, `HTTPS`, `UDP` |
| `health_check_path`     | `string`  | `/healthz`                          |
| `health_check_protocol` | `string`  | `HTTP`, `TCP`, `HTTPS`, `UDP` |
| `health_check_interval` | `int64`   | `30`                                |
| `health_check_timeout`  | `int64`   | `5`                                 |
| `healthy_threshold`     | `int64`   | `3`                                 |
| `unhealthy_threshold`   | `int64`   | `2`                                 |
| `port`                  | `int64`   | `80`                                |
