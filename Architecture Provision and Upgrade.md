# Phase 1: Infrastructure Provisioning
## 1.1 Network Setup (VPC & Subnets)
```bash
# Create VPC
aws ec2 create-vpc --cidr-block 10.10.0.0/24 --region us-east-1 \
    --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=academy-group2-vpc}, {Key=env,Value=training}, {Key=team,Value=academy-sre}, {Key=group,Value=group2}]'

# Enable DNS
aws ec2 modify-vpc-attribute --vpc-id <VPC_ID> --enable-dns-support '{"Value":true}'
aws ec2 modify-vpc-attribute --vpc-id <VPC_ID> --enable-dns-hostnames '{"Value":true}'

# Create Public Subnets (For Load Balancers)
aws ec2 create-subnet --vpc-id <VPC_ID> --cidr-block 10.10.0.0/28 --availability-zone us-east-1a \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=academy-group2-public-1a}, {Key=kubernetes.io/role/elb,Value=1}]'

aws ec2 create-subnet --vpc-id <VPC_ID> --cidr-block 10.10.0.16/28 --availability-zone us-east-1b \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=academy-group2-public-1b}, {Key=kubernetes.io/role/elb,Value=1}]'

aws ec2 modify-subnet-attribute --subnet-id <PUBLIC_1A_ID> --map-public-ip-on-launch
aws ec2 modify-subnet-attribute --subnet-id <PUBLIC_1B_ID> --map-public-ip-on-launch

# Create Private Subnets (For Worker Nodes)
aws ec2 create-subnet --vpc-id <VPC_ID> --cidr-block 10.10.0.64/26 --availability-zone us-east-1a \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=academy-group2-private-1a}, {Key=kubernetes.io/role/internal-elb,Value=1}]'

aws ec2 create-subnet --vpc-id <VPC_ID> --cidr-block 10.10.0.128/26 --availability-zone us-east-1b \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=academy-group2-private-1b}, {Key=kubernetes.io/role/internal-elb,Value=1}]'
```
## 1.2 Routing & Gateway Connectivity
```bash

# Internet Gateway
aws ec2 create-internet-gateway \
    --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=academy-group2-igw}]'
aws ec2 attach-internet-gateway --internet-gateway-id <IGW_ID> --vpc-id <VPC_ID>

# NAT Gateway (For Private Subnet Internet Access)
aws ec2 allocate-address --domain vpc
aws ec2 create-nat-gateway --subnet-id <PUBLIC_1A_ID> --allocation-id <EIP_ALLOC_ID> \
    --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=academy-group2-nat}]'

# Route Tables & Routes
aws ec2 create-route-table --vpc-id <VPC_ID> --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=academy-group2-public-rt}]'
aws ec2 create-route --route-table-id <PUBLIC_RT_ID> --destination-cidr-block 0.0.0.0/0 --gateway-id <IGW_ID>

aws ec2 create-route-table --vpc-id <VPC_ID> --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=academy-group2-private-rt}]'
aws ec2 create-route --route-table-id <PRIVATE_RT_ID> --destination-cidr-block 0.0.0.0/0 --nat-gateway-id <NAT_ID>

# Subnet Associations
aws ec2 associate-route-table --route-table-id <PUBLIC_RT_ID> --subnet-id <PUBLIC_1A_ID>
aws ec2 associate-route-table --route-table-id <PUBLIC_RT_ID> --subnet-id <PUBLIC_1B_ID>
aws ec2 associate-route-table --route-table-id <PRIVATE_RT_ID> --subnet-id <PRIVATE_1A_ID>
aws ec2 associate-route-table --route-table-id <PRIVATE_RT_ID> --subnet-id <PRIVATE_1B_ID>
```
# Phase 2: Cluster Deployment (v1.34)
## 2.1 IAM Role Configuration
```bash

# Cluster Role
aws iam create-role --role-name academy-group2-clusterrole --assume-role-policy-document '{
  "Version": "2012-10-17",
  "Statement": [{"Effect": "Allow", "Principal": {"Service": "eks.amazonaws.com"}, "Action": ["sts:AssumeRole", "sts:TagSession"]}]
}'
aws iam attach-role-policy --role-name academy-group2-clusterrole --policy-arn arn:aws:iam::aws:policy/AmazonEKSClusterPolicy
aws iam attach-role-policy --role-name academy-group2-clusterrole --policy-arn arn:aws:iam::aws:policy/AmazonEKSBlockStoragePolicy

# Node Role
aws iam create-role --role-name academy-group2-noderole --assume-role-policy-document '{
  "Version": "2012-10-17",
  "Statement": [{"Effect": "Allow", "Principal": {"Service": "ec2.amazonaws.com"}, "Action": "sts:AssumeRole"}]
}'
aws iam attach-role-policy --role-name academy-group2-noderole --policy-arn arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy
aws iam attach-role-policy --role-name academy-group2-noderole --policy-arn arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy
aws iam attach-role-policy --role-name academy-group2-noderole --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly
```
## 2.2 EKS Cluster & Initial Add-ons
```bash

aws eks create-cluster --name academy-group2-cluster --kubernetes-version 1.34 \
  --role-arn arn:aws:iam::<ACCOUNT_ID>:role/academy-group2-clusterrole \
  --resources-vpc-config "subnetIds=<PUB_1A>,<PUB_1B>,<PRIV_1A>,<PRIV_1B>" \
  --access-config authenticationMode=API_AND_CONFIG_MAP

aws eks wait cluster-active --name academy-group2-cluster

# Standard Add-ons
aws eks create-addon --cluster-name academy-group2-cluster --addon-name vpc-cni
aws eks create-addon --cluster-name academy-group2-cluster --addon-name coredns
aws eks create-addon --cluster-name academy-group2-cluster --addon-name kube-proxy

# EBS CSI Driver with IRSA
eksctl utils associate-iam-oidc-provider --cluster academy-group2-cluster --approve
eksctl create iamserviceaccount --name ebs-csi-controller-sa --namespace kube-system \
  --cluster academy-group2-cluster --role-name academy-group2-ebscsirole --role-only \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy --approve

aws eks create-addon --cluster-name academy-group2-cluster --addon-name aws-ebs-csi-driver \
  --service-account-role-arn arn:aws:iam::<ACCOUNT_ID>:role/academy-group2-ebscsirole

kubectl annotate serviceaccount ebs-csi-controller-sa \
  -n kube-system \
  eks.amazonaws.com/role-arn=arn:aws:iam::<ACCOUNT_ID>:role/academy-group2-ebscsirole
```
## 2.3 Managed Node Group
```bash

aws eks create-nodegroup --cluster-name academy-group2-cluster --nodegroup-name academy-group2-ng-01 \
  --node-role arn:aws:iam::<ACCOUNT_ID>:role/academy-group2-noderole \
  --ami-type AL2023_x86_64_STANDARD --instance-types m7i-flex.large \
  --scaling-config minSize=0,maxSize=3,desiredSize=2 \
  --subnets <PRIV_1A> <PRIV_1B>
```
# Phase 3: Zero-Downtime Upgrade (v1.34 → v1.35)
## 3.1 Deploy Validation Workload
```bash

kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-upgrade-test
spec:
  terminationGracePeriodSeconds: 30  # Give EBS CSI driver time to detach
  replicas: 2
  selector:
    matchLabels:
      app: nginx-upgrade-test
  template:
    metadata:
      labels:
        app: nginx-upgrade-test
    spec:
      containers:
      - name: nginx
        image: nginx:stable
EOF
```
## 3.2 Step-by-Step Upgrade Sequence

### 1. Control Plane Upgrade
```bash

aws eks update-cluster-version --name academy-group2-cluster --kubernetes-version 1.35
watch 'aws eks describe-cluster --name academy-group2-cluster --query "cluster.{Version:version,Status:status}" --output table'
```
### 2. Add-on Upgrades
```bash

# Update each component to the 1.35-compatible version
aws eks update-addon --cluster-name academy-group2-cluster --addon-name vpc-cni
aws eks update-addon --cluster-name academy-group2-cluster --addon-name coredns
aws eks update-addon --cluster-name academy-group2-cluster --addon-name kube-proxy
aws eks update-addon --cluster-name academy-group2-cluster --addon-name aws-ebs-csi-driver
```
### 3. Node Group Upgrade
```bash
# Scale up CoreDNS for HA
kubectl scale deployment coredns -n kube-system --replicas=3

# BEFORE upgrading nodes, ensure "ALLOWED DISRUPTIONS" is not 0
kubectl get pdb -A

# If it IS 0, check pod health and scale if necessary:
kubectl get pods -n kube-system

# If only 1 replica is running, scale it up to allow rotation:
kubectl scale deployment ebs-csi-controller -n kube-system --replicas=2

# This triggers a rolling replacement of EC2 instances
aws eks update-nodegroup-version --cluster-name academy-group2-cluster --nodegroup-name academy-group2-ng-01

watch 'kubectl get nodes -o wide'
```
### 4 Post-Upgrade Verification
```bash

# Verify all nodes are v1.35
kubectl get nodes

# Verify workload health and zero restarts
kubectl get pods -l app=nginx-upgrade-test -o wide
```