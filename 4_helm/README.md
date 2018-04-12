1. Create `k8sworkshop` folder for a new helm chart.
```bash
mkdir k8sworkshop && cd k8sworkshop
```
2. Add `Chart.yaml`.
```yaml
apiVersion: v1
appVersion: "1.0"
description: A Helm chart for Kubernetes
name: k8sworkshop
version: 0.1.0
```
3. Add empty file `values.yaml`.
4. Create `templates` folder and put `deployment.yaml` and 'service.yaml` in it.
```bash
mkdir templates && cd templates
```
5. To make replicas count configurable via `values.yaml` replace `replicas` value in `deployment.yaml` with placeholder `{{ .Values.replicas }}`.
```diff
@@ -3,7 +3,7 @@ kind: Deployment
metadata:
  name: k8sworkshop-deployment
spec:
-  replicas: 1
+  replicas: {{ .Values.replicas }}
  strategy:
    type: RollingUpdate
  selector:
```
Specify `replicas` value in `k8sworkshop/values.yaml`.
```yaml
replicas: 3
```
6. Install helm chart.
```ps
PS C:\development\k8workshop\4_helm> helm install .\k8sworkshop\ --name k8sws
NAME:   k8sws
LAST DEPLOYED: Fri Mar 30 10:11:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                    READY  STATUS   RESTARTS  AGE
k8sworkshop-deployment-bd5f9848c-m9phl  0/1    Pending  0         0s
k8sworkshop-deployment-bd5f9848c-pgs6s  0/1    Pending  0         0s
k8sworkshop-deployment-bd5f9848c-qk4tg  0/1    Pending  0         0s

==> v1/Service
NAME                 TYPE      CLUSTER-IP     EXTERNAL-IP  PORT(S)       AGE
k8sworkshop-service  NodePort  10.102.134.74  <none>       80:32371/TCP  0s

==> v1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
k8sworkshop-deployment  3        0        0           0          0s
```
7. Create new `values.yaml` outside `k8sworkshop` folder.
```yaml
replicas: 5
```
8. Update chart with new values.
```ps
PS C:\development\k8workshop\4_helm> helm upgrade k8sws .\k8sworkshop\ -f custom-values.yaml
Release "k8sws" has been upgraded. Happy Helming!
LAST DEPLOYED: Fri Mar 30 10:15:54 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME                 TYPE      CLUSTER-IP     EXTERNAL-IP  PORT(S)       AGE
k8sworkshop-service  NodePort  10.102.134.74  <none>       80:32371/TCP  4m

==> v1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
k8sworkshop-deployment  5        3        3           3          4m

==> v1/Pod(related)
NAME                                    READY  STATUS             RESTARTS  AGE
k8sworkshop-deployment-bd5f9848c-dwzmd  0/1    Pending            0         0s
k8sworkshop-deployment-bd5f9848c-m9phl  1/1    Running            0         4m
k8sworkshop-deployment-bd5f9848c-pgs6s  1/1    Running            0         4m
k8sworkshop-deployment-bd5f9848c-qk4tg  1/1    Running            0         4m
k8sworkshop-deployment-bd5f9848c-rdpp2  0/1    ContainerCreating  0         0s
```
9. Cleanup.
```ps
PS C:\development\k8workshop\4_helm> helm del k8sws --purge
release "k8sws" deleted
```
