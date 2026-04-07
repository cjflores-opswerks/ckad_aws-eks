

> [!NOTE] Create an EKS cluster with the name `demo-eks`. Follow the instructions on the following GitHub repo page.
> Contents

# Deploying the Cluster

[](https://github.com/kodekloudhub/amazon-elastic-kubernetes-service-course/blob/main/docs/deploy.md#deploying-the-cluster)

**IMPORTANT**: Ensure that all resources are created in the `us-east-1` (N. Virginia) region

If you came here from the [Amazon EKS course](https://learn.kodekloud.com/user/courses/aws-eks), this is lab step 3.

1. Clone the Repository
    
    Clone the required repository
    
    ```shell
    git clone https://github.com/kodekloudhub/amazon-elastic-kubernetes-service-course
    ```
    
2. Navigate to the EKS Directory
    
    Change into the EKS directory
    
    ```shell
    cd amazon-elastic-kubernetes-service-course/eks
    ```
    
3. Run the following command. It will check the lab/cloud environment for a few things that need to be correct for the cluster to deploy properly. If it tells you to restart the lab, then please do so. If it still tells you to restart the lab after 2 or 3 attempts, then please report in the forums.
    
    - If _and only if_ you are running this lab directly from a Windows PowerShell terminal, run the following
        
        ```
        .\check-environment.ps1
        ```
        
    - **Otherwise** for everything else (KodeKloud lab terminal, CloudShell, any Linux or Mac), instead run this:
        
        ```shell
        source check-environment.sh
        ```
        
4. Initialize Terraform
    
    Initialize the Terraform configuration
    
    ```shell
    terraform init
    ```
    
5. Plan the Terraform Deployment
    
    Run Terraform plan to review the changes that will be applied
    
    ```shell
    terraform plan
    ```
    
6. Apply the Terraform Configuration
    
    Apply the Terraform configuration to provision the EKS cluster. This step will take up to 10 minutes to complete
    
    ```shell
    terraform apply
    ```
    
    When prompted, type `yes` to confirm.
    
7. Retrieve Outputs
    
    After Terraform completes, note the output values for `NodeAutoScalingGroup`, `NodeInstanceRole`, and `NodeSecurityGroup`. You will see something similar to this
    
    ```
    Outputs:
    
    NodeAutoScalingGroup = "demo-eks-stack-NodeGroup-UUJRINMIFPLO"
    NodeInstanceRole = "arn:aws:iam::058264119838:role/eksWorkerNodeRole"
    NodeSecurityGroup = "sg-003010e8d8f9f32bd"
    ```



> [!NOTE] # Set up access and join nodes
> Contents
1. Create a KUBECONFIG for `kubectl`
    
    ```shell
    aws eks update-kubeconfig --region us-east-1 --name demo-eks
    ```
    
2. Join the worker nodes
    
    1. Download the node authentication ConfigMap
        
        ```
        curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/aws-auth-cm.yaml
        ```
        
    2. Edit the ConfigMap YAML to add in the `NodeInstanceRole` obtained from terraform
        
        ```shell
        vi aws-auth-cm.yaml
        ```
        
    3. Replace the placeholder text `<ARN of instance role (not instance profile)>` with the value of `NodeInstanceRole` from Terraform, then save and exit the editor. The ConfigMap looks like this before editing:
        
        ```yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
        name: aws-auth
        namespace: kube-system
        data:
        mapRoles: |
          - rolearn: <ARN of instance role (not instance profile)> # <- EDIT THIS
            username: system:node:{{EC2PrivateDNSName}}
            groups:
              - system:bootstrappers
              - system:nodes
        
        ```
        
3. Apply the edited ConfigMap
    
    1. Apply the ConfigMap to join the nodes:
        
        ```shell
        kubectl apply -f aws-auth-cm.yaml
        ```
        
    2. Wait around 60 seconds for all nodes to join.
        
4. Verify the Nodes
    
    Verify that the nodes have joined the cluster and are in the `Ready` state. You may need to run the following several times until all nodes are ready.
    
    ```shell
    kubectl get node -o wide
    ```
    
    You should see 3 worker nodes in ready state. Note that with EKS you do not see control plane nodes, as they are managed by AWS.
    
    You can also view the completed cluster in the [EKS Console](https://us-east-1.console.aws.amazon.com/eks/home?region=us-east-1).
    

## Personal AWS Account

[](https://github.com/kodekloudhub/amazon-elastic-kubernetes-service-course/blob/main/docs/nodes.md#personal-aws-account)

If you deployed the cluster into your own AWS account, you should delete resources when finished to avoid unwanted charges and also any risk of account compromise! This is _not_ a security focused production grade deployment! Run the following:

```
terraform destroy
```

> TO CHECK THE CIDR TABLE
> 
>aws ec2 describe-vpcs --query 'Vpcs[*].{VpcId:VpcId,CidrBlock:CidrBlock}' --output table

> First, download the `aws-k8s-cni.yaml` configuration file from the AWS VPC CNI GitHub repository.
> 
> curl -o aws-k8s-cni.yaml https://raw.githubusercontent.com/aws/amazon-vpc-cni-k8s/master/config/master/aws-k8s-cni.yaml


### Verifying ENI and Prefix Assignment

Step 1: Create a new pod and inspect the node to verify ENI and prefix assignment.  
Deploy the pod using the same YAML as in previous lab `nginx`.

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
EOF
```

Step 2: Inspect the node’s ENIs and routes with following commands:

```
kubectl get nodes
kubectl describe node <node-name>
aws ec2 describe-network-interfaces --query 'NetworkInterfaces[*].{ID:NetworkInterfaceId,PrivateIpAddress:PrivateIpAddress}'
aws ec2 describe-route-tables --query 'RouteTables[*].Routes'
```


### Network Policies

Step 1: Deploy network policies to manage pod communication.

```
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
EOF
```

Step 2: Deploy a second pod

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  labels:
    app: test
spec:
  containers:
  - name: busybox
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
EOF
```

Step 3: Verify that communication is blocked.

```
kubectl exec -it test-pod -- ping <nginx-pod-ip>
```


### Network Policies

Step 1: Deploy a network policy that allows communication based on labels.

```
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-nginx
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: nginx
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: test
EOF
```

Step 2: Verify that communication between pods is allowed based on the network policy.  
Ensure that test-pod can communicate with nginx pod based on the network policy.

```
kubectl exec -it test-pod -- ping <nginx-pod-ip>
```