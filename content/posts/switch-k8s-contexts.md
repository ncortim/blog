---
title: 'Switch K8s Contexts'
date: 2024-11-05T17:07:20+01:00
draft: true
description:
tags:
  - kubernetes
  - k8s-contexts
---

# How to Switch Kubernetes Contexts When Managing Multiple Clusters

**Answer:** To switch between multiple Kubernetes clusters, you’ll need a Kubernetes configuration file (e.g., `~/.kube/config`) that contains access credentials for each cluster. This configuration file can hold multiple Kubernetes context configurations, combining access details from several `kubeconfig` files.

For more information on setting up configuration files, refer to the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/).

## What You Need to Do

1. **Check your Client and Server Versions**

   Begin by verifying the version of your Kubernetes client (`kubectl`) and server (e.g., `kubeadm`). This ensures compatibility between the versions.

   ```bash
   $ kubectl version --client
   ```

   The output of this command should look something like:

   ```bash
   Client Version: v1.27.3
   Kustomize Version: v5.0.1
   Server Version: v1.30.0
   ```

   **Warning:** The difference between the client and server versions should not exceed the supported minor version skew of ±1. 

   In this example, the client version (1.27) and server version (1.30) exceed the supported version skew. Therefore, updating the `kubectl` client is necessary. Find more information on this in the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).

2. **Make Sure You Have Access to Multiple Clusters**

   You’ll need access to two or more Kubernetes clusters to benefit from switching contexts.

3. **Verify the `KUBECONFIG` Environment Variable**

   Ensure that your `KUBECONFIG` environment variable points to all relevant configuration files, like so:

   ```bash
   $ env | grep -i kub
   KUBECONFIG=/home/user/.kube/config-prod:/home/user/.kube/config-dev
   ```

   Alternatively, you can use the `kubectl` command to confirm that all configurations are loaded:

   ```bash
   $ kubectl config view
   apiVersion: v1
   clusters:
   - cluster:
       certificate-authority-data: DATA+OMITTED
       server: https://my-dev-cluster
     name: dev-cluster
   - cluster:
       certificate-authority-data: DATA+OMITTED
       server: https://my-prod-cluster
     name: prod-cluster
   contexts:
   - context:
       cluster: dev-cluster
       namespace: default
       user: developer
     name: development
   - context:
       cluster: prod-cluster
       user: producer
     name: production
   current-context: ""
   kind: Config
   preferences: {}
   users:
   - name: developer
     user:
       client-certificate-data: DATA+OMITTED
       client-key-data: DATA+OMITTED
   - name: producer
     user:
       client-certificate-data: DATA+OMITTED
       client-key-data: DATA+OMITTED
   ```

   Since the `KUBECONFIG` environment variable is set, `kubectl` recognizes and displays both configurations.

4. **Merge Configuration Files into a Single File**

   To simplify switching contexts, merge multiple configuration files into one. The `kubectl` command makes this easy:

   ```bash
   $ kubectl config view --merge --flatten > ~/.kube/config-merged
   ```

   This command does the following:
   - `--merge`: Combines the configurations from multiple files.
   - `--flatten`: Produces a standalone, portable configuration file.

   Once the merged `KUBECONFIG` is created, you can easily switch contexts using the `kubectl` client, or with tools like [`kubectx`](https://github.com/ahmetb/kubectx) and [`k9s`](https://k9scli.io).

**Note:** If you need to create a configuration file from scratch, this can also be done with the `kubectl` client. Detailed steps are available in the [Kubernetes documentation](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/).
