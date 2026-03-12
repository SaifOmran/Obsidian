- To see the downloaded images.
```Docker
docker images
```

- To see the running containers
```Docker
docker ps
```

- To see all containers (running and stopped)
```Docker
docker ps -a
```

- To see the all containers ID without any other details
```Docker
docker ps -aq
```

- To download image
```Docker
docker pull [image]

# docker pull alpine:2.2.6
# By default the TAG is "latest".
```

- The images are on a *registry*, the registry could be public or private.
- The most common public registry is *Docker Hub*.
- image tag = image version

- To create a container, but it is not running
```Docker
docker create --name [container_name] [image]

# docker create --name lab1 httpd
```
after creating it check by using `docker ps` to see the running container, you will NOT find it.

- To create a container and make it running
```Docker
docker run -d --name [container_name] [image]

# docker run -d --name lab1 httpd
# -d = deattach to run the container in background of the terminal
```

- To start a created container
```Docker
docker start [container_name]
# docker start lab1
# you can start multiple containers in one command
```

- To stop running container
```Docker
docker stop [container_name]
# docker stop lab1
# you can stop multiple containers in one command
```

- The `docker update` command is used to dynamically change resource constraints and configurations on one or more running containers.
- When the system restarts or when the docker service restarts, all containers are stopped.
- `--restart` controls how the container behaves after system restart or service restart.
- To make the containers auto start after the restart
```Docker
docker update --restart always [container_name]

# docker update --restart always test
```

### Docker Architecture
- **Docker uses a client–server architecture.**  
- The **Docker client** sends commands to the **Docker daemon**, which builds, runs, and manages containers. They communicate through a **REST API** using UNIX sockets or network interfaces. The client and daemon can run on the same machine or on different machines.
---
### Main Components
**1. Docker Daemon (dockerd)**  
- The daemon listens for Docker API requests and manages Docker objects such as:
	- Images
	- Containers
	- Networks
	- Volumes
---
**2. Docker Client**  
- The client (`docker` command) is the main tool users interact with.  
- Commands like:
	- docker run  
	- docker pull  
	- docker build
are sent by the client to the Docker daemon for execution.
---
**3. Docker Desktop**  
Docker Desktop is an application for **Windows, Mac, and Linux** that includes:
- Docker daemon.
- Docker client.
- Docker Compose.
- Kubernetes.
- Other supporting tools.
- It simplifies building and managing containerized applications.
---
**4. Docker Registries**  
A **registry** stores Docker images.
Examples:
- **Docker Hub** (default public registry).
- Private registries (can be online or local).
Commands:
- `docker pull` → download an image.
- `docker push` → upload an image (you must have an access to push an image to registry).
---
### Docker Objects
**Images**
- Read-only templates used to create containers.
- Built using a **Dockerfile**.
- Made of layers, which makes them lightweight and efficient.

**Containers**
- Running instances of images.
- Can be started, stopped, moved, or deleted.
- Isolated from other containers and the host system.
- Data is lost when a container is removed unless stored in persistent storage.
---
- To list all images on local registry
```Docker
docker images
```

- To search about an image in the configured registries
```Docker
docker search [image_name]
```

- To delete image we have to make sure that there is no container is created from it
```Docker
docker rmi [image_name]

# docker rmi httpd:latest
```

- To remove a container we have to stop it first
```Docker
docker stop [container_name]
docker rm [container_name]

# docker stop lab1
# docker rm lab1
```

- We can force remove a container while it is running
```Docker
docker rm -f [container_name]

# docker rm -f lab1
```

- To rename a container
```Docker
docker rename [old_name] [new_name]

# docker rename lab1 lab_1_V1.1
```

- To allocate specific resources for container while creating it
```Docker
docker run -d --name [container_name] --restart always --cpus "no #cpu" --memory [no# memory] [image]

# docker run -d --name lab1 --restart always --cpus "0.5" --memory 100M httpd:latest
# docker run -d --name lab2 --restart always --cpus 1 --memory 150M httpd:latest

```

- If we don't specified resources limit for the container, it can use whatever it wants from them.
- We can specify the resources for the container based on:
	- Type of the application (Python, Nginx, Apache, Node.js, Database).
	- Load testing.
	- Traffic.
	- Monitoring.

- To retrieve detailed, low-level information about various Docker objects such as containers, images, networks, volumes, and services
```Docker
docker inspect [object_name]

# docker inspect httpd:latest
# docker inspect lab1
```

- To run command in a running container (ad-hoc command)
```Docker
docker exec [container_name] [command]

# docker exec lab1 ls
```

- To open terminal in a running container (common used)
```Docker
docker exec -ti [container_name] /bin/bash

# docker exec -ti lab1 /bin/bash
```
---
### Docker File
- **AppImage** is a single-file, portable format for distributing desktop applications that includes all dependencies, requiring no installation.
- **Base Image** is a minimal, underlying OS layer (like Ubuntu, Alpine, or Debian) used as a starting point to build Docker containers or virtual machine images.
- We need to build docker image if there is no image for our application, or if we need to apply some changes to existing image.
- When we start to build am image, firstly we choose the base image, and then we type the instructions.
- When we start to build the image the docker engine looks at the base image and it creates a temporary container with this base image and apply the changes, and finally convert this image to read-only image and delete the temporary container.
- We can build Docker Image by 2 methods:
	1. Docker file from scratch.
	2. Convert running container to image.

- To convert running container to an image
```Docker
docker commit -m "message" [container_name] [image_name:TAG]

# docker commit -m "vim installed" lab1 httpd-vim:v1.0.1
```

The "parameters" of a Dockerfile are referred to as instructions or directives, which are commands executed by the Docker engine to build an image.

The most common and essential Dockerfile instructions include:
- **`FROM`**: Specifies the base image for the new image. It must be the first instruction in a Dockerfile.

- **`RUN`**: Executes commands in a new layer on top of the current image during the build process, for tasks like installing packages or creating directories.

- **`CMD`**: Provides default arguments for an executing container. If multiple `CMD` instructions are present, only the last one is used. It can be overridden when running the container.

- **`ENTRYPOINT`**: Configures a container to run as an executable. It is often used with `CMD` to set default parameters for the `ENTRYPOINT` command, and is less easily overridden than `CMD`.

- **`COPY`**: Copies files or directories from the host filesystem to a specified path in the image.

- **`ADD`**: Similar to `COPY`, but it can also handle remote URLs and automatically extract compressed archives.

- **`ENV`**: Sets environment variables within the image that will be available to the container at runtime. These can be overridden using the `-e` flag with `docker run`.

- **`EXPOSE`**: Documents which ports the container will listen on at runtime. It is only for documentation and does not actually publish the ports.

- **`WORKDIR`**: Sets the working directory for any subsequent `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, or `ADD` instructions.

- **`VOLUME`**: Declares a mount point for persistent or shared data, creating a volume to access a directory on the host machine.

- After creating the docker file, we need to build the image using it
```Docker
docker build -t [image_name:TAG] .

# docker build -t myapp:v1.1 .

# . refers to the Dockerfile in the current location, we can replace it by the path of the docker file
```

- To push an image we make 3 steps:
	1. Create repo for the image of DockerHub.
	2. Create tag for the local image and mount it to the repo.
	3. Push the image.
---
### Docker Network
- Container networking refers to the ability for containers to connect to and communicate with each other, and with non-Docker network services.
- Containers have ==networking enabled by default==, and they can make outgoing connections.
- When Docker Engine on Linux starts for the first time, it has a single built-in network called the =="default bridge"== network. When you run a container without the `--network` option, it is connected to the default bridge.
- Containers attached to the ==default bridge== have access to network services outside the Docker host. They use "masquerading" which means, if the Docker host has Internet access, no additional configuration is needed for the container to have Internet access.
- The default bridge can be internal bridge or external bridge.
- The  ==internal bridge== allows the containers to only communicate with each other and they can NOT access the internet.
- While ==external bridge== allows the containers to communicate with each other and also can access the internet..

- To list the networks
```Docker
docker network ls
```

- To create new network
```Docker
docker network create [net-name] --driver [bridge|host|none] --subnet [net ID] --gateway [IP]
```

- To create bridge internal network
```Docker
docker network create back_end_net --driver bridge --internal --subnet [net ID] --gateway [IP]
```

>With out `--internal` option the network will be bridge external

- To show the metadata of the network
```Docker
docker network inspect [network_name]

# docker network inspect back_end_net
```

- To connect a container to network
```Docker
docker network connect [net_name] [container_name]

# docker network connect back_end_net front

# Here we connected a container named "front" with a network named "back_end_net", and this network is configured as bridge internal..so the front container can communicate with the containers in this network and these containers still can NOT access the internet while the "front" container can as it is in the external bridge network.
```

>When the container is connected to multiple networks, it is like that the container has multiple vNICs and each one has IP from the network that it is connected to.
---
### Docker Volume
- Docker volume is a component responsible about storing the data of the container, and allows the container to access this data when needed.

- Why we have to store the data outside the container ?
	1. The container is a temporary process that can be destroyed or removed.
	2. Data sync between containers.
	3. Backup of data.
	4. Process isolation, so if any container wants data from another container, it accesses the docker volume instead of the container itself.
	5. Reduce image size as the data is not stored on the container.
	6. Cost saving as the data of multiple containers from one image stored in one place instead of redundancy. 

- Docker volume can be local disk, SAN storage, cloud storage.

- To create new volume
```Docker
docker volume create [volume_name]

# docker volume create appdate 
```

- To list all volumes
```Docker
docker volume ls
```

- To show info about the volume
```Docker
docker volume inspect [volume_name]

# docker volume inspect appdate

# Here you will find the mount point of the volume which you will make it the mount point of the logical volume or the partition.
```

- To create a container and connect it to the created volume
```Docker
docker run -d --name [container_name] -p 90:80 --volume [volume_name]:App_data_path

# docker run -d --name apache_web -p 90:80 --volume appdata:/usr/local/apache2

# the app data path is "/usr/local/apache2" as it is apache application.
```













