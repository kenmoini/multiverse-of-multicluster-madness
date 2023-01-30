# AWS IPI Cluster Creation Cheat Sheet

Ok so since ROSA doesn't support additional trust bundles or ODF, you may want to deploy a cluster to AWS via IPI.  Here's a bit of YAML and things that could make that easier to get your {Geo,Hub-of-}Hubs up and running.  Note the modifications to make at the bottom - otherwise the node configuraiton should suffice.

```yaml
additionalTrustBundlePolicy: Proxyonly
apiVersion: v1
compute:
- architecture: amd64
  hyperthreading: Enabled
  name: worker
  platform:
    aws:
      type: m5.8xlarge
  replicas: 3
controlPlane:
  architecture: amd64
  hyperthreading: Enabled
  name: master
  platform:
    aws:
      type: m5.2xlarge
  replicas: 3
metadata:
  creationTimestamp: null
  name: hoh
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 10.0.0.0/16
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
platform:
  aws:
    region: us-east-2
publish: External
baseDomain: <BASE_DOMAIN>
pullSecret: '<INSERT_PULL_SECRET_MINIFIED_JSON_BLOB>'
sshKey: |
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDYtqiYWlWtyXhF0azxbmLb34tMb7h0MyGCTrtCt0W/KDCvv5IwZ5+u7qYZTziOUXYQ3xR5Y8I/1n149TIi8xqcdg7hKa+WyrFJJUEJGXyXW0DPtzORToz04PMz6E2/NeGFnXoEazAFu2VUwgdNMVWgw0w7nvgmBNqQilD1YF9Opbgg9mjwNKwzIwt2Xhca7Jz48KvwF2yIbWyXltdWv/t5k+5gpssMDrQVjiEl2gqfwrFJqB/99rjy95lcJbm9wII7fi/6zweXwioxCmM2NTR7YppQ0juxNlJ8ry5rYwNkXAeSUehn/qdc2RlLD2rYW9ro2PUIyXjdClFlZ08kbKKgTaXCL1KNXEKJb+0xm0RVcFdoS7g4n4JcBLcy5nXuCk6wcKb9SOTVc9iuczxEvRPeVfRZIr6gJZCwEyIF5+gnu8s39YBa0ABW9P7FZrDgzvIZjsIuT4RzqosEtp/OrXxJ/5RgxBxK1ODegOQ4wtCWn74WfcyRBEhDy040m7PhTeckHJrsf5PjiuI720dGc+YCEUgabzIikoJ74JxInun8i5vXjw/ZQx+ftGsD92RT5Tu9kFBoOc5lr6mbYx+s14TY8inNsCpluz/yxWMO8tDs6HYNP+B5mHpWCdsswXK+SRtnIZZGI4zfSBV8Gjc+8oZGP6gqyN0R1ssbkufxp5oMHQ== mvomcm-geo-hub-ztp

```

1. Create an `install-config.yml` file with that similar spec in a directory
2. Run `openshift-install create cluster --dir=that_dir`
3. ?????

Once the cluster is up, you may want some dedicated Infrastructure or Storage Nodes.

