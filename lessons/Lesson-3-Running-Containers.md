# Lesson 3: Running Containers

- [Lesson 3: Running Containers](#lesson-3-running-containers)
    - [3.1 Exploring Containers](#31-exploring-containers)
      - [Understanding Containers](#understanding-containers)
      - [Understanding the Success of Containers](#understanding-the-success-of-containers)
      - [Understanding Differences with Virtual Machines](#understanding-differences-with-virtual-machines)
    - [3.2 Understanding Namespaces and Cgroups](#32-understanding-namespaces-and-cgroups)
      - [Understanding chroot](#understanding-chroot)
      - [Containers and the Linux Kernel](#containers-and-the-linux-kernel)
      - [Current Linux Namespaces](#current-linux-namespaces)
    - [3.3 Container Runtimes](#33-container-runtimes)
      - [Understanding Containers Engines](#understanding-containers-engines)
      - [Understanding Container Runtimes](#understanding-container-runtimes)
      - [Understanding Runc](#understanding-runc)
    - [3.4 Using Container Images and Registries](#34-using-container-images-and-registries)
      - [Understanding Container Images](#understanding-container-images)
      - [Understanding Container Registries](#understanding-container-registries)
    - [3.5 Running Docker Containers](#35-running-docker-containers)
      - [Demo: Installing Docker](#demo-installing-docker)
      - [Demo: Running Docker Container](#demo-running-docker-container)
    - [3.6 Building Container Images](#36-building-container-images)
      - [Creating Container Images](#creating-container-images)
      - [Understanding Containerfile](#understanding-containerfile)
      - [Dockerfile Best Practices](#dockerfile-best-practices)
      - [Demo: Using a Dockerfile](#demo-using-a-dockerfile)
      - [Demo: Using the same with Alpine](#demo-using-the-same-with-alpine)
      - [ENTRYPOINT versus CMD](#entrypoint-versus-cmd)
      - [Understanding Tags](#understanding-tags)
      - [Tagging Images](#tagging-images)
    - [3.7 Implementing Container Security](#37-implementing-container-security)
      - [Understanding Security Risks](#understanding-security-risks)
      - [Cloud Native Security](#cloud-native-security)
      - [Using Container Security Best Practices](#using-container-security-best-practices)
    - [3.8 Practice Questions](#38-practice-questions)
    - [Lesson 3 Lab: Building Container Images](#lesson-3-lab-building-container-images)
    - [Lesson 3 Lab Solution: Building Container Images](#lesson-3-lab-solution-building-container-images)

### 3.1 Exploring Containers

#### Understanding Containers

- The container image is the foundation of a container.
- A container image contains everything needed to run an application.
- Images don't require any installation, you just need to run them.
- Images are obtained from image registries such as hub.docker.com and quay.io
- To run container, a container runtime is required.
- Container runtimes are available for all platforms, including cloud.

#### Understanding the Success of Containers

- Images provide standard distribution models for developers and they can be used on computing platforms as well as cloud.
- Containers are operating system idependent.
- Containers run natively on cloud.
- While integrated in an orchestration platform, containers offer a platform that allows for zero-downtime application updates.

#### Understanding Differences with Virtual Machines

- In a virtual machine the hardware is virtualized.
- On top of this virtual machine you will still run a complete operating system.
- A container requires a host operating system, offering a kernel.
- The container runs on top of the container runtime.
- Because containers do not include a kernel, they ar more efficient than virtual machines.

### 3.2 Understanding Namespaces and Cgroups

#### Understanding chroot

- The **chroot** jail is the foundation of any container.
- It is used to isolate processes from the root directory, and present only the contents of a specific directory to the process.
- **chroot** was introduced in the late 1970's, but is still used as a basic security measure by many applications.
- **chroot** has further evolved into Linux kernel Namespaces.

#### Containers and the Linux Kernel

- Namespaces and cgroups are important features for running containers, which are provided by the Linux kernel.
- Namespaces provide resource isolation at different levels.
- Cgroups are about resource allocation, and ensure that a container can never go beyond the specified amount of resources.
- All containers run on top of a Linux kernel, providing these features.

#### Current Linux Namespaces

- **pid**: isolation for processes.
- **user**: presents isolated users only existing inside the namespace.
- **mnt**: the modren alternative to the **chroot** jail.
- **net**: provides isolated networking.
- **ipc**: inter-process communication, used for sharing memory.
- **uts**: UNIX time sharing, which allows processes to have their own hostname and domain name.
- **cgroup**: providing isolated root directories for each cgroup.
- **time**: used to virtualize the clock of a system.

### 3.3 Container Runtimes

#### Understanding Containers Engines

- In the early days of containers (2014/2015), containers were started and managed by a container engine.
- A container engine offers multiple components.
  - A core component that runs the containers.
  - Optionally a daemon that controls the containers.
  - A command line interface.
- Common container engines include and included.
  - Docker
  - LXC
  - Podman
  - Systemd-nspawn

#### Understanding Container Runtimes

- While container engines developed further, container runtimes were split off as separate projects.
- By splitting off container runtimes into different open source projects, it became easier to focus on devleloping and standardizing them.
- Containerd became the common runtime in Docker environments.
- CRI-o is the common runtime in Podman environments.

#### Understanding Runc

- Runc is the OCI standardized container runtime.
- It is used by Containerd as well as CRI-o.
- It focuses on running the container image.

### 3.4 Using Container Images and Registries

#### Understanding Container Images

- A container image is a package of software that includes everything that is needed to run an application: code, runtime, system tools, system libraries and settings.
- It consistes of a file system bundle and metadata describing what is used.
- Container images were introduced by Docker and are standardized by Open Containers Initiative (OCI).
- Images are commonly built using Dockerfile (AKA Containerfile) and commonly provided through image registries.
  
#### Understanding Container Registries

- The container registry is the distribution platform for container images.
- Docker Hub (hub.docker.com) is a common container registry.
- quay.io is commonly used in Red Hat-based containerized environments.
- Private registries can easily be created - typically by running a container that offers its services on port 5000.
- While using containers, you can refer to any registry by specifying the full path to the iamge: docker run **localhost:5000/myapp/latest**

### 3.5 Running Docker Containers

#### Demo: Installing Docker

```bash
# Install Docker on Ubuntu.
cd 
mkdir -p ~/bin && cd $_
curl -fsSL https://get.docker.com -o get-docker.sh;
sudo sh get-docker.sh;
sudo usermod -aG docker ${USER};
newgrp docker;
sudo reboot;
docker run hello-world;
```

#### Demo: Running Docker Container

```bash
docker run ubuntu;
docker ps;
docker ps -a;
docker run -d nginx;
docker ps;
docker run -it ubuntu sh; Ctrl+p, Ctrl+q
docker inspect ubuntu;
docker rm ubuntu;
docker run --name webserver --memory="128m" -d -p 8080:80 nginx;
curl localhost:8080
```

### 3.6 Building Container Images

#### Creating Container Images

Roughly, there are three approaches to creating an image
- Using a running container: a container is started, and modifications are applied to the container. From the container, **docker** commands are used to write modifications.
- Using a Dockerfile: a Dockerfile/Containerfile container instructions for building an image. Each instruction adds a new layer to the image, which offers more control over which files are added to which layer.
- Use **buildah** to create an image by executing commands whitin the image.

#### Understanding Containerfile

- Dockerfile is a way to automate container builds.
- Containerfile is the same.
- It contains all instructions required to build a containr image.
- So instead of distributing images, you could just distribute the Dockerfile.
- Use **docker build** to build the container image based on the Dockerfile in the current directory.
- Images will be stored on your local system, but you can direct the image to be stored in a repository.

#### Dockerfile Best Practices

- Use Alpine as the base image. It is small and still a full Linux distribution.
- Run multiple commands in one **RUN** statement, connected by **&&**
- Split long **RUN** statements on multiple lines to keep them readable.
- Use **ENV** to set environment variables that are used by the entrypoint application.
- Use **COPY**, not **ADD**
- Use **USER** to run as a non-root user.

#### Demo: Using a Dockerfile

- Dockerfile demo is in **labs/dockerfile**
- Use **docker build -t nmap .** to run it from the currrent directory.
- Use **docker build  --no-cache -t nmap .** to ensure the complete procedure is performed again.
- Next, use **docker run nmap** to run it.
- For troubleshooting: **docker run -it nmap /bin/bash** 

#### Demo: Using the same with Alpine

- Dockerfile demo is in **labs/alpmap**
- Use **docker build -t alpmap .**
- **docker run -it alpmap**
- **docker image ls** and check the size
- **docker run -it alpmap sh**

#### ENTRYPOINT versus CMD

- ENTRYPOINT is the default command to be precessed.
- If not specified, **/bin/sh -c** is executed as the default command.
- Arguments to the **ENTRYPOINT** command should be specified separately using **CMD**
  - ENTRYPOINT["command"]
  - CMD["arg1", "arg2"]
- If the default command is specified using **CMD** insted of **ENTRYPOINT**, the command is executed as an argument to the default entrypoint **sh -c** which can give unexpected results.
- If the arguments to the command are specified within the **ENTRYPOINT**, then they cannot be overwritten from the command line.

#### Understanding Tags

- Tags are used to specify information about a specific image version.
- Tags are aliases to the imge ID and will show when using **docker images**
- Tags are typically set when building the image.
  - **docker build -t username/imagename:tagname**
  - For private use, the **username** part is optional, when pushing it to a public registry it is mandatory.
- Alternatively, tags can be set using **docker tag**
  - **docker tag source-image[:tag] targetimage[:tag]**
- If no tag is applied, the tag **:latest** is automatically set
    **:latest**always points to the latest verion of an image.
- Target image repositories can also be specified in the tag.

#### Tagging Images

- Tags allow you to assign multiple names to images.
  - A common tag is "latest", which allows you to run the latest.
- In CentOS, all images are in one repository, and tags are used to specify which specific image you want: **centos:7.6**
- Manually tag images: **docker tag myapache myapache:1.0**
  - Next, using **docker images | grep myapache** will show the same image listed twice, as 1.0 and as latest.

### 3.7 Implementing Container Security

#### Understanding Security Risks

- Containers address teh same kernel.
- Containers running as root.
- Public images may contain malicious software.

#### Cloud Native Security

- In Cloud native environment, four layers of security are identified.
  - Cloud/Colocation/Corporate datacenter.
  - Cluster
  - Container
  - Code
- Containers are only one part of them, and container security should always be integrated in a bigger picture.

#### Using Container Security Best Practices

- Include as little as possible in container images.
- Run rootless containers.
- Specify which user account to use in an image.
- Use verified images.
- Sign your images.
- Use access control on registries.
- Use Kubernetes for implementing Role Based Access Control (RBAC).
- Run containers on isolated networks.

### 3.8 Practice Questions

**Question 1:** Which of the following is the OCI standardized container runtime?
  1. CRI-o
  2. **runc [correct]**
  3. containerd
  4. container-shim

**Question 2:** Which of the following should you use when building a Dockerfile if you want to specify the primary application is such a way that it can not be overwritten while running the container?
  1. RUN
  2. CMD
  3. **ENTRYPOINT [correct]**
  4. START

**Question 3:** What is the name of the specific component that starts the container image?
  1. Hypervisor
  2. Container Engine
  3. **Container Runtime [correct]**
  4. Container Kernel

### Lesson 3 Lab: Building Container Images

- Create a Dockerfile that starts the vsftpd service.
- Apply security best practices while creating this Dockerfile, and minimize the number of layers created in the Dockerfile.

### Lesson 3 Lab Solution: Building Container Images

```bash
cd lab1/
vim Dockerfile
docker build -t myvsftpd .
docker images
docker run -d myvsftpd 
docker ps
```
