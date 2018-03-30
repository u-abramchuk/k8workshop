1. To deploy service in cluster create `pod.yaml` and install it into cluster.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: k8sworkshop-pod
spec:
  containers:
  - env:
    - name: ASPNETCORE_ENVIRONMENT
      value: Production
    image: targetprocess/k8sworkshop:initial
    imagePullPolicy: Always
    name: k8sworkshop-pod
    ports:
    - containerPort: 80
      protocol: TCP
```
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
3. Stop docker container corresponding to the pod.
```ps
PS C:\development\k8workshop\2_manifests> docker ps
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                    NAMES
fe9a1adcd2be        targetprocess/k8sworkshop   "dotnet k8sworkshop.…"   24 minutes ago        Up 24 minutes                                  k8s_k8sworkshop-pod_k8sworkshop-pod_default_f6a960f6-3324-11e8-80c4-00155d4b01d5_1
PS C:\development\k8workshop\2_manifests> docker stop fe9a1adcd2be
fe9a1adcd2be
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME              READY     STATUS      RESTARTS   AGE
k8sworkshop-pod   0/1       Completed   0          23m
```
4. Pod will be restarted automatically. Number of restarts increased.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME              READY     STATUS    RESTARTS   AGE
k8sworkshop-pod   1/1       Running   1          23m
```
5. Delete pod.
```ps
PS C:\development\k8workshop\2_manifests> kubectl delete po k8sworkshop-pod
pod "k8sworkshop-pod" deleted
```
6. Now there is no pods. Deleted pod won't be re-created.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
No resources found.
```
7. Create `deployment.yaml` and install it into the cluster.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: k8sworkshop-deployment
spec:
  replicas: 1
  strategy:
    type: RollingUpdate
  selector:
    matchLabels:
      component: k8sworkshop-deployment
  template:
    metadata:
      labels:
        component: k8sworkshop-deployment
    spec:
      containers:
      - name: k8sworkshop-deployment
        image: targetprocess/k8sworkshop:initial
        imagePullPolicy: Always
        env:
        - name: ASPNETCORE_ENVIRONMENT
          value: Production
        ports:
        - containerPort: 80
```
```ps
PS C:\development\k8workshop\2_manifests> kubectl apply -f deployment.yaml
deployment "k8sworkshop-deployment" created
```
8. Now there is one pod in cluster again.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-jdgf9   1/1       Running   0          6m
```
9. And one deployment.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get deployments
NAME                     DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
k8sworkshop-deployment   1         1         1            1           6m
```
10. Delete pod.
```ps
PS C:\development\k8workshop\2_manifests> kubectl delete po k8sworkshop-deployment-bd5f9848c-jdgf9
pod "k8sworkshop-deployment-bd5f9848c-jdgf9" deleted
```
11. Pod is re-created
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-vmbqr   1/1       Running   0          21s
```
12. Change number of application instances to 3. Set `replicas` value to 3 in `deployment.yaml`.
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
13. Update deployment.
```ps
PS C:\development\k8workshop\2_manifests> kubectl apply -f .\deployment.yaml
deployment "k8sworkshop-deployment" configured
```
14. Make sure that there are 3 pods now.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-h6g2w   1/1       Running   0          45s
k8sworkshop-deployment-bd5f9848c-s8g8f   1/1       Running   0          45s
k8sworkshop-deployment-bd5f9848c-vmbqr   1/1       Running   0          4m
```
15. Delete one pod again.
```ps
PS C:\development\k8workshop\2_manifests> kubectl delete po k8sworkshop-deployment-bd5f9848c-h6g2w
pod "k8sworkshop-deployment-bd5f9848c-h6g2w" deleted
```
16. Number of replicas is still 3.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get po
NAME                                     READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-bd5f9848c-s8g8f   1/1       Running   0          3m
k8sworkshop-deployment-bd5f9848c-vmbqr   1/1       Running   0          7m
k8sworkshop-deployment-bd5f9848c-zndsq   1/1       Running   0          57s
```
17. Application is still only accessible inside cluster. Create service (`service.yaml`) to allow application to receive requests from outside.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: k8sworkshop-service
spec:
  ports:
    - port: 80
      name: web
      targetPort: 80
  selector:
    component: k8sworkshop-deployment
  type: NodePort
```
```ps
PS C:\development\k8workshop\2_manifests> kubectl apply -f service.yaml
service "k8sworkshop-service" created
```
18. List services in cluster.
```ps
PS C:\development\k8workshop\2_manifests> kubectl get svc
NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
k8sworkshop-service   NodePort    10.97.117.196   <none>        80:32238/TCP   10s
kubernetes            ClusterIP   10.96.0.1       <none>        443/TCP        16d
```
19. `k8sworkshop-service` is available on 32238 port. Open `http://localhost:32238` in browser and make sure that application responds.
20. Even if one of the application pods is deleted service will be still able to serve requests.
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
21. Here is what we have in cluster now.
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
