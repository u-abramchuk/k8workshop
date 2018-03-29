1. Deploy service in cluster. It's deployed as pod.
```ps
PS C:\development\k8workshop\2_manifests> kubectl apply -f pod.yaml
pod "k8sworkshop-pod" created
```
2. Now there is one pod in cluster.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME              READY     STATUS    RESTARTS   AGE
k8sworkshop-pod   1/1       Running   0          4s
```
3. Delete pod.
```ps
PS C:\development\k8workshop\2_manifests> kubectl delete po k8sworkshop-pod
pod "k8sworkshop-pod" deleted
```
4. Now there is no pods. Deleted pod won't be re-created.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
No resources found.
```
5. Create deployment
```ps
PS C:\development\k8workshop\2_manifests> kubectl apply -f deployment.yaml
deployment "k8sworkshop-deployment" created
```
6. Now there is one pod in cluster again.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-jdgf9   1/1       Running   0          6m
```
7. And one deployment.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get deployments
NAME                     DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
k8sworkshop-deployment   1         1         1            1           6m
```
8. Delete pod.
```ps
PS C:\development\k8workshop\2_manifests> kubectl delete po k8sworkshop-deployment-bd5f9848c-jdgf9
pod "k8sworkshop-deployment-bd5f9848c-jdgf9" deleted
```
9. Pod is re-created
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-vmbqr   1/1       Running   0          21s
```
10. Change number of application instances to 3. Set `replicas` value to 3 in `deployment.yaml`.
```diff
@@ -3,7 +3,7 @@ kind: Deployment
 metadata:
   name: k8sworkshop-deployment
 spec:
-  replicas: 1
+  replicas: 3
   strategy:
     type: RollingUpdate
   selector:
```
11. Update deployment.
```ps
PS C:\development\k8workshop\2_manifests> kubectl apply -f .\deployment.yaml
deployment "k8sworkshop-deployment" configured
```
12. Make sure that there are 3 pods now.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-h6g2w   1/1       Running   0          45s
k8sworkshop-deployment-bd5f9848c-s8g8f   1/1       Running   0          45s
k8sworkshop-deployment-bd5f9848c-vmbqr   1/1       Running   0          4m
```
13. Delete one pod again.
```ps
PS C:\development\k8workshop\2_manifests> kubectl delete po k8sworkshop-deployment-bd5f9848c-h6g2w
pod "k8sworkshop-deployment-bd5f9848c-h6g2w" deleted
```
14. Number of replicas is still 3.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-s8g8f   1/1       Running   0          3m
k8sworkshop-deployment-bd5f9848c-vmbqr   1/1       Running   0          7m
k8sworkshop-deployment-bd5f9848c-zndsq   1/1       Running   0          57s
```
15. Application is still not accessible outside cluster. Create service to allow application to receive requests from outside.
```ps
PS C:\development\k8workshop\2_manifests> kubectl apply -f service.yaml
service "k8sworkshop-service" created
```
16. List services in cluster.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get svc
NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
k8sworkshop-service   NodePort    10.97.117.196   <none>        80:32238/TCP   10s
kubernetes            ClusterIP   10.96.0.1       <none>        443/TCP        16d
```
17. `k8sworkshop-service` is available on 32238 port. Open `http://localhost:32238` in browser and make sure that application responds.
18. Even if one of the application pods is deleted service will be still able to serve requests.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-s8g8f   1/1       Running   0          10m
k8sworkshop-deployment-bd5f9848c-vmbqr   1/1       Running   0          14m
k8sworkshop-deployment-bd5f9848c-zndsq   1/1       Running   0          7m
PS C:\development\k8workshop\2_manifests> kubectl delete po k8sworkshop-deployment-bd5f9848c-vmbqr
pod "k8sworkshop-deployment-bd5f9848c-vmbqr" deleted
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS              RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-hwt9v   0/1       ContainerCreating   0          1s
k8sworkshop-deployment-bd5f9848c-s8g8f   1/1       Running             0          10m
k8sworkshop-deployment-bd5f9848c-vmbqr   0/1       Terminating         0          14m
k8sworkshop-deployment-bd5f9848c-zndsq   1/1       Running             0          7m
```
20. Here is what we have in cluster now.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get all
NAME                            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/k8sworkshop-deployment   3         3         3            3           22h

NAME                                  DESIRED   CURRENT   READY     AGE
rs/k8sworkshop-deployment-bd5f9848c   3         3         3         22h

NAME                            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/k8sworkshop-deployment   3         3         3            3           22h

NAME                                  DESIRED   CURRENT   READY     AGE
rs/k8sworkshop-deployment-bd5f9848c   3         3         3         22h

NAME                                        READY     STATUS    RESTARTS   AGE
po/k8sworkshop-deployment-bd5f9848c-hwt9v   1/1       Running   0          22h
po/k8sworkshop-deployment-bd5f9848c-s8g8f   1/1       Running   0          22h
po/k8sworkshop-deployment-bd5f9848c-zndsq   1/1       Running   0          22h

NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
svc/k8sworkshop-service   NodePort    10.105.42.221   <none>        80:32164/TCP   9s
svc/kubernetes            ClusterIP   10.96.0.1       <none>        443/TCP        16d
```
19. Delete everything before moving onto next step.
```ps
PS C:\development\k8workshop\2_manifests> kubectl delete -f .\service.yaml
service "k8sworkshop-service" deleted
PS C:\development\k8workshop\2_manifests> kubectl delete -f .\deployment.yaml
deployment "k8sworkshop-deployment" deleted
```
