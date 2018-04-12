1. Create new `secret.yaml` and install it into the cluster.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: k8sworkshop-secret
type: Opaque
data:
  secret.txt: VGhlcmUgaXMgbm90aGluZyBpbnRlcmVzdGluZyBpbiBoZXJlLg==
```
```ps
PS C:\development\k8workshop\3_secret_volume> kubectl apply -f .\secret.yaml
secret "k8sworkshop-secret" created
```
2. List all secrets in cluster.
```ps
PS C:\development\k8workshop\3_secret_volume> kubectl get secrets
NAME                  TYPE                                  DATA      AGE
default-token-lnwv4   kubernetes.io/service-account-token   3         18d
k8sworkshop-secret    Opaque                                1         10s
```
3. Update `deployment.yaml` to mount the secret as volume.
```diff
@@ -23,3 +23,12 @@ spec:
          value: Production
        ports:
        - containerPort: 80
+        volumeMounts:
+        - mountPath: /secrets
+          name: k8sworkshop-secret
+          readOnly: true
+      volumes:
+      - name: k8sworkshop-secret
+        secret:
+          defaultMode: 420
+          secretName: k8sworkshop-secret
```
4. Update the deployment.
```ps
PS C:\development\k8workshop\3_secret_volume> kubectl apply -f .\deployment.yaml
deployment "k8sworkshop-deployment" configured
```
5. Get list of running pods.
```ps
PS C:\development\k8workshop\3_secret_volume> kubectl get po
NAME                                      READY     STATUS    RESTARTS   AGE
k8sworkshop-deployment-689dcccd88-v95ps   1/1       Running   0          14s
```
6. Enter the pod.
```ps
PS C:\development\k8workshop\3_secret_volume> kubectl exec -it k8sworkshop-deployment-689dcccd88-v95ps -- bash
```
7. List contents of `/secrets` folder.
```bash
root@k8sworkshop-deployment-689dcccd88-v95ps:/app# ls /secrets/
secret.txt
```
8. See what's inside `secret.txt`.
```bash
root@k8sworkshop-deployment-689dcccd88-v95ps:/app# cat /secrets/secret.txt
There is nothing interesting in here.
```
9. Exit.
```bash
root@k8sworkshop-deployment-689dcccd88-v95ps:/app# exit
exit
```
10. Cleanup.
```ps
PS C:\development\k8workshop\3_secret_volume> kubectl delete -f service.yaml -f secret.yaml -f deployment.yaml
service "k8sworkshop-service" deleted
secret "k8sworkshop-secret" deleted
deployment "k8sworkshop-deployment" deleted
```
