# Cheat Sheet - ROSA Cluster Creation

To quickly create a Hub-of-Hub cluster via ROSA, run the following:

```bash
# Install the AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Install the ROSA CLI
wget https://mirror.openshift.com/pub/openshift-v4/clients/rosa/latest/rosa-linux.tar.gz
tar zxvf rosa-linux.tar.gz && rm rosa-linux.tar.gz
sudo mv rosa /usr/local/bin

# Install the OC CLI
curl -L https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz | tar -xvzf - -C /usr/local/bin/ oc && chmod 755 /usr/local/bin/oc && ln -s /usr/local/bin/oc /usr/local/bin/kubectl

## ROSA Getting Started: https://console.redhat.com/openshift/create/rosa/getstarted

## Run the AWS Configuration if you haven't before
aws configure

## Check for AWS Roles
aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing"
# Create if it doesn't exist
aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"

## Log into rosa
rosa login --token="eyJhbG..."

## Create needed roles
rosa create account-roles --mode auto --region us-east-2

## Verify quota
rosa verify quota

## Create the cluster
rosa create cluster --cluster-name hoh-rosa --sts --region us-east-2 --replicas 3 --compute-machine-type m5.2xlarge --machine-cidr 10.0.0.0/16 --service-cidr 172.30.0.0/16 --pod-cidr 10.128.0.0/14 --host-prefix 23

rosa create operator-roles --cluster hoh-rosa --region us-east-2
rosa create oidc-provider --cluster hoh-rosa --region us-east-2

## Watch the cluster
rosa describe cluster -c hoh-rosa --region us-east-2
rosa logs install -c hoh-rosa --watch --region us-east-2

## Create the IDP
rosa create idp --cluster=hoh-rosa --region=us-east-2 --name=RedHatSSO --type=google --hosted-domain=redhat.com \
  --client-id=$(cat /mnt/remoteWork/.gcp-oauth-cid) \
  --client-secret=$(cat /mnt/remoteWork/.gcp-oauth-cs)

## Attach the role
rosa grant user cluster-admin --cluster=hoh-rosa --region=us-east-2 --user="kmoini@redhat.com"
rosa grant user cluster-admin --cluster=hoh-rosa --region=us-east-2 --user="mtipton@redhat.com"
rosa grant user cluster-admin --cluster=hoh-rosa --region=us-east-2 --user="tsikes@redhat.com"
```

## Deleting the Cluster

```bash
## Delete the cluster
rosa delete cluster --cluster=hoh-rosa --watch

## Delete the OIDC provider and roles with the bits it gives ya
CLUSTER_ID="ID_HERE"

rosa delete oidc-provider -c $CLUSTER_ID
rosa delete operator-roles -c $CLUSTER_ID
```
