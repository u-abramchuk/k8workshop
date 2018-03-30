# Workshop plan

## Pre-requisites
Laptop with MacOS X, Win 10, Linux (then you'll need minikube).

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
docker pull microsoft/dotnet:2.0-sdk
```

## Workshop
1. [Dockerize application](1_app_docker)
2. [Kubernetes manifests](2_manifests)
3. [Secrets and volumes](3_secret_volume)
4. [Convert to helm chart](4_helm)
5. [Generate new helm chart](5_helm_create)
6. [Ingress](6_ingress)
