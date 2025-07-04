#### Docker architecture

- Docker follows a client-server architecture with these main components:
  
     - Docker Client (docker CLI)
            - The primary interface for users to interact with Docker (e.g., running docker build, docker run).
            - Sends commands to the Docker Daemon via REST API.

     - Docker Daemon (dockerd)
          - The server component that manages Docker objects (containers, images, networks, etc.).
          - Listens for API requests (via /var/run/docker.sock on Linux or TCP sockets).
       
     - Docker Engine
          - The core runtime that includes:
          - The Docker Daemon (dockerd).
          - A REST API for client-daemon communication.
          - The containerd runtime (for container lifecycle management).
          - runc (OCI-compliant low-level runtime that creates/executes containers).

     - Docker Objects
          - Images: Immutable templates for containers (built from Dockerfile).
          - Containers: Runnable instances of images.
          - Volumes: Persistent data storage outside containers.
          - Networks: Isolated communication layers (bridge, host, overlay, etc.).
          - Registries: Stores/distributes images (e.g., Docker Hub, private registries).

     - Additional Components (often overlooked but critical):
          - containerd: Default container runtime (manages container execution, pulled by Docker in 2017).
          - runc: Lightweight CLI tool to spawn containers (implements OCI spec).
          - BuildKit: Modern build engine (faster, more secure alternative to the legacy builder).
          - Docker Desktop: GUI tool for Mac/Windows (includes Docker Engine, Kubernetes, and utilities).
          - Docker Compose: Tool for multi-container apps (defines services in docker-compose.yml).
          - Docker Swarm: Native clustering/orchestration (though less common now with Kubernetes dominance).

#### Detailed Explaination

  - The Client interacts with the Daemon using REST APIs, the Daemon manages containers using namespaces and cgroups.
    - Docker components
       -  **Docker Client (CLI)**		# Sends commands to the Docker Daemon.
       -  **Docker Daemon (dockerd)**	# Manages images, containers, networks, and volumes.
       -  **Docker Images**		        # Read-only templates used to create containers.
       -  **Docker Containers**		# Running instances of images, providing an isolated environment.
       -  **Docker Registry**		# Stores and distributes images (e.g., Docker Hub, private registries).
       -  **Docker Storage**		# Includes Volumes (persistent data storage) and Bind Mounts (direct host folder access).
       -  **Docker Networking**		# Connects containers using different network types (Bridge, Host, Overlay, etc.).
       -  **Docker Compose**		# Manages multi-container applications using a YAML file.
       -  **Docker Orchestration**	        # Tools like Docker Swarm and Kubernetes for container management at scale.
    - Docker compose
       - For complex applications which requires multiple services, we can use docker compose ( docker-compose.yml )
    - Docker Networking
       - Docker provides bridge, host, and overlay networks. 
       - By default, containers use bridge networking, allowing communication with each other via a virtual subnet.
       - Overlay networks are used in Docker Swarm for cross-host networking.
    - Docker Deployment Workflow
       - Writing a Dockerfile
       - Building a image
       - running a docker image
       - pushing the docker image to the registry ( Docker Hub or AWS ECR )
    - Overlay Network docker
       - its a virtual network that runs on top of an existing physical network.
       - It enables communication between containers, VMs, services across multiple hosts without requiring changes to the underlying infrastructure.
       - Creates a logical layer over a physical network.
       - Provides isolated and secure connectivity.
       - Supports multi-host communication for containerized environments.

#### Docker Labels
- Those are like key-value metadata assigned to Docker images, containers, volumes, networks.
- They help organize, document, manage resources.
     - **Metadata tagging** – Add versioning, authorship, and environment details.
     - **Automation** – Labels help tools like Prometheus, Swarm, and Kubernetes manage resources.
     - **Container discovery** – Helps monitoring tools like Datadog and ELK filter logs by labels.
     - **Resource management** – Helps with cost tracking, debugging, and compliance.

#### Docker Client
- The Docker client is a command-line tool (docker CLI) used to interact with the Docker daemon.
     - It sends commands (docker build, docker run, docker pull, etc.) to the Docker daemon.
     - The client communicates with the daemon using REST API, UNIX sockets, or TCP.

#### Docker Daemon (dockerd) & Enginer
- it runs on the host machine and is responsible for managing Docker objects like images, containers, networks, volumes.
     - It listens for requests from the Docker client and processes them.

- Docker Daemon: Handles requests and manages containers.
     - REST API: Allows communication between the client and daemon.
     - CLI (Command Line Interface): Used to interact with Docker via commands.

#### Docker Images
- its a lightweight, standalone, executable software package that includes everything needed to run an application.
     - Images are built using a Dockerfile.
     - Stored in Docker Hub or private registries.
- Commands
   -  **docker images**	Lists available Docker images.**
   -  **docker pull <image>**	Downloads an image from Docker Hub.
   -  **docker push <image>**	Uploads an image to Docker Hub.
   -  **docker rmi <image>**	Removes an image from the local system.
   -  **docker tag <image> <new_image>**	Tags an image with a new name.
   -  **docker build - t <image_name> .**	Builds an image from a Dockerfile.
   -  **docker save - o <image.tar> <image>**	Saves an image as a tar archive.
   -  **docker load - i <image.tar>**	Loads an image from a tar archive.
   -  **docker history <image>**	Shows the history of an image's layers.

#### Docker Containers & registry
- A container is a running instance of a Docker image.
     - It is isolated, lightweight, and portable.
     - Containers share the host OS kernel but run independently.

- A Docker registry stores and distributes images.
     - Examples: Docker Hub (public), AWS ECR, Azure ACR, Google GCR, Harbor (private registries).

#### Docker Volume & Storage
- Used for persistent storage for containers.
     - Volumes allow data to persist even if a container is stopped or deleted.
- Commands
   -  **docker volume ls**	Lists all volumes.
   -  **docker volume create <volume_name>**	Creates a new volume.
   -  **docker volume inspect <volume_name>**	Displays details of a volume.
   -  **docker volume rm <volume_name>**	Deletes a volume.
   -  **docker volume prune**	Removes all unused volumes.

#### Docker Networking
- Allows containers to communicate with each other and external services.
- Types of Docker networks:
     - Bridge (default, for container-to-container communication on the same host)
     - Host (shares the host’s network namespace)
     - Overlay (used in Docker Swarm for multi-host networking)
     - None (no network)
- Commands
   -  **docker network ls**	Lists all Docker networks.
   -  **docker network create <network_name>**	Creates a new Docker network.
   -  **docker network inspect <network_name>**	Displays information about a network.
   -  **docker network connect <network> <container>**	Connects a container to a network.
   -  **docker network disconnect <network> <container>**	Disconnects a container from a network.
   -  **docker network rm <network_name>**	Removes a Docker network.

#### Docker Workflow
- Build → Use a Dockerfile to create an image (docker build -t myapp .).
     - Push → Store the image in a registry (docker push myapp).
     - Pull → Download the image from a registry (docker pull myapp).
     - Run → Create a container from an image (docker run -d myapp).
     - Manage → Monitor, scale, orchestrate containers.

#### Sample dockerfile Node.js 
```
FROM node:18-alpine		# Use the official Node.js image
WORKDIR /app			# Set working directory inside the container
COPY package*.json ./		# Copy package files first (for better caching)
RUN npm install			# Install dependencies
COPY . .			# Copy the rest of the app
EXPOSE 3000			# Expose the port the app runs on
CMD ["npm", "start"]		# Start the app
```

#### Example Dockerfile Java
- Dockerfile in the root of your repo
```
FROM openjdk:17-jdk-alpine			# Use an official Java runtime as the base image
WORKDIR /app					# Set the working directory inside the container
COPY myapp.jar app.jar				# Copy the jar file into the container
ENTRYPOINT ["java", "-jar", "app.jar"]		# Set the command to run the jar
```

#### Dockerfile contents
- in-detail
     - **FROM**		# Specifies the base image to use for the container.
     - **LABEL**	# Adds metadata to the image (e.g., version, maintainer).
     - **RUN**		# Executes commands in the container, typically to install packages or perform system configuration.
     - **COPY**		# Copies files or directories from the host into the container.
     - **ADD**		# Similar to COPY, but with additional features like extracting compressed files and fetching files from URLs.
     - **CMD**		# Specifies the default command to run when the container starts.
     - **ENTRYPOINT**	# Sets the command that is always executed when the container starts (can be overridden by CMD).
     - **ENV**		# Sets environment variables inside the container.
     - **EXPOSE**	# Specifies the ports the container will listen on at runtime.
     - **VOLUME**	# Defines mount points for volumes (persistent storage).
     - **USER**		# Sets the user name or UID to use when running the container.
     - **WORKDIR**	# Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow.
     - **ARG**		# Defines build  - time variables that can be passed to the Docker build process.
     - **STOPSIGNAL**	# Sets the system call signal to stop the container.
     - **SHELL**	# Specifies the shell to use for the RUN commands.

#### Docker Container Lifecycle Management
- process of creating, running, monitoring, updating, and eventually deleting containers in an automated and consistent way. It ensures that containers are well-managed throughout their existence.
   -  **docker run <image>** Creates and starts a new container from an image.
   -  **docker run   - d <image>**	Runs a container in detached mode (background).
   -  **docker run   - it <image> /bin/bash**	Runs a container interactively with a shell.
   -  **docker ps**	Lists running containers.
   -  **docker ps   - a**	Lists all containers (including stopped ones).
   -  **docker start <container_id>**	Starts a stopped container.
   -  **docker stop <container_id>**	Stops a running container.
   -  **docker restart <container_id>** Restarts a container.
   -  **docker pause <container_id>**	Pauses a running container.
   -  **docker unpause <container_id>**	Unpauses a paused container.
   -  **docker rm <container_id>**	Removes a stopped container.
   -  **docker kill <container_id>**	Forcefully stops a container.
   -  **docker attach <container_id>**	Attaches to a running container.
   -  **docker wait <container_id>**	Blocks until a container stops and returns its exit code.
   -  **docker logs <container_id>**	Fetches logs of a container.
   -  **docker inspect <container_id>**	Displays detailed information about a container.
   -  **docker exec - it <container_id>** bash	Runs a command inside a running container (interactive shell).
   -  **docker export <container_id> - o <file.tar>**	Exports a container's filesystem as a tar archive.

#### Docker Compose
- tool used to define and run multi-container Docker applications. Instead of running containers one-by-one manually, Compose lets you define them all in a single YAML file (docker-compose.yml) and manage them as a group.
- Key Features:
     - Define multiple services (containers), networks, volumes in one file.
     - Spin up the entire environment with docker-compose up.
     - Supports environment variables, build instructions, health checks, and volumes.
     - Easy to manage service dependencies and startup order.
- Use case
     - Integration Testing - In CI/CD pipelines (e.g., GitHub Actions, Azure DevOps), we can use Docker Compose to spin up dependent services like databases before tests run.
- (for Multi  - Container Applications)
   -  **docker - compose up**	Starts containers defined in docker  - compose.yml.
   -  **docker - compose up   - d**	Starts containers in detached mode.
   -  **docker - compose down**	Stops and removes containers and networks.
   -  **docker - compose ps**	Lists containers managed by Compose.
   -  **docker - compose logs**	Shows logs for services in Compose.
   -  **docker - compose exec <service> <command>**	Runs a command inside a running service container.

#### Docker System Cleanup
   -  **docker system df**	Shows disk usage by Docker.
   -  **docker system prune**	Removes unused containers, networks, and images.
   -  **docker container prune**	Removes stopped containers.
   -  **docker image prune**	Removes unused images.
   -  **docker network prune**	Removes unused networks.
     
#### Docker Security
   -  **docker scan <image>**	Scans an image for vulnerabilities.
   -  **docker trust inspect <image>**	Shows trust data for an image.

#### Files and Folders in containers base images :
  - /bin: contains binary executable files, such as the ls, cp, and ps commands.
  - /sbin: contains system binary executable files, such as the init and shutdown commands.
  - /etc: contains configuration files for various systemservices.
  - /lib: contains library files that are used by the binary executables.
  - /usr: contains user-related files and utilities, such as applications, libraries, and documentation.
  - /var: contains variable data, such as log files, spool files, and temporary files.
  - /root: is the home directory of the root user.

#### Docker Commands :
  - Basic Management & Information:  
    - docker version: Displays the Docker client and server version information.
    - docker info: Provides detailed system-level information about the Docker environment.
    - docker ps: Lists running containers.
    - docker ps -a: Lists all containers, including stopped ones.
    - docker images: Lists all Docker images on your system.
    - docker search <image_name>: Searches the Docker Hub registry for a specific image.
   
  - Container Operations:
	  - docker run <image_name>: Creates and starts a new container from an image.
	  - docker run -d <image_name>: Runs a container in detached mode (background).
	  - docker start <container_id>: Starts a stopped container.
	  - docker stop <container_id>: Stops a running container.
	  - docker kill <container_id>: Forcefully stops a container.
	  - docker rm <container_id>: Removes a stopped container.
	  - docker exec -it <container_id> <command>: Executes a command within a running container.
	  - docker logs <container_id>: Fetches and displays logs from a container.
	  - docker inspect <container_id>: Displays detailed information about a container.

- Image Management:
	- docker pull <image_name>: Downloads an image from a registry.
	- docker build -t <image_name> . : Builds a Docker image from a Dockerfile.
	- docker tag <image_id> <repository>:<tag>: Tags an image to be pushed to a registry.
	- docker push <repository>:<tag>: Pushes an image to a registry.
	- docker rmi <image_id>: Removes an image.

- VolumeManagement:
	- docker volume create <volume_name>: Creates a Docker volume.
	- docker volume ls: Lists all Docker volumes.
	- docker volume rm <volume_name>: Removes a volume.
	- docker volume inspect <volume_name>: Displays information about a volume.

- Network Management:
	- docker network create <network_name>: Creates a new network.
	- docker network ls: Lists all networks.
	- docker network rm <network_name>: Removes a network.
	- docker network connect <network_name> <container_id>: Connects a container to a network.
	- docker network disconnect <network_name> <container_id>: Disconnects a container from a network.

- Other Important Commands:
	- docker system prune: Removes unused Docker resources.
	- docker system df: Displays disk usage statistics for Docker.
	- docker stats <container_id>: Shows real-time resource consumption for a container.
	- docker cp <source> <destination>: Copies files or directories between the host machine and a container.
	- docker commit <container_id> <image_name>: Creates a new image from the changes in a container.
	- docker login: Logs into a Docker registry.
	- docker logout: Logs out of a Docker registry.
	- docker save -o <output_file.tar> <image_id>: Saves an image to a file.
	- docker load -i <input_file.tar>: Loads an image from a file.


#### Alias for DevOps Engineer

```

alias docker='docker'
alias dps='docker ps'
alias dpsa='docker ps -a'
alias di='docker images'
alias drmi='docker rmi'
alias drmiu='docker rmi $(docker images -f "dangling=true" -q)'
alias drmiuall='docker rmi $(docker images -q)'
alias drmiuallf='docker rmi $(docker images -q) --force'
alias dbuild='docker build -t'
alias drun='docker run -it'
alias druna='docker run -it --rm'
alias drund='docker run -d -it --rm'
alias dexec='docker exec -it'
alias dlogs='docker logs -f'
alias dstop='docker stop'
alias drm='docker rm'
alias drmall='docker rm $(docker ps -a -q)'
alias dprune='docker system prune'
alias dprunea='docker system prune -a'
alias dprunef='docker system prune --force'
alias dprunefall='docker system prune --force --all'
alias dnetwork='docker network'
alias dnetworkls='docker network ls'
alias dnetworkcreate='docker network create'
alias dnetworkrm='docker network rm'
alias dnetworkinspect='docker network inspect'
alias dvolume='docker volume'
alias dvolumels='docker volume ls'
alias dvolumecreate='docker volume create'
alias dvolumerm='docker volume rm'
alias dvolumeinspect='docker volume inspect'
alias dcompose='docker-compose'
alias dcomposeup='docker-compose up'
alias dcomposeupd='docker-compose up -d'
alias dcomposebuild='docker-compose build'
alias dcomposebuildd='docker-compose build --no-cache'
alias dcomposebuildp='docker-compose build --pull'
alias dcomposebuildf='docker-compose build --force-rm'
alias dcomposebuildfn='docker-compose build --force-rm --no-cache'
alias dcomposebuildfno='docker-compose build --force-rm --no-cache'
alias dcomposerun='docker-compose run'
alias dcomposerund='docker-compose run -d'
alias dcomposeps='docker-compose ps'
alias dcomposeupf='docker-compose up --force-recreate'
alias dcomposeupfno='docker-compose up --force-recreate --no-cache'
alias dcomposelogs='docker-compose logs -f'
alias dcomposerm='docker-compose rm'
alias dcomposermforce='docker-compose rm -f'
alias dcomposekill='docker-compose kill'
alias dcomposepsa='docker-compose ps -a'
alias dcstart='docker-compose start'
alias dcstop='docker-compose stop'
alias dcomposedown='docker-compose down'
alias dcomposeps='docker-compose ps'
alias dcomposelogs='docker-compose logs -f'
alias dcomposeexec='docker-compose exec'
alias dcomposeconfig='docker-compose config'
alias dcomposepull='docker-compose pull'
alias dcomposepush='docker-compose push'
alias dcomposeversion='docker-compose version'
alias dcomposeimages='docker-compose images'
alias dcomposekill='docker-compose kill'
alias dcomposerestart='docker-compose restart'
alias dcomposeport='docker-compose port'
alias dcomposelogs='docker-compose logs'
alias dcomposeevents='docker-compose events'
alias dcomposecreate='docker-compose create'
alias dcomposeupf='docker-compose up --force-recreate'
alias dcomposebuildf='docker-compose build --force-rm'


```
