# RHACM Policies

Red Hat Advanced Cluster Management's Governance framework is extremely powerful - personally my favorite capability it carries.

These governance Policies are enforced on clusters or used as informational conduits into state of cluster systems.

## Templating

One of the more powerful capabilities within the Policies is the ability to use Golang Templates: https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.6/html-single/governance/index#support-templates-in-config-policies

Note that you can use most anything that the internal Golang templating package provides: https://pkg.go.dev/text/template

Then there are some additional functions that provide some ease in referencing ConfigMaps, Secrets, other objects, formatting data to other types, etc: https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.6/html-single/governance/index#template-functions