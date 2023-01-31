# Cheat Sheet - Secret Seeding

Out of the box the clusters leverage a quickly bootstrapped Vault instance with the External Secrets Operator to pull them into the clusters.  The ExternalSecrets expect a few secrets to already be in place in Vault, and this cheat sheet will help you get those secrets seeded.

Assuming you'll be delivering this demonstration on a ROSA Hub-of-Hub cluster, and at least one Geo-Hub cluster that is deliver ZTP-as-a-Service with Spoke clusters, then you should need the following Secrets made:

- An k8s/OCP Secret for the token used by the External Secrets Operator to authenticate to Vault
- Credentials to read this Git repo, called `hoh-git-reader`
- Credentials to push to another (or this) Git repo for the Geo-Hub ZTP clusters called `geo-ztp-git-credentials`
- Credentials to ZTP to vSphere clusters called `geo-ztp-vsphere-credentials`
- Container Image Pull Credentials for the Geo-Hub ZTP clusters called `geo-ztp-image-pull-credentials`
- SSH Key Pair for the Geo-Hub ZTP clusters called `geo-ztp-ssh-keypair`

## `vault-token-auth`

Create the k8s/OCP Secret for the token used by the External Secrets Operator to authenticate to Vault.  If you're using the Vault deployment in dev mode then the token is `root`.

```bash
oc create secret generic vault-token-auth --from-literal=token=root -n vault
```

If you are not using Vault in dev mode as is default, then you need to initialize the Vault first.

```bash
## The hub-of-hubs/99_vault_init manifests take care of all the following steps
oc apply -k hub-of-hubs/99_vault_init/

## ORRRRR...

## Switch to the Project
oc project vault

## Initialize the Vault
VAULT_INIT=$(oc exec -it vault-0 -- vault operator init -key-shares=1 -key-threshold=1)

## Take note of the Vault Key and Root Token!
echo "$VAULT_INIT" > .vault-init

UNSEAL_KEY=$(cat .vault-init | grep "Unseal Key" | sed 's/Unseal Key 1: //')
ROOT_TOKEN=$(cat .vault-init | grep "Initial Root Token" | sed 's/Initial Root Token: //')

## Create the OpenShift Secret for the Vault Root Token
oc create secret generic vault-token-auth --from-literal=token="${ROOT_TOKEN}" -n vault

## Unseal with the Vault Key
oc exec -it vault-0 -- vault operator unseal "$UNSEAL_KEY"

## Log in to Vault with the Root Token
oc exec -it vault-0 -- vault login "${ROOT_TOKEN}"

## Enable key/value Secrets Engine
oc exec -it vault-0 -- vault secrets enable -version=2 kv

### [Optional] Enable Vault PKI Secrets Engine
oc exec -it vault-0 -- vault secrets enable pki

### [Optional] Configure a 10yr max lease time for PKI
oc exec -it vault-0 -- vault secrets tune -max-lease-ttl=87600h pki

## [Optional] Enable K8s authentication provider in Vault
oc exec -it vault-0 -- vault auth enable kubernetes

### [Optional] Configure the K8s Auth Integration
oc exec -it vault-0 -- vault write auth/kubernetes/config kubernetes_host="https://\$KUBERNETES_PORT_443_TCP_ADDR:443" token_reviewer_jwt=@/var/run/secrets/kubernetes.io/serviceaccount/token kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt issuer="https://kubernetes.default.svc.cluster.local"

oc exec -it vault-0 -- vault write auth/kubernetes/role/issuer bound_service_account_names="*" bound_service_account_namespaces="*" ttl=20m
```

## `hoh-git-reader`

1. Generate a Personal Access Token (PAT) for GitHub: https://github.com/settings/personal-access-tokens/new
2. Create the Secret in Vault

```bash
GIT_USERNAME="kenmoini" # Your GitHub username
GIT_PASSWORD="$(cat ~/.mvomcm-gh-pat)" # Your GitHub PAT, store in a local file for easy reuse
GIT_REPO="https://github.com/kenmoini/multiverse-of-multicluster-madness.git" # The URL to this repo/your fork

oc rsh -n vault vault-0 vault kv put -mount=kv hoh-git-reader git_username="${GIT_USERNAME}" git_password="$GIT_PASSWORD" \
  git_url="$GIT_REPO" \
  git_branch=main \
  git_auth_method=basic \
  git_user_name="Weebo" \
  git_user_email="prof.brainard@medfield.edu" \
  git_ssh_key=""
```

## `geo-ztp-git-credentials`

Create the Geo-local Git repo credentials to push to the Geo-Hub ZTP repos.
Unless using a non-standard Git source for the Geo-Hub ZTP clusters, you can use the following Gitea default to match the one used in its deployment, just change the GIT_REPO URL parts

```bash
GIT_USERNAME="user-1"
GIT_PASSWORD="openshift"
GIT_REPO="https://gitea-gitea.apps.MCM-GEO-HUB.EXAMPLE.COM/user-1/openshift-ztp.git"

oc rsh -n vault vault-0 vault kv put -mount=kv geo-ztp-git-credentials git_username="${GIT_USERNAME}" git_password="$GIT_PASSWORD" \
  git_url="$GIT_REPO" \
  git_branch=main \
  git_auth_method=basic \
  git_user_name="Weebo" \
  git_user_email="prof.brainard@medfield.edu" \
  git_ssh_key=""
```

## `geo-ztp-vsphere-credentials`

Create the Geo-local vSphere credentials to ZTP to vSphere clusters.

```bash
VCENTER_USERNAME="administrator@vsphere.lab.kemo.network"
VCENTER_PASSWORD="$(cat ~/.vcenter-admin-pwd)"
VCENTER_FQDN="vcenter.lab.kemo.network"

oc rsh -n vault vault-0 vault kv put -mount=kv geo-ztp-vsphere-credentials \
 vcenter_username="${VCENTER_USERNAME}" \
 vcenter_password="$VCENTER_PASSWORD" \
 vcenter_fqdn="$VCENTER_FQDN" \
 vcenter_validate_ssl=false
```

## `geo-ztp-image-pull-credentials`

Create the Geo-local Image Pull credentials to ZTP to vSphere clusters.  This could change depending on if you have a disconnected/private registry available for container images instead of pulling them from the internet.

```bash
oc rsh -n vault vault-0 vault kv put -mount=kv pull-secret dockerconfigjson=$(cat ~/.docker/config.json | jq -rMc)
oc rsh -n vault vault-0 vault kv put -mount=kv hoh-pull-secret dockerconfigjson=$(cat ~/.docker/config.json | jq -rMc)
oc rsh -n vault vault-0 vault kv put -mount=kv geo-ztp-image-pull-credentials dockerconfigjson=$(cat ~/.docker/config.json | jq -rMc)
```

## `geo-ztp-ssh-keypair`

Create the Geo-local SSH Key Pair to ZTP to vSphere clusters.

```bash
# Create a new SSH key pair
ssh-keygen -t rsa -b 4096 -f ~/.ssh/mvomcm-geo-hub-ztp -C "mvomcm-geo-hub-ztp" -N ""

oc rsh -n vault vault-0 vault kv put -mount=kv geo-ztp-ssh-keypair private_key="$(cat ~/.ssh/mvomcm-geo-hub-ztp)" public_key="$(cat ~/.ssh/mvomcm-geo-hub-ztp.pub)"
```

## Additional Credentials

Some times for different adaptations of this demo environment you may have things like credentials to the hyperscalar clouds that are leveraged for things such as IPI deployments of clusters, Hypershift, and ZTP.  You can create those secrets in Vault as well, and then use the External Secrets Operator to pull them into the clusters with the different example ExternalSecrets.

```bash
## AWS creds example
aws_access_key=$(awk -F "=" '/aws_access_key_id/ {print $2}' ~/.aws/credentials | tr -d " ")
aws_secret_key=$(awk -F "=" '/aws_secret_access_key/ {print $2}' ~/.aws/credentials | tr -d " ")

oc rsh -n vault vault-0 vault kv put -mount=kv hoh-aws-creds aws_access_key_id=$aws_access_key aws_secret_access_key=$aws_secret_key

## Azure creds example
oc rsh -n vault vault-0 vault kv put -mount=kv hoh-azure-creds azure_client_id=REDACTED azure_client_secret=REDACTED azure_tenant_id=REDACTED azure_subscription_id=REDACTED

## GCP creds example
oc rsh -n vault vault-0 vault kv put -mount=kv hoh-gcp-creds gcp_service_account="$(cat ~/.gcp/creds.json)"
```