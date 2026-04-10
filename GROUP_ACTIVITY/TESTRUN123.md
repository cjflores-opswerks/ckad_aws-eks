```bash

## FIXING A BROKEN NODE GROUP

    ##LISTING THE NODEGROUPS, THE OUTPUT HERE ARE CATEGORIZED AS EKS MANAGED NODE GROUP, IF IT IS A MANAGED NODE GROUP, IT HAS AUTOSCALER PRESENT, MEANING IF YOU DELETED AN INSTANCE INSIDE IT, IT WILL RESPUN THAT INSTANCE IF DELETED.
    aws eks list-nodegroups --cluster-name <cluster-name>

    ## LOOK FOR THE UNHEALTHY NODE
    kubectl get nodes
    kubectl describe node <node-name>

    ##TO GET THE INSTANCE OF THE NODE
    kubectl get node <node-name> -o jsonpath='{.spec.providerID}'

    ##THE STEPS TO DELETE THE UNHEALTHY NODE AND RESPUN IT AGAIN
    kubectl cordon <node-name>
    kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
    INSTANCE_ID=$(kubectl get node <node-name> -o jsonpath='{.spec.providerID}' | awk -F/ '{print $NF}')
    aws ec2 terminate-instances --instance-ids "$INSTANCE_ID"
    kubectl get nodes -w

    ##TO TAINT A NODE

    kubectl taint nodes <nodeIP> dedicated=special:NoSchedule

    ##CREATE A POD WITH TOLERATIONS
    apiVersion: v1
    kind: Pod
    metadata:
    name: taint-test-pod
    spec:
    tolerations:
    - key: "dedicated"
        operator: "Equal"
        value: "special"
        effect: "NoSchedule"
    containers:
    - name: nginx
        image: nginx


    kubectl apply -f taint-test-pod.yaml

    k


    ##CONFIGMAP AWS AUTH

    kubectl edit configmap aws-auth -n kube-system



    ##ADD THIS SO YOU CAN SEE YOUR NDOES ON YOUR CLUSTER
    mapUsers: |
    - userarn: arn:aws:iam::315527911029:user/user123
      username: user123
      groups:
        - system:masters    

      mapUsers: |
    - userarn: arn:aws:iam::315527911029:user/user123
      username: user123
      groups:
        - system:bootstrappers
        - system:nodes

     