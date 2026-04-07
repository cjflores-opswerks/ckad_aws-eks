

> [!NOTE] Use the below command in the terminal to get information on tags.
>aws ec2 describe-subnets --query "Subnets[*].{SubnetId:SubnetId, Tags:Tags}" | jq


### Configure the AWS LoadBalancer Controller

2. Locate the Load Balancer YAML file (`v2_7_2_full.yaml`) under in the present directory.

```
/root/amazon-elastic-kubernetes-service-course/eks
```

3. Edit the YAML file to use the cluster `demo-eks`. Update the following section:
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    ...
    name: aws-load-balancer-controller
    namespace: kube-system
    spec:
        ...
        template:
            spec:
                containers:
                    - args:
	                        - --cluster-name=<your-cluster-name> ## Update this to demo-eks
    ```
    
4. Apply the Load Balancer controller using the following command:
    

```sh
    kubectl apply -f v2_7_2_full.yaml
```

This command will create all the necessary resources for the AWS Load Balancer to operate efficiently.




Create a deployment (**`webapp-color`**) and service (**`webapp-color`**) to expose the deployment.  
Use the below yaml files for deployment.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-color
  labels:
    app: webapp-color
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp-color
  template:
    metadata:
      labels:
        app: webapp-color
    spec:
      containers:
      - name: webapp-color
        image: kodekloud/webapp-color
        ports:
        - containerPort: 8080
```

Create a service with below yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-color
  namespace: default
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
    service.beta.kubernetes.io/aws-load-balancer-internal: "false" # Ensure this line is not set to "true"
spec:
  type: LoadBalancer
  selector:
    app: webapp-color
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

Create the deployment and service with the below command.


```sh
kubectl create -f <name of file>
```




Now, we create a deployment with service as ingress type.  
In the `/root/amazon-elastic-kubernetes-service-course/eks` folder, there is a `2048_full.yaml` file. Create a new deployment with this yaml file.

---
apiVersion: v1
kind: Namespace
metadata:
  name: game-2048
---
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: game-2048
  name: deployment-2048
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: app-2048
  replicas: 5
  template:
    metadata:
      labels:
        app.kubernetes.io/name: app-2048
    spec:
      containers:
      - image: public.ecr.aws/l6m2t8p7/docker-2048:latest
        imagePullPolicy: Always
        name: app-2048
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  namespace: game-2048
  name: service-2048
spec:
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
  type: NodePort
  selector:
    app.kubernetes.io/name: app-2048
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: game-2048
  name: ingress-2048
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
  ingressClassName: alb
  rules:
    - http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: service-2048
              port:
                number: 80


