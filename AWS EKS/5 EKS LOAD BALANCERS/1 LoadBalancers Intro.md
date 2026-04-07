> ## Documentation Index
> Fetch the complete documentation index at: https://notes.kodekloud.com/llms.txt
> Use this file to discover all available pages before exploring further.

# LoadBalancers Intro

> This guide explains how Kubernetes manages traffic and automates load balancer provisioning using the AWS Load Balancer Controller.

In this guide, you’ll learn how Kubernetes routes traffic in and out of your cluster and how the AWS Load Balancer Controller automates provisioning ALBs, NLBs, and ELBs for your Services.

## Kubernetes Controllers and the Cloud Controller

Originally, Kubernetes ran all controllers—including the cloud controller—inside the API Server and Controller Manager. Modern setups (like EKS) run the cloud controller separately: it watches Service resources and calls your cloud provider’s API to create load balancers and other infrastructure.

<Frame>
  ![The image illustrates a comparison of Kubernetes controllers, showing a "Main API Server" and a "Cloud Controller" with an icon.](https://kodekloud.com/kk-media/image/upload/v1752862856/notes-assets/images/AWS-EKS-LoadBalancers-Intro/kubernetes-controllers-comparison-api-server.jpg)
</Frame>

## AWS Load Balancer Options

AWS supports three main load balancer types for Kubernetes Services:

| Load Balancer                   | Use Case                                  | Annotation                                               |
| ------------------------------- | ----------------------------------------- | -------------------------------------------------------- |
| Application Load Balancer (ALB) | HTTP/HTTPS routing, host/path-based rules | `service.beta.kubernetes.io/aws-load-balancer-type: alb` |
| Network Load Balancer (NLB)     | TCP/UDP, ultra-low latency                | `service.beta.kubernetes.io/aws-load-balancer-type: nlb` |
| Classic ELB                     | Legacy, limited feature set               | (default when no annotation is set)                      |

<Frame>
  ![The image illustrates different types of load balancers, including a Load Balancer Controller, Application Load Balancer, and Network Load Balancer, with AWS branding.](https://kodekloud.com/kk-media/image/upload/v1752862858/notes-assets/images/AWS-EKS-LoadBalancers-Intro/load-balancers-aws-diagram.jpg)
</Frame>

## How Traffic Flows: Nodes, Pods, and Services

A Kubernetes node runs Pods that serve your application. To expose a Pod externally, you define a Service. Kubernetes maps a port on each node (NodePort) to your Pod’s port behind the scenes.

<Frame>
  ![The image illustrates a concept of a Kubernetes node containing a pod, represented by gears, under the title "Understanding Load Balancer in Kubernetes."](https://kodekloud.com/kk-media/image/upload/v1752862858/notes-assets/images/AWS-EKS-LoadBalancers-Intro/kubernetes-load-balancer-node-pod-gears.jpg)
</Frame>

### NodePort Service

A Service of type `NodePort` opens the same high port on every node. You can then reach your application at:

```text  theme={null}
http://<node-ip>:<node-port>
# e.g. http://10.0.1.15:35611
```

```yaml  theme={null}
apiVersion: v1
kind: Service
metadata:
  name: myapp-nodeport
spec:
  type: NodePort
  ports:
    - port: 80
      nodePort: 30080
  selector:
    app: myapp
```

<Frame>
  ![The image illustrates a Node Port Service in a Kubernetes cluster, showing the flow from a host through a specific port to multiple pods within the cluster.](https://kodekloud.com/kk-media/image/upload/v1752862860/notes-assets/images/AWS-EKS-LoadBalancers-Intro/node-port-service-kubernetes-cluster-diagram.jpg)
</Frame>

### LoadBalancer Service

Setting `type: LoadBalancer` still opens the NodePort, but also provisions an external load balancer that fronts all nodes and handles health checks, distributing traffic automatically:

```yaml  theme={null}
apiVersion: v1
kind: Service
metadata:
  name: myapp-loadbalancer
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: myapp
```

<Frame>
  ![The image illustrates a Load Balancer Service directing traffic to a Kubernetes cluster, which contains a node managing multiple pods.](https://kodekloud.com/kk-media/image/upload/v1752862861/notes-assets/images/AWS-EKS-LoadBalancers-Intro/load-balancer-kubernetes-cluster-pods.jpg)
</Frame>

### Health Checks and kube-proxy

By default, the cloud load balancer health-checks every node, even those without Pods for this Service. When traffic lands on an empty node, `kube-proxy` reroutes it to a healthy node, possibly incurring cross-AZ hops.

<Callout icon="lightbulb" color="#1CB2FE">
  Set `externalTrafficPolicy: Local` on your Service to ensure only nodes with active Pods are in the load balancer’s target group. This reduces extra hops and cross-zone charges.
</Callout>

```yaml  theme={null}
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  selector:
    app: myapp
```

<Frame>
  ![The image illustrates the role of Kube Proxy in Kubernetes, showing its interaction with multiple pods and a network component.](https://kodekloud.com/kk-media/image/upload/v1752862862/notes-assets/images/AWS-EKS-LoadBalancers-Intro/kube-proxy-kubernetes-pods-network-diagram.jpg)
</Frame>

## AWS Load Balancer Controller

The AWS Load Balancer Controller observes Services (and Ingresses) annotated for a load balancer. It then:

* Provisions ALBs, NLBs, or Classic ELBs
* Configures security groups, listener rules, and target groups
* Keeps resources in sync with Kubernetes objects

Annotations for an ALB might look like:

```yaml  theme={null}
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: alb
    service.beta.kubernetes.io/aws-load-balancer-target-type: ip
```

<Frame>
  ![The image is a diagram about load balancers within a Kubernetes cluster, showing Application Load Balancer, Network Load Balancer, and Elastic Load Balancer.](https://kodekloud.com/kk-media/image/upload/v1752862863/notes-assets/images/AWS-EKS-LoadBalancers-Intro/kubernetes-load-balancers-diagram.jpg)
</Frame>

## Integrating External DNS

To automate DNS entries in Route 53 (or other providers), run [External DNS](https://github.com/kubernetes-sigs/external-dns/) alongside the Load Balancer Controller. External DNS watches Services and Ingresses and creates DNS records pointing to your ALB/NLB.

For example, a Service named `myapp.fun` can automatically generate a `myapp.fun` A record in Route 53 that resolves to your load balancer.

<Frame>
  ![The image illustrates the integration of AWS Route 53 with a Kubernetes cluster using External DNS, showing connections to various AWS services like S3, CloudFront, and Lambda.](https://kodekloud.com/kk-media/image/upload/v1752862865/notes-assets/images/AWS-EKS-LoadBalancers-Intro/aws-route53-kubernetes-external-dns.jpg)
</Frame>

<Frame>
  ![The image illustrates a flowchart for external DNS configuration, showing the progression from "myapp.fun" through Kubernetes External DNS, Route 53, and finally to a Load Balancer.](https://kodekloud.com/kk-media/image/upload/v1752862866/notes-assets/images/AWS-EKS-LoadBalancers-Intro/external-dns-configuration-flowchart.jpg)
</Frame>

## Global Load Balancer

AWS offers a Global Load Balancer for routing traffic across regions. You point a Route 53 alias to it and distribute traffic to regional ALBs/NLBs with failover or weighted policies. Currently, the AWS Load Balancer Controller manages only regional resources, but global support may arrive in future releases.

<Frame>
  ![The image is a diagram illustrating a hierarchy of load balancers, with a global load balancer at the top branching into an application load balancer and a network load balancer.](https://kodekloud.com/kk-media/image/upload/v1752862867/notes-assets/images/AWS-EKS-LoadBalancers-Intro/load-balancer-hierarchy-diagram.jpg)
</Frame>

## Summary

* Kubernetes Services (`NodePort` and `LoadBalancer`) expose Pods to external traffic
* `kube-proxy` handles traffic forwarding when nodes are healthy
* AWS Load Balancer Controller automates ALB, NLB, and ELB provisioning
* External DNS with Route 53 automates DNS record management
* AWS Global Load Balancer enables multi-region routing and failover

Ingress resources can also be integrated with the AWS Load Balancer Controller for advanced HTTP routing.

## Links and References

* [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/)
* [AWS Load Balancer Controller GitHub](https://github.com/kubernetes-sigs/aws-load-balancer-controller)
* [External DNS](https://github.com/kubernetes-sigs/external-dns/)
* [Route 53 Documentation](https://docs.aws.amazon.com/route53/)
* [Amazon EKS Load Balancer Guide](https://docs.aws.amazon.com/eks/latest/userguide/load-balancing.html)

<CardGroup>
  <Card title="Watch Video" icon="video" cta="Learn more" href="https://learn.kodekloud.com/user/courses/aws-eks/module/3242702b-09b2-43c8-9bbe-283c1d64c685/lesson/a432f1ea-d443-4067-b602-501fcb296f9b" />
</CardGroup>


Built with [Mintlify](https://mintlify.com).