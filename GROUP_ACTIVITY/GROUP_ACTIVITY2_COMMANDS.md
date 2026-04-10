```bash


##KEYWORDS TO SEARCH FOR IN CLI, OR AWS AMAZON
FORNAT:
AWS AMAZON SEARCHBAR - WHAT YOU NEED

CREATE - CREATE, ASSOCIATE
MODIFY - CHANGE, ACTIONS, EDIT, ENABLE
ATTACH - ATTACH

##STEP1 GET TERMINAL ACCESS
aws configure

##STEP2 CONFIRM IDENTITY
aws sts get-caller-identity


##STEP3 CREATE VARIABLES AND CREATE VPC
export GROUP=group6
export REGION=us-east-1
COMMON_TAGS='Key=env,Value=training Key=team,Value=academy-sre Key=group,Value=group6'

##TO FIND THE QUERY PATH. THIS IS ONLY CREATING A DUMMY VPC, LIKE A DRY RUN. SHOWS YOU AN EXAMPLE OUTPUT IF YOU CREATED ONE.
aws ec2 create-vpc --generate-cli-skeleton output | grep -in --color=always "Id"

##AFTER FINDING OUT THE QUERY PATH FOR Example: "Vpc.VpcId". Create the VPC now. 
VPC_ID=$(aws ec2 create-vpc \
  --region "$REGION" \
  --cidr-block 10.10.0.0/24 \
  --query 'Vpc.VpcId' \
  --output text)

##PUT THE TAGS ON THIS VPC
aws ec2 create-tags \
  --region "$REGION" \
  --resources "$VPC_ID" \
  --tags Key=Name,Value="academy-$GROUP-vpc" ${(z)COMMON_TAGS}

##TO ENABLE DNS HOSTNAMES AND DNS RESOLUTION
aws ec2 modify-vpc-attribute \
  --vpc-id "$VPC_ID" \
  --enable-dns-support '{"Value":true}'

aws ec2 modify-vpc-attribute \
  --vpc-id "$VPC_ID" \
  --enable-dns-hostnames '{"Value":true}'

##TO CREATE THE FOUR SUBNETS
SNP1A_ID=$(aws ec2 create-subnet \
  --vpc-id "$VPC_ID" \
  --availability-zone us-east-1a \
  --cidr-block 10.10.0.0/28 \
  --query 'Subnet.SubnetId' \
  --output text)

  SNP1B_ID=$(aws ec2 create-subnet \
    --vpc-id "$VPC_ID" \
    --availability-zone us-east-1b \
    --cidr-block 10.10.0.16/28 \
    --query 'Subnet.SubnetId' \
    --output text)

  SNPR1A_ID=$(aws ec2 create-subnet \
    --vpc-id "$VPC_ID" \
    --availability-zone us-east-1a \
    --cidr-block 10.10.0.64/26 \
    --query 'Subnet.SubnetId' \
    --output text)

  SNPR1B_ID=$(aws ec2 create-subnet \
    --vpc-id "$VPC_ID" \
    --availability-zone us-east-1b \
    --cidr-block 10.10.0.128/26 \
    --query 'Subnet.SubnetId' \
    --output text)


  ## TO VERIFY THE TAGS ON THE SUBNETS YOU CREATED
  aws ec2 describe-subnets \
    --subnet-ids "$SNP1A_ID" "$SNP1B_ID" "$SNPR1A_ID" "$SNPR1B_ID" \
    --query 'Subnets[].Tags[?Key==`Name` || Key==`kubernetes.io/role/elb` || Key==`kubernetes.io/role/internal-elb` || Key==`env` || Key==`team` || Key==`group`]' \
    --output table

  ## TO ENABLE AUTO ASSIGN PUBLIC IPV4 ADDRESS
    aws ec2 modify-subnet-attribute --subnet-id subnet-1a2b3c4d --map-public-ip-on-launch

##TO CREATE INTERNET GATEWAY
IG_ID=$(aws ec2 create-internet-gateway \
  --query 'InternetGateway.InternetGatewayId' \
  --output text)

  ## TO TAG THE GATEWAY
  aws ec2 create-tags \
  --resources "$IG_ID" \
  --tags Key=Name,Value="academy-$GROUP-igw" ${(z)COMMON_TAGS}

  ##TO ATTACH THE GATEWAY TO THE VPC
  aws ec2 attach-internet-gateway \
  --internet-gateway-id "$IG_ID" \
  --vpc-id "$VPC_ID"

##FIND THE QUERY PATH FIRST FOR NAT GATEWAY
aws ec2 create-nat-gateway --generate-cli-skeleton output | grep -in  --color=always "ID"

  ##CREATE THE ELASTIC IP ALLOCATION FIRST
  EIP_ALLOC_ID=$(aws ec2 allocate-address \
  --domain vpc \
  --query 'AllocationId' \
  --output text)

  ## CREATE THE NAT GATEWAY
  NAT_ID=$(aws ec2 create-nat-gateway \
    --subnet-id "$SNP1A_ID" \
    --allocation-id "$EIP_ALLOC_ID" \
    --query 'NatGateway.NatGatewayId' \
    --output text)
  
  ## APPLY THE TAGS TO THE NAT GATEWAY
  aws ec2 create-tags \
    --resources "$NAT_ID" \
    --tags Key=Name,Value="academy-$GROUP-nat" ${(z)COMMON_TAGS}

  ## WAIT FOR THE GATEWAY TO BE CREATED, IF YOU CAN TYPE AGAIN TO TERMINAL ,THAT MAENS CREATTION SUCCESFUL
  aws ec2 wait nat-gateway-available \
    --nat-gateway-ids "$NAT_ID"

##CREATE ROUTE TABLE
RTPUB_ID=$(aws ec2 create-route-table \
  --vpc-id "$VPC_ID" \
  --query 'RouteTable.RouteTableId' \
  --output text)

  ##OPTIONAL ONLY.(INCASE YOU TYPO'D ON THE VARIABLE NAME)
  NEW_RTP_ID="$RTP_ID"
  unset RTP_ID

  aws ec2 create-tags \
    --resources "$RTPUB_ID" \
    --tags Key=Name,Value="academy-$GROUP-public-rt" ${(z)COMMON_TAGS}

  aws ec2 create-route \
    --route-table-id "$RTPUB_ID" \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id "$IG_ID"

  aws ec2 associate-route-table \
    --route-table-id "$RTPUB_ID" \
    --subnet-id "$SNP1A_ID"

  aws ec2 associate-route-table \
    --route-table-id "$RTPUB_ID" \
    --subnet-id "$SNP1B_ID"
  
  
  ##CREATE THE PRIVATE ROUTING TABLE NOW
  RTPRIV_ID=$(aws ec2 create-route-table \
  --vpc-id "$VPC_ID" \
  --query 'RouteTable.RouteTableId' \
  --output text)

  aws ec2 create-tags \
    --resources "$RTPRIV_ID" \
    --tags Key=Name,Value="academy-$GROUP-private-rt" ${(z)COMMON_TAGS}

  aws ec2 create-route \
    --route-table-id "$RTPRIV_ID" \
    --destination-cidr-block 0.0.0.0/0 \
    --nat-gateway-id "$NG_ID"

  aws ec2 associate-route-table \
    --route-table-id "$RTPRIV_ID" \
    --subnet-id "$SNPR1A_ID"

  aws ec2 associate-route-table \
    --route-table-id "$RTPRIV_ID" \
    --subnet-id "$SNPR1B_ID"
  
## CREATING THE CLUSTER NOW

  ##CREATE THE CLUSTER TRUST POLICY FIRST
  cat > eks-cluster-role-trust-policy.json <<'EOF'
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "eks.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
  
  ##CREATE THE ROLES FOR THE CLUSTER AND ATTACH THE POLICIES 
  aws iam create-role \
    --role-name group6-eks-role-sample \
    --assume-role-policy-document file://eks-cluster-role-trust-policy.json

  aws iam attach-role-policy \
    --role-name group6-eks-role-sample \
    --policy-arn arn:aws:iam::aws:policy/AmazonEKSClusterPolicy

  

  ## GET THE CLUSTER ARN ROLE FIRST
  CLUSTER_ROLE_ARN=$(aws iam get-role \
  --role-name group6-eks-role-sample \
  --query 'Role.Arn' \
  --output text)

## CREATE THE CLUSTER NOW
aws eks create-cluster \
  --name academy-group6-cluster \
  --kubernetes-version 1.34 \
  --role-arn "$CLUSTER_ROLE_ARN" \
  --resources-vpc-config subnetIds="$SNP1A_ID","$SNP1B_ID","$SNPR1A_ID","$SNPR1B_ID" \
  --tags env=training,team=academy-sre,group=group6 \
  --access-config authenticationMode=API_AND_CONFIG_MAP

## ADDING THE FOUR ADDINS
aws eks create-addon \
  --cluster-name academy-group6-cluster \
  --addon-name vpc-cni \
  --resolve-conflicts OVERWRITE

aws eks create-addon \
  --cluster-name academy-group6-cluster \
  --addon-name coredns \
  --resolve-conflicts OVERWRITE

aws eks create-addon \
  --cluster-name academy-group6-cluster \
  --addon-name kube-proxy \
  --resolve-conflicts OVERWRITE

aws eks create-addon \
  --cluster-name academy-group6-cluster \
  --addon-name aws-ebs-csi-driver

## TO HAVE ACCESS TO THE KUBECTL COMMANDS 
aws eks update-kubeconfig \
  --region us-east-1 \
  --name academy-group6-cluster

## CREATING A WORKLOAD BEFORE UPGRADING
kubectl apply -f - <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-upgrade-test
  namespace: default
  labels:
    app: nginx-upgrade-test
spec:
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
        ports:
        - containerPort: 80
EOF


##CREATE A NODEGROUP

##CREATE THE JSON FILE AND CREATE IAM ROLE WITH THAT JSON FILE
  cat > eks-node-role-trust-policy.json <<'EOF'
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Service": "ec2.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }
EOF
##CREATING AN IAMROLE AND  AND ATTACHING THE POLICIES TO IT
  aws iam create-role \
    --role-name group6-eks-node-role \
    --assume-role-policy-document file://eks-node-role-trust-policy.json

  aws iam attach-role-policy \
    --role-name group6-eks-node-role \
    --policy-arn arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy

  aws iam attach-role-policy \
    --role-name group6-eks-node-role \
    --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryPullOnly

aws iam attach-role-policy \
  --role-name group6-eks-node-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy

aws iam attach-role-policy \
  --role-name group6-eks-node-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy

##CREATING A NODEGROUP 
NODE_ROLE_ARN=$(aws iam get-role \
  --role-name group6-eks-node-role \
  --query 'Role.Arn' \
  --output text)

aws eks create-nodegroup \
  --cluster-name academy-group6-cluster \
  --nodegroup-name academy-group6-ng-01 \
  --node-role "$NODE_ROLE_ARN" \
  --subnets "$SNPR1A_ID" "$SNPR1B_ID" \
  --ami-type AL2023_x86_64_STANDARD \
  --capacity-type ON_DEMAND \
  --instance-types m7i-flex.large \
  --scaling-config minSize=0,maxSize=3,desiredSize=2 \
  --tags env=training,team=academy-sre,group=group6


##VERIFY ALL PODS ARE RUNNING, IF NOT RUNNING APPLY THE ROLE BELOW
aws iam attach-role-policy \
  --role-name group6-eks-role-sample \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKSBlockStoragePolicy

##then run this to restart ebs csi
kubectl rollout restart deployment ebs-csi-controller -n kube-system
kubectl rollout restart daemonset ebs-csi-node -n kube-system


##TO FIX CRASHLOOP BACKOFF

ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
OIDC_ISSUER=$(aws eks describe-cluster --name academy-group6-cluster --query "cluster.identity.oidc.issuer" --output text)
OIDC_HOSTPATH=${OIDC_ISSUER#https://}

cat > trust-policy-oidc.json <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::${ACCOUNT_ID}:oidc-provider/${OIDC_HOSTPATH}"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "${OIDC_HOSTPATH}:sub": "system:serviceaccount:kube-system:ebs-csi-controller-sa",
          "${OIDC_HOSTPATH}:aud": "sts.amazonaws.com"
        }
      }
    }
  ]
}
EOF

aws iam create-role \
  --role-name AmazonEKS_EBS_CSI_DriverRole \
  --assume-role-policy-document file://trust-policy-oidc.json

aws iam attach-role-policy \
--role-name AmazonEKS_EBS_CSI_DriverRole \
--policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy

aws eks update-addon \
--cluster-name academy-group6-cluster \
--addon-name aws-ebs-csi-driver \
--service-account-role-arn arn:aws:iam::315527911029:role/AmazonEKS_EBS_CSI_DriverRole \
--resolve-conflicts OVERWRITE

kubectl rollout restart deployment ebs-csi-controller -n kube-system
kubectl rollout restart daemonset ebs-csi-node -n kube-system


##IF IT DOESNT WORK AND EBS CLI TAKES OTO LONG TO UPDARTE, JUST DEELTE IT 
CLUSTER_NAME=academy-group6-cluster
REGION=us-east-1
ROLE_ARN=arn:aws:iam::315527911029:role/AmazonEKS_EBS_CSI_DriverRole

aws eks delete-addon \
  --cluster-name "$CLUSTER_NAME" \
  --region "$REGION" \
  --addon-name aws-ebs-csi-driver

  aws eks create-addon \
  --cluster-name "$CLUSTER_NAME" \
  --region "$REGION" \
  --addon-name aws-ebs-csi-driver \
  --addon-version v1.57.1-eksbuild.1 \
  --service-account-role-arn "$ROLE_ARN"

##OPTIONAL (THIS IS ONLY FOR UPDATING, IF THE ROLE AmazonEKS_EBS_CSI_DriverRole ALREADY EXIST, USE THIS INSTEAD OF CREATING A NEW ONE)
aws iam update-assume-role-policy \
  --role-name AmazonEKS_EBS_CSI_DriverRole \
  --policy-document file://trust-policy-oidc.json

##PART2 UPDATING CONTROL PLANE TO A NEW VERSION AND BEYOND
REGION=us-east-1
CLUSTER_NAME=academy-group6-cluster
NODEGROUP_NAME=academy-group6-ng-01
TARGET_VERSION=1.35

aws eks update-cluster-version \
  --name "$CLUSTER_NAME" \
  --region "$REGION" \
  --kubernetes-version "$TARGET_VERSION"

aws eks describe-cluster \
  --name academy-group6-cluster \
  --region us-east-1 \
  --query "cluster.{Name:name,Version:version,Status:status}" \
  --output table

kubectl get pods -l app=nginx-upgrade-test -o wide

aws eks list-addons \
  --cluster-name academy-group6-cluster \
  --region us-east-1 \
  --output table

aws eks describe-addon-versions \
  --region "$REGION" \
  --addon-name kube-proxy \
  --kubernetes-version "$TARGET_VERSION" \
  --query 'addons[0].addonVersions[].addonVersion' \
  --output text

aws eks describe-addon-versions \
  --region "$REGION" \
  --addon-name coredns \
  --kubernetes-version "$TARGET_VERSION" \
  --query 'addons[0].addonVersions[].addonVersion' \
  --output text

aws eks describe-addon-versions \
  --region "$REGION" \
  --addon-name vpc-cni \
  --kubernetes-version "$TARGET_VERSION" \
  --query 'addons[0].addonVersions[].addonVersion' \
  --output text

aws eks describe-addon-versions \
  --region "$REGION" \
  --addon-name aws-ebs-csi-driver \
  --kubernetes-version "$TARGET_VERSION" \
  --query 'addons[0].addonVersions[].addonVersion' \
  --output text

aws eks update-addon \
  --cluster-name "$CLUSTER_NAME" \
  --region "$REGION" \
  --addon-name kube-proxy \
  --addon-version "v1.35.3-eksbuild.2" \
  --resolve-conflicts OVERWRITE

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --addon-name kube-proxy \
  --query "addon.status" \
  --output text

aws eks update-addon \
  --cluster-name "$CLUSTER_NAME" \
  --region "$REGION" \
  --addon-name coredns \
  --addon-version "v1.13.2-eksbuild.4" \
  --resolve-conflicts OVERWRITE

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --addon-name coredns \
  --query "addon.status" \
  --output text

aws eks update-addon \
  --cluster-name "$CLUSTER_NAME" \
  --region "$REGION" \
  --addon-name vpc-cni \
  --addon-version "v1.21.1-eksbuild.7" \
  --resolve-conflicts OVERWRITE

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --addon-name vpc-cni \
  --query "addon.status" \
  --output text

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --addon-name aws-ebs-csi-driver \
  --query "addon.status" \
  --output text

kubectl get pods -o wide

aws eks update-nodegroup-version \
  --cluster-name academy-group6-cluster \
  --nodegroup-name academy-group6-ng-01 \
  --region us-east-1 \
  --kubernetes-version 1.35

aws eks describe-nodegroup \
  --cluster-name academy-group6-cluster \
  --nodegroup-name academy-group6-ng-01 \
  --region us-east-1 \
  --query "nodegroup.{Name:nodegroupName,Status:status,Version:version}" \
  --output table

kubectl get pods -l app=nginx-upgrade-test -o wide -w

aws eks describe-cluster \
  --name academy-group6-cluster \
  --region us-east-1 \
  --query "cluster.{Name:name,Version:version,Status:status}" \
  --output table

kubectl get nodes -o wide

aws eks list-addons \
  --cluster-name academy-group6-cluster \
  --region us-east-1 \
  --output table

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --region us-east-1 \
  --addon-name kube-proxy \
  --query "addon.{Name:addonName,Version:addonVersion,Status:status}" \
  --output table

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --region us-east-1 \
  --addon-name coredns \
  --query "addon.{Name:addonName,Version:addonVersion,Status:status}" \
  --output table

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --region us-east-1 \
  --addon-name vpc-cni \
  --query "addon.{Name:addonName,Version:addonVersion,Status:status}" \
  --output table

aws eks describe-addon \
  --cluster-name academy-group6-cluster \
  --region us-east-1 \
  --addon-name aws-ebs-csi-driver \
  --query "addon.{Name:addonName,Version:addonVersion,Status:status}" \
  --output table

kubectl get pods -l app=nginx-upgrade-test -o wide

kubectl get pods -l app=nginx-upgrade-test

kubectl get node











