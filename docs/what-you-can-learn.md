# What You Can Learn

This repo is meant to be an example of how to do the GitOps thing in a number of different ways and permutations so you can learn a lot of different techniques and when to employ them and how to flip a few switches to make things better.

## Table of Contents

### ArgoCD / Red Hat OpenShift GitOps

- **[ArgoCD Sync Waves](https://github.com/kenmoini/multiverse-of-multicluster-madness/blob/main/rhacm/policy-generators/install-rhacm-operator/manifests/00_namespaces.yml#L7)** - Sync different YAML manifests in different orders
- **[Installing Helm Charts via ArgoCD Applications](https://github.com/kenmoini/multiverse-of-multicluster-madness/tree/main/hub-of-hubs/gitops-apps/reflector-chart-installed)** - ArgoCD can sync in Helm Charts and your custom values for them too!
- Configuring RH GitOps/ArgoCD Operator to use Kustomize, and optionally alternative versions of it
- Configuring RH GitOps/ArgoCD Operator to use Kustomize with Helm