# Metadata Guide

A crucial part of scale out architecture is labeling and tagging your assets - this gives you atomic and ensemble based matching of your resources.  There are going to be a lot of labels/tags that are going to be applied to your objects and every organization's set of labels looks different - but there are some common attributes or features that you'll find below that could jump start your own efforts.

## General Labels

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

- `beta.kubernetes.io/arch`: amd64
- `beta.kubernetes.io/os`: linux
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
