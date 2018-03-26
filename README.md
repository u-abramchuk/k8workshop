# Workshop plan

## Pre-requisites
1. Download and install [Docker CE Edge](https://www.docker.com/community-edition). Make sure to download Edge build. 2. If you have kubectl already installed then delete $HOME/.kube folder.
3. After installing Docker go to Docker settings, switch to 'Kubernetes' tab and check 'Enable Kubernetes' checkbox. Wait till it's been installed.
4. Check Docker installation with ```docker version``` command.
5. Check Kubernetes installation with ```kubectl version``` command.
6. Download [Helm](https://github.com/kubernetes/helm/releases) and add it to PATH.
7. Initialize Helm with ```helm init```.
8. Check installation with ```helm version``` command.
9. Download and install [dotnet SDK](https://www.microsoft.com/net/download/windows).
10. Make sure dotnet core is installed with ```dotnet --version``` command.
11. Fetch dotnet Docker images we'll be using later:
```
docker pull microsoft/aspnetcore-build:2.0
docker pull microsoft/dotnet:2.0-runtime
```

1. dotnet new empty -o k8sworkshop && cd k8sworkshop # create new project
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
12. open http://localhost:31264 in browser
13. open https://github.com/kubernetes/ingress-nginx/blob/master/deploy/README.md.
14. helm install stable/nginx-ingress --name ingress-nginx -f nginx-ingress/values.yaml
15. kubectl get po # make sure that all pods are up and running
16. open http://workshop.com:32080/k8s in browser
17. helm del k8sws --purge # delete and then install with secret
18. helm install k8sworkshop --name k8sws # install helm chart
19. kubectl exec -it POD_NAME -- bash
20. cat /secrets/secret.txt
