# PolicyGenerators

PolicyGenerators are pretty cool - they generate RHACM Policies and can be more atomically, dynamically, and flexibly configured compared to normal defined Policies.

These PolicyGenerators are bound to RHACM clusters via a RHACM Application, though since the PolicyGenerator is just a Kustomize alpha plugin, there's technically nothing stopping you from using it with ArgoCD instead.

## Source of Truth & API Reference

There are a lot of different ways to use the PolicyGenerator that may make things your life easier - but which one do you use, and which ones are there anyway?

https://github.com/stolostron/policy-generator-plugin/blob/main/docs/policygenerator-reference.yaml

## Local Development

You can test your PolicyGenerators locally via `kustomize` which is really nice for a shorter feedback loop vs deploying to a server and waiting for it all to propagate.

1. Install Kustomize (if you don't already have it): https://kubectl.docs.kubernetes.io/installation/kustomize/
2. Install the Plugin: https://github.com/stolostron/policy-generator-plugin#as-a-kustomize-plugin
3. Run `kustomize build --enable-alpha-plugins` in a directory that has a `kustomization.yml` file pointing to a PolicyGenerator CR file or few.

## Directory Structure

Your PolicyGenerator directory structure can be different depending on your needs - check out `rhacm/policy-generators/idp-config/` for a way to do a mutable set of PolicyGenerators.

For the PolicyGenerator to work it needs to start as a function of Kustomize, which begets the `kustomization.yml` file:

```yaml
# Example from rhacm/policy-generators/idp-config/kustomization.yml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# This is where we leverage the PolicyGenerator CRs
generators:
  - policygenerator-google.yml
  - policygenerator-ldap.yml
  #- policygenerator-htpasswd.yml

commonLabels:
  manifest-group: idp-config

commonAnnotations:
  argocd.argoproj.io/compare-options: IgnoreExtraneous

# more kustomization.yml things...
```

Those `generators` listed are the important parts - let's look at one:

```yaml
# Example from rhacm/policy-generators/idp-config/policygenerator-ldap.yml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: idp-ldap-pg
  #annotations:
  #  argocd.argoproj.io/compare-options: IgnoreExtraneous
placementBindingDefaults:
  name: idp-ldap-pg-pb
policyDefaults:
  namespace: rhacm-policies
  # Use the name of an existing placement rule
  placement:
    placementRuleName: idp-ldap-auth-clusters
  remediationAction: enforce
  severity: high
  consolidateManifests: false # Put each object in its own ConfigurationPolicy in the Policy
  #policySets:
  #  - idp-ldap
policies:
  - name: idp-ldap
    manifests:
      - path: ldap
```

From there, we just have some files in the `ldap` folder as specified by `.policies[].manifests[].path` - another key file in this folder is another `kustomization.yml` file!  This allows us to extend a base, provide additional patches, and other mutable overriding functions:

```yaml
# Example from rhacm/policy-generators/idp-config/ldap/kustomization.yml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
- ../base

resources:
- external-secret.yml
- ldap-ca-configmap.yml

patchesStrategicMerge:
- freeipa-ldap.yml
```

## Bootstrapping to an OpenShift Cluster

As of this writing, the simplest method to apply the PolicyGenerator to a RHACM context would be with a RHACM Application:

```yaml
apiVersion: app.k8s.io/v1beta1
kind: Application
metadata:
  annotations:
    argocd.argoproj.io/compare-options: IgnoreExtraneous
  name: idp-config-policygenerator
  namespace: rhacm-applications
spec:
  componentKinds:
    - group: apps.open-cluster-management.io
      kind: Subscription
  selector:
    matchExpressions:
      - key: app
        operator: In
        values:
          - idp-config-policygenerator
---
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  annotations:
    argocd.argoproj.io/compare-options: IgnoreExtraneous
    apps.open-cluster-management.io/git-branch: main
    apps.open-cluster-management.io/git-path: rhacm/policy-generators/idp-config
    apps.open-cluster-management.io/reconcile-option: merge
  labels:
    app: idp-config-policygenerator
  name: idp-config-policygenerator
  namespace: rhacm-applications
spec:
  channel: rhacm-channels/ggithubcom-kenmoini-multiverse-of-multicluster-madness
  placement:
    placementRef:
      kind: PlacementRule
      name: management-clusters
# ---
# RHACM Channels are centrally managed in rhacm/channels/
# apiVersion: apps.open-cluster-management.io/v1
# kind: Channel
# metadata:
#   annotations:
#     argocd.argoproj.io/compare-options: IgnoreExtraneous
#     apps.open-cluster-management.io/reconcile-rate: medium
#   name: ggithubcom-kenmoini-multiverse-of-multicluster-madness
#   namespace: rhacm-channels
# spec:
#   type: Git
#   pathname: 'https://github.com/kenmoini/multiverse-of-multicluster-madness'
```

This is handy because keep in mind we're generating Policies that have their own PlacementRules and Bindings vs what this Application is bound by/to.

Then add to that, being able to take a Policy and wrap it in a PolicyGenerator like what is done in [rhacm/policy-generators/distribute-root-certs](rhacm/policy-generators/distribute-root-certs), allows for a nice way to enforce across architecture tiers of clusters.