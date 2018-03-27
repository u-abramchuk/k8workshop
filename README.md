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
```bash
docker pull microsoft/aspnetcore-build:2.0
docker pull microsoft/dotnet:2.0-runtime
```

## Create dotnet core app
1. Create new empty project
```bash
dotnet new empty -o k8sworkshop
```
2. cd to project folder
```bash
cd k8sworkshop
```
2. Fix dotnet SDK version
```bash
dotnet new globaljson
```
3. Make sure to paste the following code snippet into k8sworkshop.csproj:
```xml
<PropertyGroup>
    <PublishWithAspNetCoreTargetManifest>false</PublishWithAspNetCoreTargetManifest>
</PropertyGroup>
```

## Launch app in docker
1. Create Dockerfile and place it in the app directory:
```dockerfile
FROM microsoft/aspnetcore-build:2.0 AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM microsoft/dotnet:2.0-runtime
WORKDIR /app
COPY --from=build-env /app/out .
ENV ASPNETCORE_URLS="http://*:80"
EXPOSE 80
ENTRYPOINT ["dotnet", "k8workshop.dll"]
```
2. Build docker image:
```bash
docker build -t k8sworkshop .
```
3. Launch app in docker
```bash
docker run -it --rm -p 80:80 --name k8sws k8sworkshop
```
4. Launch app in docker in background
```bash
docker run -d --rm -p 80:80 --name myapp k8sworkshop
```
5. (optional) Publish docker image
```bash
docker tag k8sworkshop targetprocess/k8sworkshop:initial
docker push targetprocess/k8sworkshop:initial
```

## Create helm chart
1. Create directory for helm charts and cd into it
```bash
mkdir helm && cd helm
```
2. Create new helm chart
```bash
helm create k8sworkshop
```



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
