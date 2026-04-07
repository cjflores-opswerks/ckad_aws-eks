> ## Documentation Index
> Fetch the complete documentation index at: https://notes.kodekloud.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Tools needed for EKS

> This article outlines essential command-line tools for provisioning and managing Amazon EKS clusters.

Before you dive into provisioning and operating Amazon EKS clusters, equip yourself with these essential command-line tools. While our workshop may not invoke every utility directly, you’ll rely on them for automated deployments, secure authentication, and ongoing cluster maintenance.

| Tool                                 | Purpose                                           | Documentation                                   |
| ------------------------------------ | ------------------------------------------------- | ----------------------------------------------- |
| eksctl                               | Official EKS CLI for cluster lifecycle management | [eksctl on GitHub][eksctl-docs]                 |
| eksdemo                              | Demo workloads and add-on deployment using eksctl | [eksdemo on GitHub][eksdemo-docs]               |
| AWS IAM Authenticator for Kubernetes | `kubectl` plugin for AWS IAM–based authentication | [AWS IAM Authenticator][iam-authenticator-docs] |

## eksctl: The Official EKS CLI

`eksctl` streamlines cluster creation, updates, and deletion by generating CloudFormation stacks under the hood. Use it to manage control plane versions, node groups, and scaling policies—all via simple commands or declarative config files.

### Installation

```bash  theme={null}
# macOS via Homebrew
brew tap weaveworks/tap && brew install weaveworks/tap/eksctl

# Linux via curl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
  | tar xz -C /usr/local/bin
```

<Callout icon="lightbulb" color="#1CB2FE">
  Ensure your `eksctl` version matches your EKS control plane version. Check compatibility in the [eksctl release notes][eksctl-docs].
</Callout>

### Quick Start

```bash  theme={null}
# Create a new EKS cluster with default settings
eksctl create cluster
```

You can refine your cluster by:

* **Command-line flags**\
  Specify region, node type, AMI, or cluster name directly.
* **YAML config file**\
  Define multiple node groups, custom VPC settings, CIDR blocks, tags, and more.

```yaml  theme={null}
# example-cluster-config.yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: my-eks-cluster
  region: us-west-2
nodeGroups:
  - name: ng-small
    instanceType: t3.medium
    desiredCapacity: 2
```

```bash  theme={null}
eksctl create cluster -f example-cluster-config.yaml
```

## eksdemo: Automate Post-Creation Workloads

`eksdemo` builds on `eksctl` to not only manage your EKS cluster but also deploy sample applications, Helm charts, and recommended add-ons with a single command.

* Deploys demos like NGINX, MySQL, or observability stacks
* Installs add-ons: Karpenter, Metrics Server, Cluster Autoscaler
* Applies best-practice labels and annotations

```bash  theme={null}
# List available demos
eksdemo list

# Deploy the Metrics Server demo
eksdemo install metrics-server
```

<Callout icon="lightbulb" color="#1CB2FE">
  Leverage `eksdemo` to validate your cluster setup and speed up learning with preconfigured demos and add-ons.
</Callout>

## AWS IAM Authenticator for Kubernetes

To securely interact with your EKS cluster’s Kubernetes API, install the AWS IAM Authenticator plugin for `kubectl`. It transparently signs API requests using your IAM user or role credentials.

### Installation

```bash  theme={null}
# macOS via Homebrew
brew install aws-iam-authenticator

# Linux via curl
curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/latest/2023-03-11/bin/linux/amd64/aws-iam-authenticator
chmod +x aws-iam-authenticator
mv aws-iam-authenticator /usr/local/bin/
```

### How It Works

1. You run a `kubectl` command against your EKS cluster.
2. The authenticator plugin signs the request with AWS IAM and STS.
3. The EKS API server validates your IAM identity and applies RBAC controls.

```bash  theme={null}
kubectl get nodes
```

<Callout icon="triangle-alert" color="#FF6B6B">
  Without the IAM Authenticator plugin, `kubectl` cannot authenticate to your managed EKS cluster.
</Callout>

***

Now that you have the key tools installed, you’re ready to explore Amazon EKS architecture: Node Groups, auto scaling, VPC networking, and more.

## References

* eksctl on GitHub: [Weaveworks/eksctl][eksctl-docs]
* eksdemo on GitHub: [aws-samples/eksdemo][eksdemo-docs]
* AWS IAM Authenticator: [amazon-eks/aws-iam-authenticator][iam-authenticator-docs]
* Kubernetes Basics: [Kubernetes Documentation][k8s-docs]

[k8s-docs]: https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/

[eksctl-docs]: https://github.com/weaveworks/eksctl

[eksdemo-docs]: https://github.com/aws-samples/eksdemo

[iam-authenticator-docs]: https://github.com/kubernetes-sigs/aws-iam-authenticator

<CardGroup>
  <Card title="Watch Video" icon="video" cta="Learn more" href="https://learn.kodekloud.com/user/courses/aws-eks/module/b84e1a30-d946-4325-96d8-f457dd1817f8/lesson/d95af4b0-d7b1-4998-a9a7-91f0a1ca8030" />
</CardGroup>


Built with [Mintlify](https://mintlify.com).