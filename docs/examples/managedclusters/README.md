# Managed Clusters

Another sad reality of GitOps is that ManagedClusters will likely be challenging to do in an automated fashion and it really all just comes down to Secrets really.

## Create Auth on the Managed Cluster

```bash
## [ManagedCluster] Create the Multicluster Management Import ServiceAccount & Secret
oc apply -f docs/examples/managedclusters/mcm-cluster-import

## [ManagedCluster] Get the Token
TOKEN=$(oc get secret/mcm-cluster-importer-sa-token -n kube-system -o jsonpath='{.data.token}' | base64 -d)
SERVER=$(oc get infrastructure cluster -o jsonpath='{.status.apiServerURL}')

## Create the Token in the Vault
### Make sure to log back into the Hub-of-Hubs or wherever your Vault is running

oc rsh -n vault vault-0 vault kv put -mount=kv geo-hubs/core-ocp token="$TOKEN" autoImportRetry="3" server="$SERVER"
```