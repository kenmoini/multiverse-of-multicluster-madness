# Dr. Kemo and the Multiverse of Multi-cluster Madness

**Doing a Docker?**  *That's cool*
**Kicking off some Kubernetes?**  *That's cool too*
**Operating an OpenShift?** *Now we're cooking with grease*
**Messing with multiple clusters?** *That's where the fun begins*

## What is this?

This repository is a collection of GitOps-centric resources to manage Kubernetes/OpenShift - and even more so, multiple clusters, without descending into psychosis.  This isn't an end-all-be-all or gospel for how to do everything, but rather a collection of blueprints and patterns that can be used to build out a GitOps-centric multi-cluster management strategy of your own.

## What's in the box?

### [Hub Bootstrapping]

Most mutli-cluster architectures operate in a "Hub and Spoke" model, and can be extended even further by operating a "Hub of Hubs" pattern.  In an OpenShift context, there are many ways to bootstrap a Hub cluster, and arguably the method with the lowest level of effort and maintenance would be to use the OpenShift GitOps Operator (ArgoCD) to bootstrap things.  So then all you have to do is:

- Start with a fresh OpenShift cluster
- `oc apply -f hub-bootstrap/`

From there, ArgoCD will sync things to that repo in order to install Red Hat Advanced Cluster Management and a Basic MultiClusterHub.  You could add additional things to the `hub-bootstrap/` directory to install other things, however from here forward this repository will leverage RHACM to manage the clusters.

### Hub Configuration

The `hub-config/` directory contains a collection of resources that can be used to configure the Hub cluster.  This includes things like:

- Configuration of Governance Policies that are applied to the Hub local-cluster that do things such as:
  - Enforce installation of the Ansible Automation 2 Platform Operator
  - Enforce installation of the Red Hat Quay Operator
  - Enforce installation of the Red Hat Advanced Cluster Security for Kubernetes Operator
  - Configuration of the RHACS Central CR



  - Configuration of the RHACS SecuredCluster CR