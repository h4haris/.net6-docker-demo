# .NET 6 Docker Demo
Building and Deploying .NET Core applications using Docker Containers

## Introduction
Docker is a popular platform for building and deploying applications using containers. Containers allow you to package the application and its dependencies into a single, portable unit, making it easy to deploy and run the application on any machine with any operating system that has Docker installed on it.

## Pre-requisites

Make sure that you have the following prerequisites installed on the machine.

1. Docker
2. .NET Core SDK

## Setup

### Step 1: Create a new .NET Core application

1. Create a new ASP.NET Core web application in a folder called `NetCoreWithDockerDemo` using below command.

```bash
dotnet new webapp -o NetCoreWithDockerDemo
```

2. Build & run application

```bash
dotnet run
```

3. Open the localhost address and open it in the browser.

### Step 2: Create a Dockerfile

In order to build the docker image for any application, we need to create a corresponding `DockerFile`. 

A Dockerfile is a text file that contains instructions for building a Docker image.

To create a Dockerfile for a .NET Core application, create a new file with the name Dockerfile in the root of the project and add the following content:

```bash
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build-env
WORKDIR /App

# Copy everything
COPY . ./
# Restore as distinct layers
RUN dotnet restore
# Build and publish a release
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:6.0
WORKDIR /App
COPY --from=build-env /App/out .
ENTRYPOINT ["dotnet", "NetCoreWithDockerDemo.dll"]
```

The Dockerfile uses the dotnet/sdk and dotnet/aspnet base images to build and run the .NET Core application.

It first restores the NuGet packages and builds the application in the build stage, and then copies the built application to the dotnet/aspnet base image in the final stage.

### Step 3: Build the Docker image

Now that we have a Dockerfile created.

1. Let’s try to build a docker image which we can then deploy on docker, using following command at the root of the project

```bash
docker build -t net_core_with_docker_demo -f DockerFile .
```

Running above command will download all the dependent docker images and will also build a Docker image for our Web Application and assign it the tag `net_core_with_docker_demo`.

2. Confirm image created & tagged properly, run following command

```bash
docker images
```

### Step 4: Run the Docker container

Now that we have successfully built the docker image: 

1. Let’s run this image in a docker container, using following command at the root of the project:

```bash
docker run -d -p 8080:80 --name net_core_with_docker_demo net_core_with_docker_demo
```

Above command will run the application on docker as a container and will assign the container id to it. Also, we have bound the machine port 8080 to the container port 80 so that we can access the application running inside the container using the address "http://localhost:8080".

2. If you have installed the docker desktop, you can also view the container running inside it and also view the logs for the container.

3. Now open the browser and navigate to the address "http://localhost:8080" and you should be able to see the application running.
