1. Create new helm chart
```ps
helm create k8sworkshop
```
2. Install new chart into cluster with values from `values.yaml`
```ps
PS C:\development\k8workshop\3_helm> helm install .\k8sworkshop\ --name k8sws -f values.yaml
NAME:   k8sws
LAST DEPLOYED: Thu Mar 29 09:33:32 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME               TYPE      CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
k8sws-k8sworkshop  NodePort  10.110.102.6  <none>       80:31067/TCP  0s

==> v1beta2/Deployment
NAME               DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
k8sws-k8sworkshop  1        1        1           0          0s

==> v1/Pod(related)
NAME                                READY  STATUS             RESTARTS  AGE
k8sws-k8sworkshop-7c9cdb955f-f2gvj  0/1    ContainerCreating  0         0s


NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services k8sws-k8sworkshop)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```
3. Open `http://localhost:32667` and make sure application works.
4. Inspect chart and compare with manifests from step 2.
5. Change number of replicas to 3 and update chart.
```ps
PS C:\development\k8workshop\3_helm> helm upgrade k8sws .\k8sworkshop\ -f values.yaml
Release "k8sws" has been upgraded. Happy Helming!
LAST DEPLOYED: Thu Mar 29 09:35:13 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME               TYPE      CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
k8sws-k8sworkshop  NodePort  10.110.102.6  <none>       80:31067/TCP  1m

==> v1beta2/Deployment
NAME               DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
k8sws-k8sworkshop  3        1        1           1          1m

==> v1/Pod(related)
NAME                                READY  STATUS             RESTARTS  AGE
k8sws-k8sworkshop-7c9cdb955f-2gk6j  0/1    ContainerCreating  0         0s
k8sws-k8sworkshop-7c9cdb955f-f2gvj  1/1    Running            0         1m
k8sws-k8sworkshop-7c9cdb955f-tk9fg  0/1    Pending            0         0s


NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services k8sws-k8sworkshop)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```
