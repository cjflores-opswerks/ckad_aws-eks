
> To count the number of clusterroles

 k get clusterroles -A | wc -l 


> To count the number of clusterbindings

k get clusterrolebindings -A | wc -l

> A new user `michelle` joined the team. She will be focusing on the `nodes` in the cluster. Create the required `ClusterRoles` and `ClusterRoleBindings` so she gets access to the `nodes`.

kubectl create clusterrole michelle-node-reader \
  --verb=get,list,watch \
  --resource=nodes

kubectl create clusterrolebinding michelle-node-reader-binding \
  --clusterrole=michelle-node-reader \
  --user=michelle

To verify:

kubectl auth can-i get nodes --as michelle


> To list the shortcuts or aliases onthe kubernetes cluster
> 

kubectl api-resources


>`michelle`'s responsibilities are growing and now she will be responsible for storage as well. Create the required `ClusterRoles` and `ClusterRoleBindings` to allow her access to Storage.

