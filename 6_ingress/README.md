1. Install nginx ingress controller
```ps
PS C:\development\k8workshop\4_ingress> helm install stable/nginx-ingress --name ingress-nginx -f ingress-values.yaml
NAME:   ingress-nginx
LAST DEPLOYED: Thu Mar 29 09:59:04 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
ingress-nginx-nginx-ingress-controller  1     0s

==> v1/Service
NAME                                         TYPE       CLUSTER-IP     EXTERNAL-IP  PORT(S)                     AGE
ingress-nginx-nginx-ingress-controller       NodePort   10.108.9.48    <none>       80:32080/TCP,443:32443/TCP  0s
ingress-nginx-nginx-ingress-default-backend  ClusterIP  10.109.172.42  <none>       80/TCP                      0s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
ingress-nginx-nginx-ingress-controller       1        1        1           0          0s
ingress-nginx-nginx-ingress-default-backend  1        1        1           0          0s

==> v1/Pod(related)
NAME                                                         READY  STATUS             RESTARTS  AGE
ingress-nginx-nginx-ingress-controller-856bf7d4d8-fbphz      0/1    ContainerCreating  0         0s
ingress-nginx-nginx-ingress-default-backend-67b454d79-nj8gz  0/1    ContainerCreating  0         0s


NOTES:
The nginx-ingress controller has been installed.
Get the application URL by running these commands:
  export HTTP_NODE_PORT=32080
  export HTTPS_NODE_PORT=32443
  export NODE_IP=$(kubectl --namespace default get nodes -o jsonpath="{.items[0].status.addresses[1].address}")

  echo "Visit http://$NODE_IP:$HTTP_NODE_PORT to access your application via HTTP."
  echo "Visit http://$NODE_IP:$HTTPS_NODE_PORT to access your application via HTTPS."

An example Ingress that makes use of the controller:

  apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    annotations:
      kubernetes.io/ingress.class: nginx
    name: example
    namespace: foo
  spec:
    rules:
      - host: www.example.com
        http:
          paths:
            - backend:
                serviceName: exampleService
                servicePort: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
        - hosts:
            - www.example.com
          secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls
```
2. Install k8sworkshop chart
```ps
PS C:\development\k8workshop\4_ingress> helm install .\k8sworkshop\ --name k8sws -f values.yaml
NAME:   k8sws
LAST DEPLOYED: Thu Mar 29 10:11:24 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME               TYPE       CLUSTER-IP     EXTERNAL-IP  PORT(S)  AGE
k8sws-k8sworkshop  ClusterIP  10.102.21.115  <none>       80/TCP   0s

==> v1beta2/Deployment
NAME               DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
k8sws-k8sworkshop  3        3        3           0          0s

==> v1beta1/Ingress
NAME               HOSTS      ADDRESS  PORTS  AGE
k8sws-k8sworkshop  localhost  80       0s

==> v1/Pod(related)
NAME                                READY  STATUS             RESTARTS  AGE
k8sws-k8sworkshop-7c9cdb955f-6hnf4  0/1    ContainerCreating  0         0s
k8sws-k8sworkshop-7c9cdb955f-82wqn  0/1    ContainerCreating  0         0s
k8sws-k8sworkshop-7c9cdb955f-855hq  0/1    ContainerCreating  0         0s


NOTES:
1. Get the application URL by running these commands:
  http://localhost/k8s
```
3. Here is a list of all resources in the cluster.
```ps
PS C:\development\k8workshop\4_ingress> kubectl get all
NAME                                                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/ingress-nginx-nginx-ingress-controller        1         1         1            1           12m
deploy/ingress-nginx-nginx-ingress-default-backend   1         1         1            1           12m
deploy/k8sws-k8sworkshop                             3         3         3            0           10s

NAME                                                       DESIRED   CURRENT   READY     AGE
rs/ingress-nginx-nginx-ingress-controller-856bf7d4d8       1         1         1         12m
rs/ingress-nginx-nginx-ingress-default-backend-67b454d79   1         1         1         12m
rs/k8sws-k8sworkshop-7c9cdb955f                            3         3         0         10s

NAME                                                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/ingress-nginx-nginx-ingress-controller        1         1         1            1           12m
deploy/ingress-nginx-nginx-ingress-default-backend   1         1         1            1           12m
deploy/k8sws-k8sworkshop                             3         3         3            0           10s

NAME                                                       DESIRED   CURRENT   READY     AGE
rs/ingress-nginx-nginx-ingress-controller-856bf7d4d8       1         1         1         12m
rs/ingress-nginx-nginx-ingress-default-backend-67b454d79   1         1         1         12m
rs/k8sws-k8sworkshop-7c9cdb955f                            3         3         0         10s

NAME                                                             READY     STATUS    RESTARTS   AGE
po/ingress-nginx-nginx-ingress-controller-856bf7d4d8-fbphz       1/1       Running   0          12m
po/ingress-nginx-nginx-ingress-default-backend-67b454d79-nj8gz   1/1       Running   0          12m
po/k8sws-k8sworkshop-7c9cdb955f-6hnf4                            0/1       Running   0          10s
po/k8sws-k8sworkshop-7c9cdb955f-82wqn                            0/1       Running   0          10s
po/k8sws-k8sworkshop-7c9cdb955f-855hq                            0/1       Running   0          10s

NAME                                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
svc/ingress-nginx-nginx-ingress-controller        NodePort    10.108.9.48     <none>        80:32080/TCP,443:32443/TCP   12m
svc/ingress-nginx-nginx-ingress-default-backend   ClusterIP   10.109.172.42   <none>        80/TCP                       12m
svc/k8sws-k8sworkshop                             ClusterIP   10.102.21.115   <none>        80/TCP                       10s
svc/kubernetes                                    ClusterIP   10.96.0.1       <none>        443/TCP                      17d
```
4. The following is the list of helm charts installed in the cluster.
```ps
PS C:\development\k8workshop\4_ingress> helm list
NAME            REVISION        UPDATED                         STATUS          CHART               NAMESPACE
ingress-nginx   1               Thu Mar 29 09:59:04 2018        DEPLOYED        nginx-ingress-0.3.1 default
k8sws           1               Thu Mar 29 10:11:24 2018        DEPLOYED        k8sworkshop-0.1.0   default
```
5. Inspect `values.yaml` and `ingress-values.yaml`.
