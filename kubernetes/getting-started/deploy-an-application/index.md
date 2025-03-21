---
weight: 11
title: Deploy An Application to Kubernetes Cluster 
description: "Learn how to deploy application on kubernetes cluster"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/kubernetes/getting-started/deploy-an-application']
icon: 'kubernetes'
tab: true
---
This guide outlines the steps to deploy an application to a Kubernetes cluster managed by Utho, along with example outputs for each command to help verify execution.

---

## Prerequisites

To follow this guide, you must:

* [Install docker](https://docs.docker.com/engine/install/ "https://docs.docker.com/engine/install/"), the docker command-line tool
* [Install kubectl](https://kubernetes.io/docs/tasks/tools/ "https://kubernetes.io/docs/tasks/tools/"), the Kubernetes command-line tool.

#### **1. Access the Kubernetes Cluster**

* Download the `kubeconfig` file from the cluster management section on Utho’s platform.
* Use the following command to configure your local system to use the Kubernetes cluster:

  ```bash
  export KUBECONFIG=/path/to/kubeconfig
  ```
* Verify the cluster connection:

  ```bash
  kubectl cluster-info
  ```

  **Sample Output** :

  ```
  Kubernetes control plane is running at https://123.45.67.89:6443
  CoreDNS is running at https://123.45.67.89:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
  ```

---

#### **2. Prepare Your Application for Deployment**

* Ensure your application is containerized. Build and push your application image to a container registry.
  Example Docker commands:

  ```bash
  docker build -t <your-image-name>:<tag> .
  docker push <registry-url>/<your-image-name>:<tag>
  ```

  **Sample Output** :

  ```
  Sending build context to Docker daemon  10.24kB
  Step 1/4 : FROM nginx
  ---> 4bb46517cac3
  Step 2/4 : COPY . /usr/share/nginx/html
  ---> Using cache
  Step 3/4 : EXPOSE 80
  ---> Using cache
  Step 4/4 : CMD ["nginx", "-g", "daemon off;"]
  ---> Using cache
  Successfully built d9f2e4d3a356
  Successfully tagged <registry-url>/<your-image-name>:<tag>

  The push refers to repository [<registry-url>/<your-image-name>]
  abc1234: Pushed
  latest: digest: sha256:abcdef1234567890 size: 1573
  ```

---

#### **3. Create Kubernetes Deployment YAML**

* Prepare a YAML file for your application’s deployment and service.
  **`deployment.yaml`**
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: my-app
    labels:
      app: my-app
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: my-app
    template:
      metadata:
        labels:
          app: my-app
      spec:
        containers:
        - name: my-app-container
          image: <registry-url>/<your-image-name>:<tag>
          ports:
          - containerPort: 80
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: my-app-service
  spec:
    selector:
      app: my-app
    ports:
      - protocol: TCP
        port: 80
        targetPort: 80
    type: LoadBalancer
  ```

---

#### **4. Apply the YAML File**

* Deploy your application using the YAML file:

  ```bash
  kubectl apply -f deployment.yaml
  ```

  **Sample Output** :

  ```
  deployment.apps/my-app created
  service/my-app-service created
  ```
* Verify the deployment and service:

  ```bash
  kubectl get deployments
  ```

  **Sample Output** :

  ```
  NAME      READY   UP-TO-DATE   AVAILABLE   AGE
  my-app    3/3     3            3           2m
  ```

  ```bash
  kubectl get pods
  ```

  **Sample Output** :

  ```
  NAME                     READY   STATUS    RESTARTS   AGE
  my-app-658f89f469-abcde  1/1     Running   0          2m
  my-app-658f89f469-fghij  1/1     Running   0          2m
  my-app-658f89f469-klmno  1/1     Running   0          2m
  ```

  ```bash
  kubectl get services
  ```

  **Sample Output** :

  ```
  NAME              TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
  my-app-service    LoadBalancer   10.96.245.195   123.45.67.89     80:30382/TCP   2m
  ```

---

#### **5. Monitor the Deployment**

* Check the logs of your application pods to ensure everything is working correctly:

  ```bash
  kubectl logs -f <pod-name>
  ```

  **Sample Output** :

  ```
  Starting server on port 80
  Server running at http://0.0.0.0:80
  ```
* Monitor the health of your application:

  ```bash
  kubectl get pods --watch
  ```

  **Sample Output** :

  ```
  NAME                     READY   STATUS    RESTARTS   AGE
  my-app-658f89f469-abcde  1/1     Running   0          5m
  my-app-658f89f469-fghij  1/1     Running   0          5m
  my-app-658f89f469-klmno  1/1     Running   0          5m
  ```

---

#### **6. Access Your Application**

* Note the external IP of the LoadBalancer service:

  ```bash
  kubectl get service my-app-service
  ```

  **Sample Output** :

  ```
  NAME              TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
  my-app-service    LoadBalancer   10.96.245.195   123.45.67.89     80:30382/TCP   2m
  ```
* Test the application:

  ```bash
  curl http://123.45.67.89
  ```

  **Sample Output** :

  ```
  Welcome to My App!
  ```

---

#### **7. Scale the Application (Optional)**

* To scale the number of replicas:

  ```bash
  kubectl scale deployment my-app --replicas=5
  ```

  **Sample Output** :

  ```
  deployment.apps/my-app scaled
  ```
* Verify the scaling:

  ```bash
  kubectl get pods
  ```

  **Sample Output** :

  ```
  NAME                     READY   STATUS    RESTARTS   AGE
  my-app-658f89f469-abcde  1/1     Running   0          2m
  my-app-658f89f469-fghij  1/1     Running   0          2m
  my-app-658f89f469-klmno  1/1     Running   0          2m
  my-app-658f89f469-pqrst  1/1     Running   0          30s
  my-app-658f89f469-uvwxy  1/1     Running   0          30s
  ```

---

#### **8. Update the Application (Optional)**

* Update the application with a new container image:

  ```bash
  kubectl set image deployment/my-app my-app-container=<new-image-url>
  ```

  **Sample Output** :

  ```
  deployment.apps/my-app image updated
  ```
* Monitor the rollout status:

  ```bash
  kubectl rollout status deployment/my-app
  ```

  **Sample Output** :

  ```
  deployment "my-app" successfully rolled out
  ```

---

#### **9. Clean Up Resources**

* To delete the deployment and service:

  ```bash
  kubectl delete -f deployment.yaml
  ```

  **Sample Output** :

  ```
  deployment.apps "my-app" deleted
  service "my-app-service" deleted
  ```
* Verify resource deletion:

  ```bash
  kubectl get all
  ```

  **Sample Output** :

  ```
  No resources found in default namespace.
  ```

---

By following these steps and verifying with the sample outputs, you can successfully deploy your application on Utho’s Kubernetes cluster.
