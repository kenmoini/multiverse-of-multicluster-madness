# Dr. Shadowman and the Multiverse of Multi-cluster Madness

**Doing a Docker?**  *That's cool*

**Kicking off some Kubernetes?**  *Yeah, cool too*

**Operating an OpenShift?** *Now we're cooking with grease*

**Messing with multiple clusters?** *This is where the madness begins*

## What is this?

This repository is a collection of GitOps-centric resources to manage Kubernetes/OpenShift - and even more so, multiple clusters in a hub-of-hubs architecture, without descending into psychosis.  This isn't an end-all-be-all or gospel for how to do everything, but rather a collection of blueprints and patterns that can be used to build out a GitOps-centric multi-cluster management strategy of your own.

## What's in the box?

### Example Workflows

Any combination of Kustomize, Helm, ArgoCD, RHACM Apps, RHACM Policies, and RHACM PolicyGenerators that you can think of is probably available in this repository in a place or few.  There are different instances where one or a combination of sorts is the best option compared to others, which one you'll leverage will depend on what you're trying to state.

#### Hub-of-Hubs Cluster Bootstrapping

- [Example](hub-of-hubs/01_bootstrap/install-hashicorp-vault-chart/kustomization.yml) Kustomization pointing to another folder that installs a Helm Chart that was templated to a file

#### GitOps Bootstrapping

- [Example](hub-of-hubs/02_gitops-config/02_config-openshift-gitops/10_hoh-cluster-apps.yml) Bootstrapping a cluster by just installing ArgoCD, and configuring it with a Kustomized App-of-Apps to continue the subsequently sync-wave'd Apps
- ArgoCD Application that installs a Helm Chart from a Helm Repository

---

### Directory Structure

```
📦 multiverse-of-multicluster-madness
 ┣ 📂 applications - A set of sample applications
 ┃ ┣ 📂 infinite-mario - A randomly generating Mario-like web browser game
 ┃ ┃ ┣ 📂 manifests - The traditional YAML manifest files to deploy the application
 ┃ ┃ ┗ 📜 kustomization.yml - A Kustomize file that targets the files in the manifests folder
 ┃ ┣ 📂 omg-shoes - A simple HTML application with a display of cool shoes
 ┃ ┃ ┣ 📂 argocd-application - The ArgoCD Application that will sync down the files in the manifests folder
 ┃ ┃ ┣ 📂 manifests - The traditional YAML manifest files to deploy the application
 ┃ ┃ ┣ 📂 rhacm-application - A set of RHACM resources for the AppSub deployment mechanism
 ┃ ┃ ┣ 📂 site - The site source code
 ┃ ┃ ┗ 📜 Dockerfile - The Dockerfile to build this simple HTML site
 ┃ ┗ 📜 README.md - Extra information about the applications
 ┣ 📂 argocd
 ┃ ┣ 📂 appProjects - ArgoCD Projects to group Applications, synced via the initial Hub-of-Hubs bootstrap and then also referenced in `rhacm/policy-generators/install-openshift-gitops-operator`
 ┃ ┗ 📂 gitops-apps - A bunch of ArgoCD Applications that are synced by the App-of-Apps in `hub-of-hubs/02_gitops-config/02_config-openshift-gitops/10_hoh-cluster-apps.yml`
 ┃ ┃ ┣ 📂 deploy-rhacm-hub - Deploys a standard RHACM Hub
 ┃ ┃ ┣ 📂 external-secrets - A set of managed External Secrets
 ┃ ┃ ┣ 📂 hoh-rhacm-base-config - Hub of Hubs RHACM Base Configuration
 ┃ ┃ ┣ 📂 install-rhacm-operator - Installs the RHACM Operator
 ┃ ┃ ┣ 📂 reflector-chart-installed - Installs the Reflector Helm Chart
 ┃ ┃ ┣ 📂 rhacm-applications - Syncs down all the RHACM Applications, some of which are entry points to the RHACM PolicyGenerators
 ┃ ┃ ┣ 📂 rhacm-channels - Syncs down all the RHACM Channels
 ┃ ┃ ┣ 📂 rhacm-observability - Syncs down RHACM Observability add-on
 ┃ ┃ ┣ 📂 rhacm-policies - Syncs down all the RHACM Policies
 ┃ ┃ ┣ 📂 sealedsecrets-chart-installed - Installs the Sealed Secret Helm Chart
 ┃ ┃ ┗ 📜 kustomization.yml - Just toggles the individual Applications in this folder
 ┣ 📂 depreciated - plz ignore
 ┣ 📂 docs - Helpful words!  Available on https://kenmoini.github.io/multiverse-of-multicluster-madness/
 ┃ ┣ 📂 chart-repo - A Helm Chart Repository for the Helm Charts, hosted on GitHub Pages
 ┃ ┣ 📂 cheat-sheets - Some guidance and commands to spray/pray.
 ┃ ┣ 📂 examples - Useful examples for the comprehension or maintenance of this repo
 ┃ ┃ ┣ 📂 managedclusters - Example directory structure for imported ManagedClusters
 ┃ ┃ ┃ ┣ 📂 geo-hubs - Geo Hub level segmentation
 ┃ ┃ ┃ ┃ ┗ 📂 core-ocp - Geo Hub Cluster
 ┃ ┃ ┃ ┃ ┃ ┗ 📜 api-and-token.yml - Example of importing a cluster with an API endpoint and Token that was stored in a Vault referenced with an ExternalSecret.
 ┃ ┃ ┃ ┣ 📂 mcm-cluster-import - A set of YAML Manifests to apply to a cluster that needs to be imported into RHACM/RHACS
 ┃ ┃ ┃ ┗ 📜 README.md - Information around handling ManagedCluster workflows
 ┃ ┃ ┗ 📜 vault-helm-values.yml - Example values for the generated Hashicorp Vault Helm Chart, useful for updates
 ┃ ┣ 📜 generating-vault-yaml.md - How to update the generated Hashicorp Vault Helm Chart
 ┃ ┣ 📜 how-to-run-this-as-a-demo.md - How to run this as a demo
 ┃ ┣ 📜 metadata-guide.md - The 411 on Annotations, Labels and Tags
 ┃ ┗ 📜 policygenerators.md - What even are PolicyGenerators, man?
 ┣ 📂 hack - Some helpful scripts
 ┃ ┗ 📜 build-helm-chart-repo.sh - A script that will bundle/package the Helm Charts and process them for hosting.
 ┣ 📂 helm-charts - A collection of Helm Charts
 ┃ ┣ 📂 omg-shoes - A templated Helm Chart to deploy the 'OMG Shoes' application
 ┃ ┃ ┣ 📂 templates - The templated YAML manifests
 ┃ ┃ ┣ 📜 .helmignore - Things to not template
 ┃ ┃ ┣ 📜 Chart.yaml - Metadata regarding the Chart
 ┃ ┃ ┗ 📜 values.yaml - The default values passed when templating the Chart
 ┃ ┣ 📂 ztp-as-a-service - Helm Chart to deploy ZTP as a Service to clusters
 ┃ ┃ ┣ 📂 default-templates - The default YAML manifest templates that are provided as a result of running `helm create ztp-as-a-service`
 ┃ ┃ ┣ 📂 files - Files used as a referenced set of objects in the generation of a ConfigMap
 ┃ ┃ ┣ 📂 templates - The templated YAML manifests used
 ┃ ┗ 📜 README.md - More information about the collection of Helm Charts
 ┣ 📂hub-of-hubs
 ┃ ┣ 📂01_bootstrap
 ┃ ┃ ┣ 📂install-external-secrets-operator
 ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┣ 📂install-hashicorp-vault-chart
 ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┣ 📂install-openshift-gitops-operator
 ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┗ 📜kustomization.yaml
 ┃ ┣ 📂02_gitops-config
 ┃ ┃ ┣ 📂00_eso-config
 ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┣ 📜05_operatorconfig.yml
 ┃ ┃ ┃ ┣ 📜10_vault-clustersecretstore.yml
 ┃ ┃ ┃ ┗ 📜kustomization.yaml
 ┃ ┃ ┣ 📂01_deploy-openshift-gitops
 ┃ ┃ ┃ ┣ 📜00_instance.yml
 ┃ ┃ ┃ ┣ 📜01_configmap.yml
 ┃ ┃ ┃ ┣ 📜05_rbac.yml
 ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┣ 📂02_config-openshift-gitops
 ┃ ┃ ┃ ┣ 📜05_repo-externalsecret.yml
 ┃ ┃ ┃ ┣ 📜05_reposecret.yml
 ┃ ┃ ┃ ┣ 📜10_hoh-cluster-apps.yml
 ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┗ 📜kustomization.yaml
 ┃ ┗ 📂99_vault_init
 ┃ ┃ ┣ 📜00_service_account.yml
 ┃ ┃ ┣ 📜05_rbac.yml
 ┃ ┃ ┣ 📜10_job.yml
 ┃ ┃ ┗ 📜kustomization.yaml
 ┣ 📂manifests
 ┃ ┣ 📂additional-trust-bundle
 ┃ ┃ ┣ 📂policygenerator
 ┃ ┃ ┃ ┣ 📂base
 ┃ ┃ ┃ ┃ ┣ 📜base-proxy-config.yml
 ┃ ┃ ┃ ┃ ┗ 📜root-ca-configmap.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┣ 📂external-secrets
 ┃ ┃ ┣ 📜rhacm-observability-pull-secret.yml
 ┃ ┃ ┗ 📜rhacm-pull-secrets.yml
 ┃ ┣ 📂global-rbac
 ┃ ┃ ┗ 📂manifests
 ┃ ┃ ┃ ┣ 📂rolebindings
 ┃ ┃ ┃ ┃ ┣ 📜cluster-admin-users.yml
 ┃ ┃ ┃ ┃ ┣ 📜cluster-reader-users.yml
 ┃ ┃ ┃ ┃ ┗ 📜redis-administrator-users.yml
 ┃ ┃ ┃ ┗ 📂roles
 ┃ ┃ ┃ ┃ ┗ 📜redis-administrator.yml
 ┃ ┣ 📂hoh-rhacm-base-config
 ┃ ┃ ┣ 📜00_namespaces.yml
 ┃ ┃ ┣ 📜geo-hub-clusterset.yml
 ┃ ┃ ┣ 📜hoh-clusterset.yml
 ┃ ┃ ┗ 📜hoh-local-cluster-annocation.yml
 ┃ ┣ 📂install-external-secrets-operator
 ┃ ┃ ┣ 📜10_subscription.yml
 ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┣ 📂install-hashicorp-vault-chart
 ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┣ 📜10_mappedChart.yml
 ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┣ 📂install-ocp-virt-operator
 ┃ ┃ ┗ 📂manifests
 ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┣ 📜05_operatorgroup.yml
 ┃ ┃ ┃ ┗ 📜10_subscription.yml
 ┃ ┣ 📂install-pipelines-operator
 ┃ ┃ ┗ 📂manifests
 ┃ ┃ ┃ ┗ 📜10_subscription.yml
 ┃ ┣ 📂install-redis-ee-operator
 ┃ ┃ ┗ 📂manifests
 ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┣ 📜03_scc.yml
 ┃ ┃ ┃ ┣ 📜05_operatorgroup.yml
 ┃ ┃ ┃ ┣ 📜07_rbac.yml
 ┃ ┃ ┃ ┗ 📜10_subscription.yml
 ┃ ┣ 📂install-reloader-remote-kustomize
 ┃ ┃ ┗ 📜kustomization.yaml
 ┃ ┣ 📂install-serverless-operator
 ┃ ┃ ┗ 📂manifests
 ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┣ 📜05_operatorgroup.yml
 ┃ ┃ ┃ ┗ 📜10_subscription.yml
 ┃ ┣ 📂install-servicemesh-operator
 ┃ ┃ ┗ 📂manifests
 ┃ ┃ ┃ ┗ 📜10_subscription.yml
 ┃ ┗ 📂rhacm-observability
 ┃ ┃ ┣ 📜00_namespaces.yml
 ┃ ┃ ┗ 📜05_objectbucketclaim.yml
 ┣ 📂rhacm
 ┃ ┣ 📂applications
 ┃ ┃ ┣ 📂app-infinite-mario
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂app-omg-shoes
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂deploy-aap2-controller
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂deploy-lso-odf
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂deploy-rhacm-hub
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂distribute-root-certs
 ┃ ┃ ┃ ┗ 📜rhacm-app.yml
 ┃ ┃ ┣ 📂global-config
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂idp-config
 ┃ ┃ ┃ ┗ 📜rhacm-app.yml
 ┃ ┃ ┣ 📂install-aap2-operator
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂install-gitea-operator
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂install-lso-operator
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂install-odf-operator
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂install-openshift-gitops-operator
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂install-reflector-chart
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂install-rhacm-operator
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┣ 📂placement-inheritance
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┃ ┗ 📂ztp-as-a-service
 ┃ ┃ ┃ ┗ 📜rhacm-app.yaml
 ┃ ┣ 📂channels
 ┃ ┃ ┗ 📜github-kenmoini-upstream.yml
 ┃ ┣ 📂policies
 ┃ ┃ ┣ 📂aws-infra-nodes
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂geo-hub-grafana
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂geo-hub-lokistack
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂geo-hub-multiclusterobservability
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂hoh-grafana
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂placementbindings
 ┃ ┃ ┃ ┣ 📜aws-infra-nodes.yml
 ┃ ┃ ┃ ┣ 📜geo-hub-grafana.yml
 ┃ ┃ ┃ ┣ 📜geo-hub-lokistack.yml
 ┃ ┃ ┃ ┣ 📜geo-hub-multiclusterobservability.yml
 ┃ ┃ ┃ ┣ 📜hoh-grafana.yml
 ┃ ┃ ┃ ┣ 📜rhacs-central-deployed.yml
 ┃ ┃ ┃ ┣ 📜rhacs-init-bundle-generator-job.yml
 ┃ ┃ ┃ ┣ 📜rhacs-operator-installed.yml
 ┃ ┃ ┃ ┗ 📜ztp-as-a-service-consolelinks.yml
 ┃ ┃ ┣ 📂rhacs-central-deployed
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂rhacs-init-bundle-generator-job
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂rhacs-operator-installed
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┣ 📂rhacs-securedcluster-deployed
 ┃ ┃ ┃ ┗ 📜policy.yml
 ┃ ┃ ┗ 📂ztp-as-a-service-consolelinks
 ┃ ┃ ┃ ┣ 📜aap2-controller-policy.yml
 ┃ ┃ ┃ ┣ 📜gitea-policy.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜rh-sso-policy.yml
 ┃ ┗ 📂policy-generators
 ┃ ┃ ┣ 📂app-omg-shoes
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┗ 📜argocd-application.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂deploy-aap2-controller
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜10_controller_instance.yml
 ┃ ┃ ┃ ┃ ┣ 📜5_pgsql-unmanaged-deployment.yml
 ┃ ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂deploy-lso-odf
 ┃ ┃ ┃ ┣ 📂aws
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┃ ┗ 📜lso-odf-storagecluster.yml
 ┃ ┃ ┃ ┣ 📂base
 ┃ ┃ ┃ ┃ ┣ 📜default-sc.yml
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┃ ┣ 📜lso-odf-storagecluster.yml
 ┃ ┃ ┃ ┃ ┗ 📜lso-odf-storagesystem.yml
 ┃ ┃ ┃ ┣ 📂local-infra-nodes
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┃ ┣ 📜lso-localvolumediscovery.yml
 ┃ ┃ ┃ ┃ ┣ 📜lso-localvolumeset.yml
 ┃ ┃ ┃ ┃ ┣ 📜lso-odf-storagecluster.yml
 ┃ ┃ ┃ ┃ ┗ 📜lso-rook-ceph-operator-config-cm.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┣ 📜policygenerator-aws.yml
 ┃ ┃ ┃ ┗ 📜policygenerator-local-infra-nodes.yml
 ┃ ┃ ┣ 📂deploy-rhacm-hub
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜20_multiclusterhub.yml
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yaml
 ┃ ┃ ┃ ┃ ┣ 📜mch-disable-self-managed.yml
 ┃ ┃ ┃ ┃ ┗ 📜mch-nodeselectors.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂distribute-root-certs
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜configmap-policy.yml
 ┃ ┃ ┃ ┃ ┣ 📜placementbinding.yml
 ┃ ┃ ┃ ┃ ┗ 📜secret-policy.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂global-config
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┗ 📜argocd-application-rbac.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂idp-config
 ┃ ┃ ┃ ┣ 📂base
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┃ ┣ 📜matrix-login-template.yml
 ┃ ┃ ┃ ┃ ┗ 📜oauth-base-config.yml
 ┃ ┃ ┃ ┣ 📂google
 ┃ ┃ ┃ ┃ ┣ 📜external-secret.yml
 ┃ ┃ ┃ ┃ ┣ 📜google-rh-sso.yml
 ┃ ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┃ ┣ 📂ldap
 ┃ ┃ ┃ ┃ ┣ 📜external-secret.yml
 ┃ ┃ ┃ ┃ ┣ 📜freeipa-ldap.yml
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┃ ┗ 📜ldap-ca-configmap.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┣ 📜policygenerator-google.yml
 ┃ ┃ ┃ ┣ 📜policygenerator-htpasswd.yml
 ┃ ┃ ┃ ┗ 📜policygenerator-ldap.yml
 ┃ ┃ ┣ 📂install-aap2-operator
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┃ ┣ 📜05_operatorgroup.yml
 ┃ ┃ ┃ ┃ ┣ 📜07_rbac.yml
 ┃ ┃ ┃ ┃ ┣ 📜10_subscription.yml
 ┃ ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂install-gitea-operator
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜00_catalogsource.yml
 ┃ ┃ ┃ ┃ ┣ 📜05_rbac.yml
 ┃ ┃ ┃ ┃ ┗ 📜10_subscription.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂install-lso-operator
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┃ ┣ 📜07_operatorgroup.yml
 ┃ ┃ ┃ ┃ ┣ 📜10_subscription.yml
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yaml
 ┃ ┃ ┃ ┃ ┗ 📜operator-nodeselectors.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂install-odf-operator
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┃ ┣ 📜07_operatorgroup.yml
 ┃ ┃ ┃ ┃ ┣ 📜10_subscription.yml
 ┃ ┃ ┃ ┃ ┣ 📜cluster_console_config.yml
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yaml
 ┃ ┃ ┃ ┃ ┗ 📜operator-nodeselectors.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂install-openshift-gitops-operator
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜00_namespace.yml
 ┃ ┃ ┃ ┃ ┣ 📜05_operatorgroup.yml
 ┃ ┃ ┃ ┃ ┣ 📜10_subscription.yml
 ┃ ┃ ┃ ┃ ┗ 📜kustomization.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂install-reflector-chart
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┃ ┗ 📜project.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂install-rhacm-operator
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┣ 📜00_namespaces.yml
 ┃ ┃ ┃ ┃ ┣ 📜05_rbac-hive.yml
 ┃ ┃ ┃ ┃ ┣ 📜05_rbac_subscriptionadmin.yml
 ┃ ┃ ┃ ┃ ┣ 📜07_operatorgroup.yml
 ┃ ┃ ┃ ┃ ┣ 📜10_subscription.yml
 ┃ ┃ ┃ ┃ ┣ 📜kustomization.yaml
 ┃ ┃ ┃ ┃ ┗ 📜operator-nodeselectors.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┣ 📂placement-inheritance
 ┃ ┃ ┃ ┣ 📂placementrules
 ┃ ┃ ┃ ┃ ┣ 📜all-openshift-clusters.yml
 ┃ ┃ ┃ ┃ ┣ 📜aws-openshift-clusters.yml
 ┃ ┃ ┃ ┃ ┣ 📜geo-hub-clusters.yml
 ┃ ┃ ┃ ┃ ┣ 📜hub-of-hubs-clusters.yml
 ┃ ┃ ┃ ┃ ┣ 📜idp.yml
 ┃ ┃ ┃ ┃ ┣ 📜local-cluster.yml
 ┃ ┃ ┃ ┃ ┣ 📜lso-odf-clusters.yml
 ┃ ┃ ┃ ┃ ┣ 📜management-clusters.yml
 ┃ ┃ ┃ ┃ ┣ 📜o11y-geo-hub-clusters.yml
 ┃ ┃ ┃ ┃ ┣ 📜spoke-cluster.yml
 ┃ ┃ ┃ ┃ ┗ 📜ztp-as-a-service-clusters.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┃ ┃ ┗ 📂ztp-as-a-service
 ┃ ┃ ┃ ┣ 📂manifests
 ┃ ┃ ┃ ┃ ┗ 📜argocd-application.yml
 ┃ ┃ ┃ ┣ 📜kustomization.yml
 ┃ ┃ ┃ ┗ 📜policygenerator.yml
 ┗ 📜README.md - This README file!
```

---

## What's the workflow?

### Hub of Hubs Bootstrapping

Most mutli-cluster architectures operate in a "Hub and Spoke" model, and can be extended even further by operating a "Hub of Hubs" pattern.  In an OpenShift context, there are many ways to bootstrap a Hub or Hub of Hub cluster, and arguably the method with the lowest level of effort and maintenance would be to use the OpenShift GitOps Operator (ArgoCD) to bootstrap things.  So then all you have to do is:

1. Start with a fresh OpenShift cluster
2. Install the needed Operators: `oc apply -k hub-of-hubs/01_bootstrap/`
3. [Optional] Initialize the Vault `oc apply -k hub-of-hubs/99_vault_init`
4. *[Do some Secrets seeding stuff...](docs/cheat-sheets/secret-seeding.md)*
5. Configure the OpenShift GitOps instance to bootstrap the rest of the process: `oc apply -k hub-of-hubs/02_gitops-config/`
6. Wait for RHACM to deploy and the Hub-of-Hub's local-cluster to come online
7. Label Hub-of-Hub's local-cluster ManagedCluster CR:

```bash
oc label managedcluster local-cluster cluster-role=hub-of-hubs
oc label managedcluster local-cluster cluster.open-cluster-management.io/clusterset=hub-of-hubs --overwrite
```

8. ??????
9.  PROFIT!!!!!1

From there, ArgoCD will sync things to that repo in order to install Red Hat Advanced Cluster Management and a Basic MultiClusterHub.  You could add additional things to the `hub-of-hubs/01_bootstrap/` directory to install other things, however from here forward this repository will leverage RHACM to manage the clusters via Policies.

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