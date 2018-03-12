# Workshop plan

1. dotnet new razor -o k8sworkshop # create new project
2. dotnet new globaljson # fix dotnet sdk version
3. docker build -t k8sworkshop . # build docker image
4. docker run -it --rm -p 5000:5000 --name myapp k8sworkshop # launch app in docker
5. docker run -d --rm -p 5000:5000 --name myapp k8sworkshop # launch app in docker in background
6. docker tag k8sworkshop targetprocess/k8sworkshop:initial
7. docker push targetprocess/k8sworkshop:initial
