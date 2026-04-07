
### Static Provisioning of Persistent Volume

Create a pod and PersistentVolume (PV) with static provisioning.

Step 1: Create an EBS volume using AWS CLI:

```
aws ec2 create-volume --size 10 --region us-east-1 --availability-zone us-east-1a --volume-type gp2
```

Step 2: Get the volume ID from the output and replace in the following YAML

```
cat <<EOF | kubectl apply -f -
# Create a PersistentVolume (PV)
apiVersion: v1
kind: PersistentVolume
metadata:
  name: ebs-pv
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  csi:
    driver: ebs.csi.aws.com
    fsType: ext4
    volumeHandle: <vol-id> 
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: topology.kubernetes.io/zone
              operator: In
              values:
                - us-east-1a
---
# Create a PersistentVolumeClaim (PVC)
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc
spec:
  storageClassName: ""
  volumeName: ebs-pv
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
---
# Create a pod using the PVC
apiVersion: v1
kind: Pod
metadata:
  name: ebs-pod
spec:
  nodeSelector: 
    topology.kubernetes.io/zone: us-east-1a
  containers:
  - name: app
    image: busybox
    command: [ "sh", "-c", "echo Hello Kubernetes! && sleep 3600" ]
    volumeMounts:
    - mountPath: "/data"
      name: ebs-storage
  volumes:
  - name: ebs-storage
    persistentVolumeClaim:
      claimName: ebs-pvc
EOF
```