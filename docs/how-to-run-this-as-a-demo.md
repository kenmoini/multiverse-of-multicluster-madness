# How to run this as a demo

## Prerequisites

- Hub of Hubs cluster, suggested it be ARO/ROSA
- At least one geo-local cluster, can be running on anything really ([ocp4-ai-svc-universal](https://github.com/kenmoini/ocp4-ai-svc-universal))
- The ability to ZTP from the geo-local cluster ((openshift-ztp)[https://github.com/Red-Hat-SE-RTO/openshift-ztp])

## Overview

1. Clone down this repo
2. Log into the HoH cluster
3. Run `oc apply -f hub-of-hubs-bootstrap/` to install ArgoCD/Vault/External Secrets operator.
4. Initialize Vault
5. Seed the Secrets in Vault
6. Configure ESO to connect to Vault
7. Run `oc apply -f hub-of-hubs-gitops-config/` to deploy ArgoCD and the apps that use the ExternalSecrets pointing where it needs to be.