1. Create new dotnet core project (without actually installing dotnet core)
```bash
 docker run -it -v /c/development/k8workshop/1_app_docker:/app -w /app microsoft/dotnet:2.0-sdk dotnet new empty -o k8sworkshop
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
4. Create Dockerfile in application folder (or copy it from repo)
```
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
ENTRYPOINT ["dotnet", "k8sworkshop.dll"]

```
5. Build docker image
```bash
docker build -t k8sworkshop .
```
6. Run docker container
```bash
docker run -it --rm -p 80:80 --name k8sws k8sworkshop
```
7. Launch app in docker in background
```bash
docker run -d --rm -p 80:80 --name myapp k8sworkshop
```
8. List running docker containers
```bash
docker ps
```
9. Kill docker container
```bash
docker kill myapp
```
10. View all docker images
```bash
docker images
```
