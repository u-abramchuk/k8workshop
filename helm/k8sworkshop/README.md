Installation:

Install chart with Service Name set to new release name:
```
helm install k8sworkshop
```

To set Service Name to some value:

helm install k8sworkshop --set fullName=my-k8sworkshop # this will se release name
