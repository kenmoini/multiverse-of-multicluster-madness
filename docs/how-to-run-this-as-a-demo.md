# How to run this as a demo

## Prerequisites

- Hub of Hubs cluster, suggested it be ARO/ROSA
- At least one geo-local cluster, can be running on anything really ([ocp4-ai-svc-universal](https://github.com/kenmoini/ocp4-ai-svc-universal))
- The ability to ZTP from the geo-local cluster ((openshift-ztp)[https://github.com/Red-Hat-SE-RTO/openshift-ztp])

## Overview

1. Clone down this repo
2. Log into the HoH cluster
3. Run `oc apply -f hub-of-hubs/bootstrap/` to install ArgoCD/Vault/External Secrets operator.
4. Initialize Vault with some secrets - it's running in Dev mode so everything is in-memory and will be lost on restart.  The root token is just set to 'root' in this mode too.

```bash
# Get the Unseal key and Token
export UNSEAL_KEY=$(oc logs vault-0 -n vault | grep 'Unseal Key' | cut -d ' ' -f 3)
export DEV_ROOT_TOKEN="root" # lol

# Create some secrets

## Git repo example
oc rsh -n vault vault-0 vault kv put -mount=secret hoh-repo-reader username=gitlab-ci-token password=REDACTED

## Pull Secret example
oc rsh -n vault vault-0 vault kv put -mount=secret hoh-pull-secret dockerconfigjson=$(cat ~/.docker/config.json | jq -rMc)

## SSH Key example
oc rsh -n vault vault-0 vault kv put -mount=secret hoh-ssh-key private_key="$(cat ~/.ssh/id_rsa)" public_key="$(cat ~/.ssh/id_rsa.pub)"

## AWS creds example
oc rsh -n vault vault-0 vault kv put -mount=secret hoh-aws-creds aws_access_key_id=REDACTED aws_secret_access_key=REDACTED

## GCP creds example
oc rsh -n vault vault-0 vault kv put -mount=secret hoh-gcp-creds gcp_service_account="$(cat ~/.gcp/creds.json)"

## vCenter creds example
oc rsh -n vault vault-0 vault kv put -mount=secret mco-vcenter-creds vcenter_username=REDACTED vcenter_password=REDACTED vcenter_fqdn=vcenter.example.com vcenter_validate_ssl=false

# Gitea credentials (for inside the Geos)
oc rsh -n vault vault-0 vault kv put -mount=secret gitea-creds git_username=user-1 git_password=openshift \
  git_url=https://gitea.apps.hoh.example.com/user-1/openshift-ztp \
  git_branch=main \
  git_auth_method=basic \
  git_user_name="Weebo" \
  git_user_email="prof.brainard@medfield.edu" \
  git_ssh_key=""
  #git_url=git@gitea.apps.hoh.example.com:user-1/openshift-ztp.git \
  #git_ssh_key="$(cat ~/.ssh/id_rsa)"

# Custom Root CA PEM Bundle
oc rsh -n vault vault-0 vault kv put -mount=secret custom-ca-bundle ca_bundle="$(cat /etc/pki/ca-trust/source/anchors/*.pem)"

# Full baked Trusted Root CA Bundle - file is loaded from inside the vault-0 pod
oc rsh -n vault vault-0 vault kv put -mount=secret full-root-trusted-ca-bundle ca_bundle=@/etc/ssl/certs/ca-certificates.crt

# Full baked Trusted Root CA Bundle - copy the local bundle to the pod then use it instead
## Copy
oc cp -n vault /etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem vault-0:/tmp/ca-bundle.pem
## Load
oc rsh -n vault vault-0 vault kv put -mount=secret full-root-trusted-ca-bundle ca_bundle=@/tmp/ca-bundle.pem
```

5. Seed the Secrets in Vault for
   1. Auth to read this Git repo
   2. Auth to read/write to the Gitea/other Git repos for Geo ZTP
   3. Auth for geo vCenter
   4. Container Registry Pull Secret(s)
   5. SSH Key for RHCOS nodes
   6. [Optional] Custom Root CA Bundle(s)
   7. [Optional] Auth for AWS
   8. [Optional] Auth for GCP
6. Configure ESO to connect to Vault
7. Run `oc apply -f hub-of-hubs/gitops-config/` to deploy ArgoCD and the apps that use the ExternalSecrets pointing where it needs to be.