# Metadata Guide

A crucial part of scale out architecture is labeling and tagging your assets - this gives you atomic and ensemble based matching of your resources.  There are going to be a lot of labels/tags that are going to be applied to your objects and every organization's set of labels looks different - but there are some common attributes or features that you'll find below that could jump start your own efforts.

## Common Labels

There are some common labels that are considered to be baseline best practices.

| Key                          | Description                                                                      | Example      | Type   |
|------------------------------|----------------------------------------------------------------------------------|--------------|--------|
| app.kubernetes.io/name       | The name of the application                                                      | mysql        | string |
| app.kubernetes.io/instance   | A unique name identifying the instance of an application                         | mysql-abcxzy | string |
| app.kubernetes.io/version    | The current version of the application (e.g., a SemVer 1.0, revision hash, etc.) | 5.7.21       | string |
| app.kubernetes.io/component  | The component within the architecture                                            | database     | string |
| app.kubernetes.io/part-of    | The name of a higher level application this one is part of                       | wordpress    | string |
| app.kubernetes.io/managed-by | The tool being used to manage the operation of an application                    | helm         | string |

You can find these listed here in great detail: https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/

## Namespacing Metadata

Metadata can be namespaced via domain pathing, eg there are some well-known labels, annotations, and taints that have keys prefixed by `kubernetes.io`, `app.kubernetes.io`, etc.  You can of course also namespace your own labels with a domain path prefix, eg `example.com/cost-center`.

## Well-known Labels, Annotations, and Taints

You can find the well-known metadata references here: https://kubernetes.io/docs/reference/labels-annotations-taints/

---

## General Labels

There are some labels that aren't necessarily meant for organizing workloads as much as they are for attribution to business signals.  This could be for billing, environmental notation, metric selectors, policy actuation, so on.

- `cost-center` - For budgetary alignment
- `financial-party-id` - For cost actor attribution
- `salesforce-id` - SFID Attribution
- `environment` - For environmental labeling of assets, eg production, test, development, etc

---

## Infrastructure Specific Labels

### General

- `cluster-type`
- `cluster-role`
- `cluster-location`
- `cluster-infra`
- `cluster-name`
- `cluster-domain`

### AWS

- `aws-region`: us-east-1

### Azure

### GCP

### VMWare

### Bare Metal

- `datacenter_building`: 
- `datacenter_floor`: 
- `datacenter_aisle`: 
- `datacenter_rack`: 
- `server_id`: 
- `baremetal_vendor`: `hpe` | `dell` | `supermicro` ...

---

## Application-centric Labels

- `app`: alertmanager
- `app.kubernetes.io/part-of`: openshift-monitoring
- `app.kubernetes.io/instance`: main
- `app.kubernetes.io/version`: 0.24.0
- `app.kubernetes.io/component`: alert-router
- `app.kubernetes.io/managed-by: prometheus-operator
- `app.kubernetes.io/name: alertmanager

---

## Auto-generated Labels

Often you'll find some auto-generated or auto-applied labels on certain objects that the platform controllers set automatically - these are handy to use for targeting and selecting other objects, knowing the portability is met across clusters.

### On Nodes

- `kubernetes.io/arch`: amd64
- `kubernetes.io/hostname`: app-1
- `kubernetes.io/os`: linux
- `node-role.kubernetes.io/infra`: ''
- `node-role.kubernetes.io/master`: ''
- `node-role.kubernetes.io/worker`: ''
- `node.openshift.io/os_id`: rhcos

### On Machines

- `machine.openshift.io/cluster-api-cluster`: core-ocp-f66s9
- `machine.openshift.io/cluster-api-machine-role`: master
- `machine.openshift.io/cluster-api-machine-type`: master
