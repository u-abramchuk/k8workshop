# Workshop plan

1. dotnet new razor -o k8sworkshop # create new project
2. dotnet new globaljson # fix dotnet sdk version
3. docker build -t k8sworkshop . # build docker image
4. docker run -it --rm -p 5000:5000 --name myapp k8sworkshop # launch app in docker
5. docker run -d --rm -p 5000:5000 --name myapp k8sworkshop # launch app in docker in background
6. docker tag k8sworkshop targetprocess/k8sworkshop:initial
7. docker push targetprocess/k8sworkshop:initial
8. helm init # initialize helm
9. cd helm
10. helm install k8sworkshop --name k8sws # install helm chart
11. kubectl get svc # view list of services
12. # open http://localhost:31264 in browser
13. # open https://github.com/kubernetes/ingress-nginx/blob/master/deploy/README.md.
14. helm install stable/nginx-ingress --name ingress-nginx -f nginx-ingress/values.yaml
15. kubectl get po # make sure that all pods are up and running
16. # open http://workshop.com:32080/k8s in browser
17. helm del k8sws --purge # delete and then install with secret
18. helm install k8sworkshop --name k8sws # install helm chart
19. kubectl exec -it POD_NAME -- bash
20. cat /secrets/secret.txt
