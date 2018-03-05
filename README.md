# Workshop plan

1. dotnet new razor -o k8workshop # create new project
2. dotnet new globaljson # fix dotnet sdk version
2. docker build -t k8workshop .
3. docker run -d -p 8080:80 --name myapp k8workshop