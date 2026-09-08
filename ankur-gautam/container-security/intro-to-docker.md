# Intro to Docker

## What is Docker?

Imagine you build a web application on your laptop.

Everything works perfectly:

* Python is installed
* Required libraries are installed
* Configuration files are correct

Now you send the same application to your friend.

Your friend tries to run it and gets errors.

Why?

Because his machine is different.

Maybe:

* He has a different Python version
* Some libraries are missing
* The operating system is different

This is one of the biggest problems in software development.

Docker solves this problem.

Instead of sending only the application code, Docker packages the application together with everything it needs to run.

As a result, the application behaves the same way everywhere.

A popular way to describe Docker is:

```
Build once,
Run anywhere.
```

***

## Understanding Containers

The most important thing to understand is that Docker runs applications inside **containers**.

A container is an isolated environment that contains:

* Application code
* Libraries
* Dependencies
* Configuration files

Think of a container as a small box that contains everything required by an application.

For example:

```
Container 1 → Nginx Web ServerContainer 2 → MySQL DatabaseContainer 3 → Redis Cache
```

Each container runs independently.

If the database container crashes, the web server container can continue running.

This isolation is one of Docker's biggest advantages.

***

## Images vs Containers

This is a very common interview question.

A simple analogy is:

```
Recipe = ImageCake = Container
```

A recipe contains instructions.

A cake is the final product.

Similarly:

```
Image = BlueprintContainer = Running Instance
```

Docker first needs an image before it can create a container.

Example:

```
docker pull nginx
```

Downloads the Nginx image.

Then:

```
docker run nginx
```

Creates and starts a container from that image.

***

## Downloading Images

Images are usually downloaded from Docker Hub.

Example:

```
docker pull ubuntu
```

Docker downloads the Ubuntu image and stores it locally.

You can view downloaded images using:

```
docker image ls
```

Example output:

```
REPOSITORY   TAG
ubuntu       22.04
nginx        latest
```

***

## Understanding Tags

Images can have multiple versions.

Example:

```
docker pull ubuntu:18.04
docker pull ubuntu:20.04
docker pull ubuntu:22.04
```

Here:

```
ubuntu = image name
22.04 = tag
```

A tag represents a specific version.

Using specific versions is generally recommended because deployments become more predictable.

Instead of:

```
docker pull ubuntu:latest
```

prefer:

```
docker pull ubuntu:22.04
```

***

## Running Containers

The command used to create containers is:

```
docker run
```

Example:

```
docker run nginx
```

Internally Docker performs several actions:

```
Step 1 → Find ImageStep 2 → Create ContainerStep 3 → Start ApplicationStep 4 → Container Running
```

The entire process happens automatically.

***

## Interactive Containers

Sometimes you want to enter a container and use it like a normal Linux machine.

Example:

```
docker run -it ubuntu /bin/bash
```

Explanation:

```
-i = Interactive-t = Terminal
```

You will receive a shell inside the container:

```
root@container:/#
```

At this point, you are working inside the container rather than on the host machine.

***

## Running Containers in the Background

Most containers run in the background.

Example:

```
docker run -d nginx
```

The `-d` flag means:

```
Detached Mode
```

The container continues running while your terminal remains available.

This is how web servers and databases are usually deployed.

***

## Port Mapping

Imagine Nginx is running inside a container.

The web server is listening on port 80 inside that container.

However, your browser cannot access it directly.

To solve this, Docker uses port mapping.

Example:

```
docker run -p 80:80 nginx
```

Meaning:

```
Host Port 80      ↓Container Port 80
```

Now you can open:

```
http://localhost
```

and access the website.

***

## Why Docker Compose Exists

Real applications rarely use a single container.

For example, an e-commerce application may require:

```
Frontend
Backend
Database
Redis
```

Without Docker Compose you would need to start each container manually:

```
docker run frontend
docker run backend
docker run mysql
docker run redis
```

This becomes difficult to manage.

Docker Compose solves this problem.

Instead of managing containers individually, we define the entire application in a single file.

Example:

```
services:

  web:
    image: nginx

  database:
    image: mysql
```

Then start everything using:

```
docker-compose up
```

Docker automatically:

* Creates networks
* Starts containers
* Connects services
* Builds images if required

This makes deployment significantly easier.

***

## What is a Dockerfile?

A Dockerfile contains instructions for building images.

Think of it as a recipe.

Example:

```
FROM ubuntu:22.04

RUN apt update

RUN apt install apache2 -y
```

Docker reads these instructions and creates an image.

To build the image:

```
docker build -t webserver .
```

The result is a new image named:

```
webserver
```

which can later be used to create containers.

***

## Docker Security Note

One of the most important files in Docker is:

```
/var/run/docker.sock
```

This file is used for communication between the Docker Client and the Docker Daemon.

Whoever can control this socket can often control Docker itself.

Because of this, exposing `docker.sock` is considered a serious security risk.

During container security assessments, this is one of the first things security engineers look for.
