# kubeflow-experiments <!-- omit in toc -->

# TOC <!-- omit in toc -->
- [1. Environment setup](#1-environment-setup)
  - [1.1. Installing Kubeflow on a local, single machine.](#11-installing-kubeflow-on-a-local-single-machine)
    - [1.1.1. Install Kubectl](#111-install-kubectl)
    - [1.1.2. Install Kind](#112-install-kind)
  - [1.2. Deploying Kubeflow pipelines](#12-deploying-kubeflow-pipelines)
  - [1.3. Uninstalling Kubeflow](#13-uninstalling-kubeflow)
  - [1.4. Deleting cluster](#14-deleting-cluster)
- [From Notebook to Kubeflow Pipelines with MiniKF and Kale](#from-notebook-to-kubeflow-pipelines-with-minikf-and-kale)
- [2. References](#2-references)

# 1. Environment setup

## 1.1. Installing Kubeflow on a local, single machine.

Reference: [[1](https://www.kubeflow.org/docs/components/pipelines/installation/localcluster-deployment/)]

### 1.1.1. Install Kubectl

Follow [Install using native package management](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management). Note: As of 2021-05-06, the native package management method installs the latest stable kubectl version.

### 1.1.2. Install Kind

```bash
# NOTE: This is for v0.10.0, 
# please follow:
# https://kind.sigs.k8s.io/docs/user/quick-start/#installation for latest instructions
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.10.0/kind-linux-amd64
chmod +x ./kind

# I moved it to ~/kind_0_10_0
mv ./kind ~/kind_0_10_0/kind
```

Add this directory to `PATH` by adding the following to ~/.bashrc
```bash
# Add this to ~/.bashrc
export PATH="~/kind_0_10_0:$PATH"
```

Create a Kind cluster:

```bash
kind create cluster
```

View cluster info:
```bash
kubectl cluster-info --context kind-kind
```

## 1.2. Deploying Kubeflow pipelines

Refer to [Deploying Kubeflow Pipelines](https://www.kubeflow.org/docs/components/pipelines/installation/localcluster-deployment/#deploying-kubeflow-pipelines) for latest instructions.

```bash
export PIPELINE_VERSION=1.5.0
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"
kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic-pns?ref=$PIPELINE_VERSION"
```

Verify that the Kubeflow Pipelines UI is accessible by port-forwarding:

```bash
kubectl port-forward -n kubeflow svc/ml-pipeline-ui 8080:80
```

Then, open the Kubeflow Pipelines UI at http://localhost:8080/

## 1.3. Uninstalling Kubeflow

If you used the above method to install Kubeflow, you can uninstall by:

```bash
export PIPELINE_VERSION=1.5.0
kubectl delete -k "github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic-pns?ref=$PIPELINE_VERSION"
kubectl delete -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"
```

## 1.4. Deleting cluster

```bash
# Get cluster names:
kind get clusters

#Delete cluster, specify --name if not default "kind"
kind delete cluster
```

# From Notebook to Kubeflow Pipelines with MiniKF and Kale

Reference: [[2](https://codelabs.developers.google.com/codelabs/cloud-kubeflow-minikf-kale#0)]



# 2. References

1. [Deploying Kubeflow Pipelines on a local cluster](https://www.kubeflow.org/docs/components/pipelines/installation/localcluster-deployment/)
2. [From Notebook to Kubeflow Pipelines with MiniKF and Kale](https://codelabs.developers.google.com/codelabs/cloud-kubeflow-minikf-kale#0)