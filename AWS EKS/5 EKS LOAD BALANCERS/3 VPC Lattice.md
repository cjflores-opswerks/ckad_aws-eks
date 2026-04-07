	> ## Documentation Index
> Fetch the complete documentation index at: https://notes.kodekloud.com/llms.txt
> Use this file to discover all available pages before exploring further.

# VPC Lattice

> This article explores the Kubernetes Gateway API and its implementation on AWS through VPC Lattice, covering traffic flow, advanced features, and service networking.

In this article, we’ll dive into the Kubernetes Gateway API—the next-generation v2 of Ingress—and see how AWS implements it via VPC Lattice. You’ll learn:

* Traffic flow into Kubernetes clusters
* Advanced features of the Gateway API
* How VPC Lattice extends service networking across VPCs, accounts, and regions

***

## Kubernetes Gateway API Overview

Ingress controllers route Layer 7 traffic based on hosts or URL paths. The Gateway API extends this by supporting multiple protocols (HTTP, TCP, UDP, gRPC, TLS) and offering more granular control.

<Frame>
  ![The image is an introduction to the Kubernetes Gateway API, showing traffic flow through a Gateway API to a cluster.](https://kodekloud.com/kk-media/image/upload/v1752862868/notes-assets/images/AWS-EKS-VPC-Lattice/kubernetes-gateway-api-introduction-traffic-flow.jpg)
</Frame>

In AWS EKS, the **Lattice Controller** serves as a specialized Gateway Controller, managing Gateway API resources for you.

<Frame>
  ![The image illustrates the role of a Lattice Controller in AWS EKS, showing it managing a Gateway API.](https://kodekloud.com/kk-media/image/upload/v1752862869/notes-assets/images/AWS-EKS-VPC-Lattice/lattice-controller-aws-eks-gateway-api.jpg)
</Frame>

***

## Traditional Ingress vs. Gateway API

With a traditional Ingress setup, you deploy an Ingress Controller behind an external Load Balancer. The controller inspects HTTP requests and forwards them to Services by host or path.

<Frame>
  ![The image illustrates a traditional approach using ingress controllers in a Kubernetes environment, showing traffic flow from an AWS Load Balancer to a service and ingress controller.](https://kodekloud.com/kk-media/image/upload/v1752862870/notes-assets/images/AWS-EKS-VPC-Lattice/kubernetes-ingress-controllers-traffic-flow.jpg)
</Frame>

The Gateway API preserves this topology—external Load Balancer plus in-cluster controller—but introduces three core resources:

| Resource     | Purpose                                                               | Example Use Case                    |
| ------------ | --------------------------------------------------------------------- | ----------------------------------- |
| GatewayClass | Selects the controller implementation (e.g., Lattice, Istio)          | `gateway.networking.k8s.io/v1beta1` |
| Gateway      | Binds external listeners (ports/protocols) to Routes                  | Expose HTTP on port 80              |
| Route Types  | Split by protocol: HTTPRoute, TLSRoute, TCPRoute, UDPRoute, GRPCRoute | Fine-grained traffic matching rules |

<Frame>
  ![The image illustrates routing options with a Gateway API, showing a Kubernetes setup with services, an ingress controller, and an AWS load balancer. It lists different types of ingress routes such as HTTP, TLS, TCP, UDP, and GRPC.](https://kodekloud.com/kk-media/image/upload/v1752862871/notes-assets/images/AWS-EKS-VPC-Lattice/gateway-api-kubernetes-routing-options.jpg)
</Frame>

***

## AWS VPC Lattice Service Networks

AWS VPC Lattice offers a service-mesh–style abstraction for your VPCs without the complexity of peering or Transit Gateways. Central to this model is the **Service Network**, which uses AWS Cloud Map to register endpoints and perform service discovery.

<Frame>
  ![The image is a diagram illustrating service networks in AWS Lattice, showing two VPCs (VPC 01 and VPC 02) connected through AWS Lattice, with references to AWS IAM and AWS Cloud Map.](https://kodekloud.com/kk-media/image/upload/v1752862873/notes-assets/images/AWS-EKS-VPC-Lattice/aws-lattice-service-networks-diagram.jpg)
</Frame>

When Kubernetes workloads join a Lattice Service Network, pod IPs are flattened across clusters just as a CNI flattens IPs inside a cluster.

<Frame>
  ![The image illustrates a diagram of service networks in AWS Lattice, showing two VPCs (VPC 01 and VPC 02) connected through a Lattice service network, with Kubernetes DNS integration.](https://kodekloud.com/kk-media/image/upload/v1752862874/notes-assets/images/AWS-EKS-VPC-Lattice/aws-lattice-service-networks-diagram-2.jpg)
</Frame>

***

## Integrating Kubernetes with VPC Lattice

Here’s how traffic flows when a pod communicates across the Service Network:

1. Pod sends a request to a Service Network DNS name.
2. The Lattice Gateway Controller creates and updates service endpoints in Cloud Map.
3. The request traverses the Service Network to reach the target endpoint (pod, EC2, or Lambda).
4. A gateway at the target side injects traffic into its local CNI or compute runtime.

<Callout icon="lightbulb" color="#1CB2FE">
  AWS Lattice supports hybrid environments—traffic can route to other EKS clusters, EC2 instances, AWS Lambda, or external services registered in Cloud Map.
</Callout>

<Frame>
  ![The image illustrates a Kubernetes setup with a Lattice Gateway Controller, service endpoints, and pods, highlighting its suitability for complex enterprise environments and challenges with scaling. It also mentions AWS Lambda and AWS EC2 integration.](https://kodekloud.com/kk-media/image/upload/v1752862875/notes-assets/images/AWS-EKS-VPC-Lattice/kubernetes-lattice-gateway-aws-integration.jpg)
</Frame>

***

## Considerations and Challenges

While VPC Lattice streamlines cross-VPC communication, there are trade-offs:

| Challenge            | Impact                                                                    |
| -------------------- | ------------------------------------------------------------------------- |
| IAM Dependency       | Every service call relies on IAM policies—complex rules for pods/services |
| Provisioning Latency | Service Network and Cloud Map updates can take 5–10 minutes to complete   |

<Callout icon="triangle-alert" color="#FF6B6B">
  Frequent Gateway API or Service Network changes may incur delays. Plan your deployment workflows to batch updates when possible.
</Callout>

<Frame>
  ![The image outlines challenges associated with AWS Lattice, highlighting heavy reliance on IAM permissions, dependence on AWS services for provisioning, and service network creation time delays. It includes a diagram illustrating traffic management through AWS IAM with advanced permissions.](https://kodekloud.com/kk-media/image/upload/v1752862876/notes-assets/images/AWS-EKS-VPC-Lattice/aws-lattice-challenges-iam-diagram.jpg)
</Frame>

AWS VPC Lattice is ideal for **enterprise-scale** environments requiring strict isolation and multi-account routing. For smaller clusters or simpler cross-cluster needs, consider lighter-weight solutions like native Kubernetes Service or Ingress.

***

## Links and References

* [Kubernetes Gateway API](https://gateway-api.sigs.k8s.io/)
* [AWS VPC Lattice Documentation](https://docs.aws.amazon.com/vpc-lattice/latest/ug/)
* [AWS Cloud Map](https://docs.aws.amazon.com/cloud-map/latest/dg/what-is-cloud-map.html)
* [Kubernetes Ingress vs. Gateway API Comparison](https://kubernetes.io/docs/concepts/services-networking/gateway/)

<CardGroup>
  <Card title="Watch Video" icon="video" cta="Learn more" href="https://learn.kodekloud.com/user/courses/aws-eks/module/3242702b-09b2-43c8-9bbe-283c1d64c685/lesson/d2dc823c-f06a-4421-a9ab-091b8d897c77" />

  <Card title="Practice Lab" icon="installation" cta="Learn more" href="https://learn.kodekloud.com/user/courses/aws-eks/module/3242702b-09b2-43c8-9bbe-283c1d64c685/lesson/b6cf3976-afb2-4a72-b259-4ffdfe026646" />
</CardGroup>


Built with [Mintlify](https://mintlify.com).