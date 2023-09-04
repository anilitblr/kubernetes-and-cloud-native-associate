# Lesson 4: Orchestrating Containers

- [Lesson 4: Orchestrating Containers](#lesson-4-orchestrating-containers)
    - [4.1 Container Orchestration Basics](#41-container-orchestration-basics)
      - [Stand-alone Container Limitations](#stand-alone-container-limitations)
      - [Understanding Orchestration](#understanding-orchestration)
      - [Orchestration Additional Feataures](#orchestration-additional-feataures)
      - [Orchestration Platform Components](#orchestration-platform-components)
    - [4.2 Container Networking and the CNI](#42-container-networking-and-the-cni)
      - [Container Networking](#container-networking)
      - [Understanding Overlay Networking](#understanding-overlay-networking)
      - [Network Plugins and the CNI](#network-plugins-and-the-cni)
    - [4.3 Service Discovery](#43-service-discovery)
      - [Understanding Service Discovery](#understanding-service-discovery)
      - [Understanding Service Registry](#understanding-service-registry)
    - [4.4 Service Mesh](#44-service-mesh)
      - [Managing Cloud Native Networking](#managing-cloud-native-networking)
      - [Adding Cloud Native Networking to Applications](#adding-cloud-native-networking-to-applications)
      - [Understanding Service Mesh Proxies](#understanding-service-mesh-proxies)
      - [Service Mesh Solutions](#service-mesh-solutions)
    - [4.5 Managing Container Storage](#45-managing-container-storage)
      - [Understanding Storage Nature](#understanding-storage-nature)
      - [Configuring Persistent Storage](#configuring-persistent-storage)
    - [4.6 Practice Questions](#46-practice-questions)
    - [Lesson 4 Lab: Analyzing Docker Networking](#lesson-4-lab-analyzing-docker-networking)
    - [Lesson 4 Lab Solution: Analyzing Docker Networking](#lesson-4-lab-solution-analyzing-docker-networking)

### 4.1 Container Orchestration Basics

#### Stand-alone Container Limitations

- Stand-alone containers are not protected in any way.
- There is no scalability for stand-alone containers.
- Decoupling of stand-alone containers is difficult.

#### Understanding Orchestration

- Container orchestration offers a platform to run containers on top of cloud native.
- Orchestration allows for connecting multiple containers to applications that consist of multiple containers: microservices.
- Using microservices offers lots of benefits.
  - Focus on developing and managing small components.
  - Independent upgrade of these components.
  - Independent scalability.
- This is way microservices have become the standard way to run applications in cloud native.

#### Orchestration Additional Feataures

- Orchestration platforms are used to implement a cloud native computing platform.
- The platform as well as the microservices can be easily scaled.
- Availability of containers can be guaranteed.
- Flexible and decoupled networking as well as storage can be provided.

#### Orchestration Platform Components

- Orchestration platforms consist of two components.
  - The data plane is what hosts the containers.
  - The control plane is responsible for managing the containers.
- The data plane is commonly implemented as multiple worker nodes.
- The control plane is normally implemented as multiple control nodes configured in high Availability.

### 4.2 Container Networking and the CNI

#### Container Networking

- Container networking starts with the network namespace.
- Because of the network namespace, each container has its own IP address.
- Ports only need to be unique in the network namespace, so multiple containers can offer services at the same port.
- Access to containers normally happens by mapping a host port to the container port.

#### Understanding Overlay Networking

- Overlay networking creates a virtual network that allows containers running on different hosts to communicate.
- The overlay network manages IP addresses assigned to individual containers.
- The Container Network Interface (CNI) provides a standard for defining the overlay network.

#### Network Plugins and the CNI

- The CNI (https://github.com/containernetworking/cni) is a CNCF standard that defines overlay networking.
- The CNI is a common interface, used by Kubernetes but also by other projects.
- Plugins are provided to define how th overlay network is implemented.
- According to the features that are needed, plugins are provided.
- Common plugins include.
  - Weave
  - Flannel
  - Calico
  - Multus

### 4.3 Service Discovery

#### Understanding Service Discovery

- To connect to services, it's important to use names that are forwarding traffic to IP addresses.
- In orchestration this is a process that is automated.
- The service registry is used to store this information.
- Service registry can be offered as an internal DNS server.
- Alternatively, it can be implemented as a key-value store.
- Service discovery is the process to retrieve service information from the service registry.

#### Understanding Service Registry

- Cloud native DNS has an API that makes it easy for services to auto-register.
- Cloud native systems also have a key-value store that is used to store essential system information, and which may be used for service discovery.
  - **Etcd**: the standard key-value store in Kubernetes.
  - **Zookeeper**: an open source solution, commonly used in Apache environments.
  - **Consul**: a Hashicorp open source solution for storing service information.

### 4.4 Service Mesh

#### Managing Cloud Native Networking

- In cloud native networking, features are needed that are not commonly seen in stand-alone environments.
  - monitoring
  - access control
  - encryption
  - traffic flow management
- This functionality should be offered at the source: the applications used in the cloud native environment.

#### Adding Cloud Native Networking to Applications

- Roughly, there are two solutions for adding cloud native networking to applications.
  - Modify the applications to use libraries that implement required functionality.
  - Connect each application to its proxy, which is adding this functionality.
- Using Proxies is preferred, as that doesn't require the application to be modified.

#### Understanding Service Mesh Proxies

- Service mesh adds proxies to the main application to modify and filter incoming and outgoing network traffic.
- Different proxy solutions are available.
  - Nginx
  - Haproxy
  - Envoy
- The service mesh adds a control plane that allows these proxies to be managed.

#### Service Mesh Solutions

- Istio and Linked are the current common service mesh solutions.
- The Service Mesh Interface (SMI) defines a specification of how a service mesh should be implemented.
- The goal of the SMI is to provide standardization in service mesh.

### 4.5 Managing Container Storage

#### Understanding Storage Nature

- Container storage is ephemeral (short living)
- When a container is started, a read-writable layer is added to the directory where the image is stored on the host that runs the container.
- This read-writable layer is removed when the container is removed.
- Also, a host-based read-writable layer is not cloud native, as it is bound to a specific server.

#### Configuring Persistent Storage

- Volumes can be used to store container data persistently.
- Simple volumes give the container access to a directory on the host file system.
    - This directory is bind-mounted.
    - Using this approach is a security risk, as the container gets access to the host file system.
  - Advanced volumes can provide access to external storage.
  - In orchestration systems, volumes can make connection to external storage.
  - This allows accessing decoupled, shared storage in a flexible way.
  - Storage access is standardized in the Container Storage Interface (CSI).

### 4.6 Practice Questions

**Question 1:** Which of the following is not a common standard?
  1. Container Storage Interface
  2. Container Runtime Interface
  3. Container Network Interface
  4. **Container Host Interface [correct]**

**Question 2:** Using container volumes offers different benefits. Which of the following is not one of them?
  1. It allows data to survive container life time.
  2. It allows sharing storage between different containers.
  3. **It offers increased security [correct]**
  4. It separates site specific data from generic code.

**Question 3:** Which component is a service mesh takes care of traffic handling?
  1. **The proxy [correct]**
  2. The data plane
  3. The control plane
  4. The gateway

### Lesson 4 Lab: Analyzing Docker Networking

- Start a Docker container that is based on the nginx image. On the container host, use **ip a** to analyze networking. On the running container, use **docker inspect containername** to investigate network settings that have been made for the container.

### Lesson 4 Lab Solution: Analyzing Docker Networking

```bash
ip a; # Look for docker0 network interface
docker run -d nginx
docker inspect <CONTAINER ID>; # Look for NetworkSettings
docker network list;
docker network inspect <NETWORK ID>
```
