# Workshop plan

## Pre-requisites
1. Download and install [Docker CE Edge](https://www.docker.com/community-edition). Make sure to download Edge build. 2. If you have kubectl already installed then delete $HOME/.kube folder.
3. After installing Docker go to Docker settings, switch to 'Kubernetes' tab and check 'Enable Kubernetes' checkbox. Wait till it's been installed.
4. Check Docker installation with ```docker version``` command
```ps
PS C:\development\k8workshop> docker version
Client:
 Version:       18.03.0-ce
 API version:   1.37
 Go version:    go1.9.4
 Git commit:    0520e24
 Built: Wed Mar 21 23:06:28 2018
 OS/Arch:       windows/amd64
 Experimental:  true
 Orchestrator:  kubernetes

Server:
 Engine:
  Version:      18.03.0-ce
  API version:  1.37 (minimum version 1.12)
  Go version:   go1.9.4
  Git commit:   0520e24
  Built:        Wed Mar 21 23:14:32 2018
  OS/Arch:      linux/amd64
  Experimental: true
```
5. Check Kubernetes installation
```ps
PS C:\development\k8workshop> kubectl version
Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.2", GitCommit:"5fa2db2bd46ac79e5e00a4e6ed24191080aa463b", GitTreeState:"clean", BuildDate:"2018-01-18T10:09:24Z", GoVersion:"go1.9.2", Compiler:"gc",
Platform:"windows/amd64"}
Server Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.2", GitCommit:"5fa2db2bd46ac79e5e00a4e6ed24191080aa463b", GitTreeState:"clean", BuildDate:"2018-01-18T09:42:01Z", GoVersion:"go1.9.2", Compiler:"gc",
Platform:"linux/amd64"}
```
6. Download [Helm](https://github.com/kubernetes/helm/releases) and add it to PATH.
7. Initialize Helm with ```helm init```.
8. Check installation
```ps
PS C:\development\k8workshop> helm version
Client: &version.Version{SemVer:"v2.8.2", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.8.2", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean"}
```
9. Fetch dotnet Docker images we'll be using later:
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
