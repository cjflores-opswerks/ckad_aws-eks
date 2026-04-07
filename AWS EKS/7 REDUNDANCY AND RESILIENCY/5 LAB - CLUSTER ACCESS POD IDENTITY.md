
### Verify Current User Access

Verify that the current user has access to EKS clusters.  
Step 1: Display the kubeconfig settings

```
kubectl config view
```

Step 2: Get All Pods

```
kubectl get pods --all-namespaces
```


### Create an IAM User and Access Key in AWS

Follow the steps below to create an IAM user named `eks-user` and generate an access key for this user.

#### Step 1: Create an IAM User

Run the following command to create a new IAM user named `iamuser-eksuser`:

```sh
aws iam create-user --user-name iamuser-eksuser
```

#### Step 2: Create an Access Key for the IAM User

#### Step 2: Create an Access Key for the IAM User

Generate an access key for the `iamuser-eksuser` and save the output to a JSON file for future reference:

```sh
aws iam create-access-key --user-name iamuser-eksuser | tee /tmp/create_output.json
```

### Verify `iamuser-eksuser` Access

#### Step 1: Update aws-auth-cm.yaml to Map Users

1. Open the `aws-auth-cm.yaml` file for editing.
2. Update the file to include the new user created with the previous AWS CLI commands:

```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: aws-auth
     namespace: kube-system
   data:
     mapRoles: |
       - rolearn: arn:aws:iam::891377054545:role/eks-demo-node
         username: system:node:{{EC2PrivateDNSName}}
         groups:
           - system:bootstrappers
           - system:nodes
# Add mapUsers as below (ARN can be get in the AWS CLI command result in the previous step):
     mapUsers: |
       - userarn: arn:aws:iam::891377054545:user/iamuser-eksuser
         username: iamuser-eksuser
```

3. Apply the updated `aws-auth-cm.yaml` configuration:

```bash
   kubectl apply -f aws-auth-cm.yaml
```

#### Step 2: Verify User Access

Verify the access rights of the new user using the following command:

```bash
   kubectl auth can-i get pod --as iamuser-eksuser
```

Expected result: `No`

### Add Role and RoleBinding for the User

Step 1: Create a role for the user:

```bash
cat <<EOF > user-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: iamuser-eks-role
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "watch"]
- apiGroups: ["extensions", "apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch"]
EOF

kubectl create -f user-role.yaml
```

Step 2: Create a RoleBinding for the user:

```bash
cat <<EOF > user-role-binding.yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: iamuser-eks-binding
subjects:
- kind: User
  name: iamuser-eksuser
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: iamuser-eks-role
  apiGroup: rbac.authorization.k8s.io
EOF

kubectl create -f user-role-binding.yaml
```

