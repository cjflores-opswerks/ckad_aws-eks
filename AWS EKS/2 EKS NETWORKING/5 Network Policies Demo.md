
> ## Documentation Index
> Fetch the complete documentation index at: https://notes.kodekloud.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Network Policies Demo

> Learn to secure pod-to-pod communication in Kubernetes using NetworkPolicies through a hands-on guide with practical examples and configurations.

In this hands-on guide, you’ll learn how to secure pod-to-pod communication in Kubernetes using NetworkPolicies. We’ll start with a clean cluster—no workloads, services, or policies—and progressively:

1. Deploy two NGINX apps (`app1` & `app2`)
2. Verify default connectivity
3. Enforce a default-deny posture
4. Open selective ingress/egress rules
5. Test DNS resolution and direct IP connectivity

***

<Callout icon="lightbulb" color="#1CB2FE">
  Ensure you have a running Kubernetes cluster (v1.8+) with a CNI plugin that supports NetworkPolicies (e.g., Calico, Cilium).
</Callout>

## 1. Deploying Two Sample Applications

We’ll create two deployments and expose each via a ClusterIP service.

### 1.1 app1 Deployment & Service (`app1.deploy.yaml`)

```yaml  theme={null}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: "1"
  template:
    metadata:
      labels:
        app: "1"
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app1
spec:
  type: ClusterIP
  selector:
    app: "1"
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

### 1.2 app2 Deployment & Service (`app2.deploy.yaml`)

```yaml  theme={null}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: "2"
  template:
    metadata:
      labels:
        app: "2"
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app2
spec:
  type: ClusterIP
  selector:
    app: "2"
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Apply both manifests and confirm pods are running:

```bash  theme={null}
kubectl apply -f app1.deploy.yaml -f app2.deploy.yaml
kubectl get pods
```

Expected output:

```text  theme={null}
NAME                     READY   STATUS    RESTARTS   AGE
app1-xxxxxxxxxx-xxxxx    1/1     Running   0          ...
app2-xxxxxxxxxx-xxxxx    1/1     Running   0          ...
```

## 2. Verifying Basic Connectivity

By default, all pods can talk to each other. From inside `app1`, curl the `app2` service:

```bash  theme={null}
kubectl exec -it \
  $(kubectl get pod -l app=1 -o jsonpath='{.items[0].metadata.name}') \
  -- curl http://app2
  
  
  ## CAN ALSO USE THIS BELOW FOR EASIER CURL
  kubectl exec -it <PodName> -- curl http://app2
```


You should see the NGINX welcome page:

```html  theme={null}
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
```

Repeat from `app2` to `app1` to confirm bi-directional access.

## 3. Applying a Default-Deny NetworkPolicy

Now enforce a zero-trust posture by blocking all ingress and egress in the `default` namespace.

```yaml  theme={null}
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: default
spec:
  podSelector: {}                   # Selects all pods
  policyTypes:
  - Ingress
  - Egress
```

```bash  theme={null}
kubectl apply -f default-deny.networkpolicy.yaml
kubectl get networkpolicies.networking.k8s.io
```

Output:

```text  theme={null}
NAME           POD-SELECTOR   POLICY-TYPES      AGE
default-deny   <all pods>     Ingress,Egress    ...
```

<Callout icon="triangle-alert" color="#FF6B6B">
  This policy blocks **all** traffic, including DNS queries. Pods will no longer resolve service names or reach cluster DNS.
</Callout>

Test connectivity failure:

```bash  theme={null}
kubectl exec -it \
  $(kubectl get pod -l app=2 -o jsonpath='{.items[0].metadata.name}') \
  -- curl http://app1
# curl: (6) Could not resolve host: app1
```

## 4. Allowing Traffic Between app1 and app2

We’ll create two policies to selectively permit pod-to-pod and DNS traffic.

### 4.1 allow-app1 (`allow-app1.networkpolicy.yaml`)

```yaml  theme={null}
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app1
spec:
  podSelector:
    matchLabels:
      app: "1"
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: "2"
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: "2"
  - to:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
```

### 4.2 allow-app2 (`allow-app2.networkpolicy.yaml`)

```yaml  theme={null}
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app2
spec:
  podSelector:
    matchLabels:
      app: "2"
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: "1"
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: "1"
```

Apply both policies:

```bash  theme={null}
kubectl apply -f allow-app1.networkpolicy.yaml
kubectl apply -f allow-app2.networkpolicy.yaml
kubectl get networkpolicies.networking.k8s.io
```

## 5. Testing Connectivity with NetworkPolicies

| Test Case               | Command                                                               | Expected Result                   |
| ----------------------- | --------------------------------------------------------------------- | --------------------------------- |
| Direct Service IP       | `curl http://$(kubectl get svc app1 -o jsonpath='{.spec.clusterIP}')` | Success (HTTP 200)                |
| DNS Name Resolution     | `curl http://app1`                                                    | Success after DNS rule is added   |
| Blocked DNS (pre-allow) | `curl http://app1` (without egress rule for kube-dns)                 | Failure: `Could not resolve host` |

1. **Direct IP**
   ```bash  theme={null}
   IP=$(kubectl get svc app1 -o jsonpath='{.spec.clusterIP}')
   kubectl exec -it $(kubectl get pod -l app=2 -o jsonpath='{.items[0].metadata.name}') -- curl http://$IP
   ```
2. **Service Name (DNS)**
   ```bash  theme={null}
   kubectl exec -it $(kubectl get pod -l app=2 -o jsonpath='{.items[0].metadata.name}') -- curl http://app1
   ```

***

## Links and References

* [Kubernetes NetworkPolicy Concepts](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
* [Calico Network Policies](https://docs.projectcalico.org/security/calico-network-policy)
* [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/)

***

By following this tutorial, you’ve implemented a default-deny network posture and selectively opened up pod-to-pod and DNS traffic between `app1` and `app2`. This approach helps you secure microservices communication with fine-grained policies.

<CardGroup>
  <Card title="Watch Video" icon="video" cta="Learn more" href="https://learn.kodekloud.com/user/courses/aws-eks/module/fdf5f38f-ffcc-4b70-bde9-751c06d39ac1/lesson/9736608e-5c96-49ea-b95e-89d1d5263771" />

  <Card title="Practice Lab" icon="installation" cta="Learn more" href="https://learn.kodekloud.com/user/courses/aws-eks/module/fdf5f38f-ffcc-4b70-bde9-751c06d39ac1/lesson/25419f99-dffc-4624-93e8-253cf9703618" />
</CardGroup>


Built with [Mintlify](https://mintlify.com).