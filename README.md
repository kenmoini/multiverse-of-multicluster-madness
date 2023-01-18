# Dr. Shadowman and the Multiverse of Multi-cluster Madness

**Doing a Docker?**  *That's cool*

**Kicking off some Kubernetes?**  *Yeah, cool too*

**Operating an OpenShift?** *Now we're cooking with grease*

**Messing with multiple clusters?** *This is where the madness begins*

## What is this?

This repository is a collection of GitOps-centric resources to manage Kubernetes/OpenShift - and even more so, multiple clusters in a hub-of-hubs architecture, without descending into psychosis.  This isn't an end-all-be-all or gospel for how to do everything, but rather a collection of blueprints and patterns that can be used to build out a GitOps-centric multi-cluster management strategy of your own.

## What's in the box?

### Directory Structure

```
📦hub-of-hubs
 ┣ 📂01_bootstrap - The first thing applied to the Hub of Hubs cluster
 ┃ ┣ 📂install-external-secrets - Installs OpenShift GitOps (ArgoCD)
 ┃ ┣ 📂install-hashicorp-vault - Installs Hashicorp Vault for Secrets Management
 ┃ ┗ 📂install-openshift-gitops - Installs External Secrets Operator
 ┣ 📂02_gitops-config - After Secrets seeding, this is applied to the Hub of Hubs cluster to start syncing configuration, policies, and workloads via ArgoCD
 ┃ ┣ 📂00_eso-config - Configures the External Secrets Operator to use the in-cluster Vault instance
 ┃ ┣ 📂01_deploy-openshift-gitops - Deploys the OpenShift GitOps (ArgoCD) instance
 ┃ ┗ 📂02_config-openshift-gitops - Configures the OpenShift GitOps (ArgoCD) instance, deploys an ArgoCD Application that points to the public upstream repo on GitHub and the `hub-of-hubs/gitops-apps/` directory
 ┣ 📂gitops-apps - A collection of ArgoCD Applications that load the individual manifest groups from the `hub-of-hubs/composition/` directory, could easily point to separate repos
 ┃ ┣ 📂external-secrets
 ┃ ┣ 📂rhacm-config
 ┃ ┣ 📂rhacm-installed
 ┃ ┗ 📂rhacm-observability
 ┗ 📂manifests - A collection of grouped manifests that will be synced to the Hub of Hubs to configure it, the geo-local clusters, as well as their spoke clusters.
 ┃ ┣ 📂external-secrets - Managed Secrets pulled in via External Secrets Operator
 ┃ ┣ 📂rhacm-installed - Installs the RHACM Operator
 ┃ ┣ 📂rhacm-installed - Installs the RHACM Operator
 ┃ ┗ 📂rhacm-observability - Installs the RHACM Observability components
 ┗ 📂manifests -
 ┗ 📂manifests -
```


- `hub-of-hubs/manifests/`
  - rhacm-install/ (installs RHACM with OLM CRs on HoH)
  - rhacm-config/ (sets policies for HoH, those forced on Geos, and forced on their Spokes)
    - policies/
      - rhacm-installed/ (vendor=OpenShift)
      - rhacs-installed/ (vendor=OpenShift)
      - cluster-alerting/ (vendor=OpenShift) sets up email alerts for cluster health
      - hoh-rhacs-central-config/ (local-cluster=true) installs RHACS Central on HoH
      - hoh-rhacm-hub-config/ configures OAS/Observability/etc
      - hoh-rhacm-managedclusters/ points to a repo of managedclusters to import into RHACM (core-ocp)
      - geo-rhacm-config/ (cluster-role=geo-cluster) configures OAS/Observability/etc
      - geo-rhacs-securedcluster/ template to connect Geo to HoH RHACS
      - geo-idp/ template to set OAuth for Geo IdPs
      - geo-ztp-config/ (aap2/cert-manager/gitea/gitops/lso/odf/reflector/Job w+ ansible CLI[maybe quay])
      - spoke-rhacm-config/ (cluster-role=spoke-cluster)
      - spoke-rhacs-securedcluster/
      - spoke-idp/
      - root-ca/ (vendor=OpenShift) sets up a root CA for all clusters
      - global-rbac/ (allows our users to get access to what they need)
      - hoh-secret-courier/ (copies secrets from NS on HoH to other OCP clusters' NS')

---

## What's the workflow?

### Hub of Hubs Bootstrapping

Most mutli-cluster architectures operate in a "Hub and Spoke" model, and can be extended even further by operating a "Hub of Hubs" pattern.  In an OpenShift context, there are many ways to bootstrap a Hub or Hub of Hub cluster, and arguably the method with the lowest level of effort and maintenance would be to use the OpenShift GitOps Operator (ArgoCD) to bootstrap things.  So then all you have to do is:

1. Start with a fresh OpenShift cluster
2. `oc apply -f hub-of-hubs/bootstrap/`
3. *Do some Secrets seeding stuff...*
4. `oc apply -f hub-of-hubs/gitops-config/`
5. ??????
6. PROFIT!!!!!1

From there, ArgoCD will sync things to that repo in order to install Red Hat Advanced Cluster Management and a Basic MultiClusterHub.  You could add additional things to the `hub-of-hubs/bootstrap/` directory to install other things, however from here forward this repository will leverage RHACM to manage the clusters via Policies.

### Secret Seeding

The tragic truth is that there is likely never going to be a true one-command line standing up of a full stand multi-cluster environment, probably even single clusters really when you think about it - all because of Secrets Management.

At many points you'll need to use some secrets for things such as authenticating to a Git repo or connecting to infrastructure providers.  Some common secrets you may need to seed are:

- Git repo credentials
- Infrastructure credentials (AWS, Azure, GCP, vSphere, IPAM, etc)
- Container Pull Secrets
- SSH Keys
- Vault Token for External Secrets Operator to authenticate to Hashicorp Vault

You can read more about a quick start in how to seed these secrets in the [Secret Seeding](examples/cheat-sheets/secret-seeding.md) cheat sheet.

---

## Prerequisites

### Cluster Resources

#### Hub of Hubs

- Please just use a managed service for this, like Red Hat OpenShift Service on AWS (ROSA) or Azure Red Hat OpenShift (ARO).
- XX GB of RAM
- YY GB of Disk
- ZZ vCPUs