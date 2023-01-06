# Generating/Updating the Vault YAML

A Vault would be something to be painstakingly curated and maintained by some poor sap - something perfect to version and maintain in a GitOps fashion!

In this example architecture the Hashicorp Vault instance is deployed to the Hub of Hubs cluster via templated Helm charts that are baked and made part of the HoH cluster bootstrap.

## Generating the Vault YAML

```bash
# Add the Hashicorp Helm repo
helm repo add hashicorp https://helm.releases.hashicorp.com

# Update the repos
helm repo update

# Create the Vault YAML
helm template vault hashicorp/vault --namespace vault --values examples/vault-helm-values.yml > hub-of-hubs-bootstrap/install-hashicorp-vault/10_mappedChart.yml
```