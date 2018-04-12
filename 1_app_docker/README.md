1. Create new dotnet core project (without actually installing dotnet core!)
```bash
docker run -it -v "$(pwd):/app" -w /app microsoft/dotnet:2.0-sdk dotnet new empty -o k8sworkshop
```
2. Copy Dockerfile into project directory
```Dockerfile
FROM microsoft/aspnetcore-build:2.0 AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj .
RUN dotnet restore

# Copy everything else and build
COPY . .
RUN dotnet publish -c Release -o out -r linux-x64

# Build runtime image
FROM microsoft/dotnet:2.0-runtime
WORKDIR /app
COPY --from=build-env /app/out .
ENV ASPNETCORE_URLS="http://*:80"
EXPOSE 80
ENTRYPOINT ["dotnet", "k8sworkshop.dll"]
```
```bash
cp Dockerfile ./k8sworkshop/
```
2. Move to project directory
```bash
cd k8sworkshop
```
3. Build docker image
```bash
docker build -t k8sworkshop .
```
4. Run docker container
```bash
docker run -it --rm -p 80:80 --name k8sws k8sworkshop
```
5. Launch app in docker in background
```bash
docker run -d --rm -p 80:80 --name myapp k8sworkshop
```
6. List running docker containers
```bash
docker ps
```
7. Stop docker container
```bash
docker stop myapp
```
8. View all docker images
```bash
docker images
```
