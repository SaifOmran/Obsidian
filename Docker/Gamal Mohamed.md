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

- To download image
```Docker
docker pull [image]
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

#docker update --restart always test
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

#docker rmi httpd:latest
```

- To remove a container we have to stop it first
```Docker
docker stop [container_name]
docker rm [container_name]

#docker stop lab1
#docker rm lab1
```

- We can force remove a container while it is running
```Docker
docker rm -f [container_name]

#docker rm -f lab1
```

- To rename a container
```Docker
docker rename [old_name] [new_name]

#docker rename lab1 lab_1_V1.1
```

- To allocate specific resources for container while creating it
```Docker
docker run -d --name [container_name] --restart always --cpus "no #cpu" --memory [no# memory] [image]

#docker run -d --name lab1 --restart always --cpus "0.5" --memory 100M httpd:latest
#docker run -d --name lab2 --restart always --cpus 1 --memory 150M httpd:latest

```

- If we don't specified resources limit for the container, it can use whatever it wants from them.
- We can specify the resources for the container based on:
	- Type of the application (Python, Nginx, Apache, Node.js, Database).
	- Load testing.
	- Traffic.
	- Monitoring.