1. Create new dotnet core project (without actually installing dotnet core!)
```bash
 docker run -it -v /c/development/k8workshop/1_app_docker:/app -w /app microsoft/dotnet:2.0-sdk dotnet new empty -o k8sworkshop
```
2. Copy Dockerfile into project directory
```bash
cp Dockerfile ./k8sworkshop/
```
2. Move to project directory
```bash
cd k8sworkshop
```
3. Make sure to paste the following code snippet into k8sworkshop.csproj:
```xml
<PropertyGroup>
    <PublishWithAspNetCoreTargetManifest>false</PublishWithAspNetCoreTargetManifest>
</PropertyGroup>
```
4. Build docker image
```bash
docker build -t k8sworkshop .
```
5. Run docker container
```bash
docker run -it --rm -p 80:80 --name k8sws k8sworkshop
```
6. Launch app in docker in background
```bash
docker run -d --rm -p 80:80 --name myapp k8sworkshop
```
7. List running docker containers
```bash
docker ps
```
8. Kill docker container
```bash
docker kill myapp
```
9. View all docker images
```bash
docker images
```
