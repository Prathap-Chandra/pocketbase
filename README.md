# PocketBase Installation and Setup Guide

This guide will help you install and set up PocketBase using Docker.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Step 1: Create a Dockerfile](#step-1-create-a-dockerfile)
- [Step 2: Build the Docker Image](#step-2-build-the-docker-image)
- [Step 3: Run the PocketBase Container](#step-3-you-can-run-the-pocketbase-container-directly-from-the-image)
- [Step 4: Push the Docker Image to Docker Hub](#step-4-push-the-docker-image-to-docker-hub)
- [Step 5: Pull the Docker Image from Docker Hub](#step-5-pull-the-docker-image-from-docker-hub)
- [Step 6: Use the Docker Image from Docker Hub](#step-6-use-the-docker-image-from-docker-hub)
- [Step 7: Access the PocketBase Admin Interface](#step-7-access-the-pocketbase-admin-interface)
- [Step 8: Stopping and Cleaning Up Resources](#step-8-stopping-and-cleaning-up-resources)

## Prerequisites

Docker installed on your system. You can download it from [Docker's official website](https://www.docker.com/products/docker-desktop).

PocketBase doesn't have an official Docker image yet. So we're going to use the dockerfile mention in the official website.

Source: https://pocketbase.io/docs/going-to-production/#using-docker

## Step 1: Create a Dockerfile

```sh
# Use the latest Alpine image
FROM alpine:latest

ARG PB_VERSION=0.22.20

# Install required packages
RUN apk add --no-cache unzip ca-certificates

# Download and unzip PocketBase
ADD https://github.com/pocketbase/pocketbase/releases/download/v${PB_VERSION}/pocketbase_${PB_VERSION}_linux_amd64.zip /tmp/pb.zip
RUN unzip /tmp/pb.zip -d /pb/

# Expose port 8080 inside the container
EXPOSE 8080

# Start PocketBase
CMD ["/pb/pocketbase", "serve", "--http=0.0.0.0:8080"]
```

## Step 2: Build the Docker Image

```sh
docker build -t pocketbase .
```

## Step 3: You can run the PocketBase container directly from the image

Run the PocketBase container with the following command:

```sh
docker run -d --name pocketbase -p 7001:8080 -v $(pwd)/pb_data:/pb/pb_data pocketbase
```

In my case, I would like to store the data in the below folder.

```sh
docker run -d --name pocketbase -p 7001:8080 -v /Users/prathapchandra/Desktop/Codebase/Docker/volumes/pocketbase:/pb/pb_data pocketbase
```

I would like to push the image to Docker Hub. So let's do that.

## Step 4: Push the Docker Image to Docker Hub

1. Log in to Docker Hub:

```sh
docker login
```

2. Tag your image:

```sh
docker tag pocketbase your_dockerhub_username/pocketbase-docker:latest
```

my username is `codefun`.

```sh
docker tag pocketbase codefun/pocketbase-docker:latest
```

3. Push the image to Docker Hub:

```sh
docker push codefun/pocketbase-docker:latest
```

## Step 5: Pull the Docker Image from Docker Hub

```sh
docker pull codefun/pocketbase-docker:latest
```

## Step 6: Use the Docker Image from Docker Hub

To use the image you've pushed to Docker Hub:

```sh
docker run -d --name pocketbase-docker -p 7001:8080 -v /Users/prathapchandra/Desktop/Codebase/Docker/volumes/pocketbase-docker:/pb/pb_data codefun/pocketbase-docker:latest
```
Now you can easily share and deploy your PocketBase Docker image across different environments.

## Step 7: Access the PocketBase Admin Interface

I chose to run pocketbase at 7001.

You can now access the PocketBase admin interface at `http://your_vps_ip:7001/_/` and the PocketBase API at `http://your_vps_ip:7001/api/`.

## Step 8: Stopping and Cleaning Up Resources

When you're done using PocketBase or want to clean up the resources, follow these steps:

1. Stop the running container:

```sh
docker stop pocketbase-docker
```

2. Remove the container:

```sh
docker rm pocketbase-docker
```

3. Remove the image:

```sh
docker rmi codefun/pocketbase-docker:latest
```

4. Remove the volume:

```sh
rm -rf /Users/prathapchandra/Desktop/Codebase/Docker/volumes/pocketbase-docker
```
