# Lesson 5: Understanding Kubernetes

- [Lesson 5: Understanding Kubernetes](#lesson-5-understanding-kubernetes)
    - [5.1 Exploring Kubernetes Architecture](#51-exploring-kubernetes-architecture)
      - [Kubernetes Origins](#kubernetes-origins)
      - [Understanding Kubernetes Components](#understanding-kubernetes-components)
      - [Understanding the API](#understanding-the-api)
      - [Understanding Namespaces](#understanding-namespaces)
    - [5.2 Options for Using Kubernetes](#52-options-for-using-kubernetes)
      - [Using Kubernetes](#using-kubernetes)
      - [Common Kubernetes Distributions](#common-kubernetes-distributions)
      - [Other Kubernetes Distributions](#other-kubernetes-distributions)
    - [5.3 Running Kubernetes in Minikube](#53-running-kubernetes-in-minikube)
    - [5.4 Kubernetes Access Control](#54-kubernetes-access-control)
      - [Understanding Access Control](#understanding-access-control)
      - [Authentication](#authentication)
      - [Authorization](#authorization)
      - [Admission Control](#admission-control)
    - [5.5 How Containers Run on Kubernetes](#55-how-containers-run-on-kubernetes)
      - [Running Containers in Kubernetes](#running-containers-in-kubernetes)
      - [Monitoring the CRI](#monitoring-the-cri)
    - [5.6 Kubernetes Networking](#56-kubernetes-networking)
      - [Understanding Networked Connections](#understanding-networked-connections)
      - [Implementing Networking](#implementing-networking)
    - [5.7 Kubernetes Scheduling](#57-kubernetes-scheduling)
      - [Understanding Scheduling](#understanding-scheduling)
      - [Influencing the Scheduling Process](#influencing-the-scheduling-process)
    - [5.8 Practice Questions](#58-practice-questions)
    - [Lesson 5 Lab: Running Minikube](#lesson-5-lab-running-minikube)
    - [Lesson 5 Lab Solution: Running Minikube](#lesson-5-lab-solution-running-minikube)

### 5.1 Exploring Kubernetes Architecture

#### Kubernetes Origins

- Kubernetes is based on Google Borg technology.
- It was open sourced in 2014 and donated to the Cloud Native Computing Foundation.
- It has become the heart of cloud native computing, in which other related technologies and solutions are integrated.

#### Understanding Kubernetes Components

The following components are used in Kubernetes architecture.
- **kube-apiserver**: validates API objects and provides a front-end.
- **etcd**: the key-value store in which all resources are stored.
- **kube-scheduler**: the component that communicates to the kubelet to schedule pods.
- **kube-controller-manager**: runs the controller process.
- **cloud-controller-manager**: links Kubernetes to the cloud.
- **container runtime**: runs containers within Kubernetes.
- **kubelet**: agent on all nodes that ensures containers are started.

#### Understanding the API

- Kubernetes provides a RESTful API that is exposed over HTTPS.
- The API is used to create, modify, delete or retrieve resources in Kubernetes.
- Client utilities like **kubectl** and **kubeadm** access the API.
- Alternatively, the API can be accessed directly using REST calls.

#### Understanding Namespaces

- Kubernetes namespaces are used to provide resource isolation.
- They can be used to group applications in such a way that access to all the required resources is easier to manage.
- Namespaces can be used to apply security related settings.
  - **Role Based Access Control (RBAC)**: permissions assigned to users.
  - **Network Policy**: cloud implemented firewall settings.
  - **Quota**: restrictions to the use of compute resources.

### 5.2 Options for Using Kubernetes

#### Using Kubernetes

- Kubernetes is offered in many different ways.
  - Hosted in cloud.
  - Installd on Permise.
  - Using learning environments like minikube.
- The source is "vanilla Kubernetes"
- Vanilla Kubernetes can be extended with many projects provided by the Kubernetes ecosystem.
- Distributions are offered to make working with Kubernetes easier.

#### Common Kubernetes Distributions

- Common distributions with focus on On Premise.
  - SUSE Rancher.
  - Red Hat OpenShift.
  - Google Anthos.
  - Canonical Kubernetes.
  - k3s
  - VMware Tanzu.

#### Other Kubernetes Distributions

- Cloud Hosted Kubernetes
  - GKE (Google)
  - AKS (Azure)
  - EKS (Amazon)
- Common minimal distributions
  - Minikube
  - KIND

### 5.3 Running Kubernetes in Minikube

```bash
# Install minikube on Ubuntu.
sh minikube-docker-setup.sh
minikube start --vm-driver=docker --cni=calico
minikube status;
kubectl get all;
```

### 5.4 Kubernetes Access Control

#### Understanding Access Control

- Kubernetes clients communicate with the API.
- In order to process requests, they need to go through three stages.
  - Authentication
  - Authorization
  - Admission Control

#### Authentication

- By default, users (such as kubeadmin) authenticate using PKI certificates.
  - Configuration is stored in ~/.kube/config
  - External authentication providers can be used as well.
- ServiceAccounts are used by Kubernetes resources to authenticate on the cluster.

#### Authorization

- Authorization is accomplished by Role Based Access Control (RBAC).
- Roles use verbs to define specific permissions.
- Role bindings connect users provided by the authentication provider to specifi roles.

#### Admission Control

- The admission controller intercepts request after authentication and authentication.
- Admission controllers are compiled into the kube-apiserver.
- They can be used to limit requests to create, delete, and modify objects.

### 5.5 How Containers Run on Kubernetes

#### Running Containers in Kubernetes

- Kubernetes manages Pods, it doesn't manage containers directly.
- The Pod adds properties required for managing containers in an orchestrated environment.
- In order to run a Pod, the kube-scheduler uses a scheduling algorighm to determine on which node the Pod is going to be started.
- The kubelet on that node is contacted.
- The kubelet runs the actual container using the container runtime.
- The Container Runtime Interface defines how the container is started.
  - Containerd is the most common runtime.
  - CRI-O is used in Red Hat environments.

#### Monitoring the CRI

- On Kubernetes nodes, the **crictl** utility can be used to monitor containers.
- It provides a generic tool that interfaces all currrent CRIs.
- Use **sudo crictl ps** to get a list of currently running containers.
- Use **sudo crictl inspect <NNNN>** to inspect containers based on container ID.
```bash
kubectl get pods -A;
kubectl get pod kube-apiserver-minikube -n kube-system
kubectl get pod kube-apiserver-minikube -n kube-system -o yaml | less
minikube ssh;
sudo crictl ps;
sudo crictl inspect <CONTAINER ID>
exit;
```  

### 5.6 Kubernetes Networking

#### Understanding Networked Connections

- Within Kubernetes, there are four types of network communication.
  - **Container-to-Container**: handled within the pod.
  - **Pod-to-Pod**: handled by a software defined network.
  - **Pod-to-Service**: handled by kube-proxy and packet filters on the node.
  - **External-to-Service**: handled by kube-proxy and node-based packet filters.
- All networking needs to meet the following requirements.
  - Direct communication between all Pods.
  - Direct communication between nodes and Pods.
  - No Network Address Translation.

#### Implementing Networking

- Networking is implemented using network plugins.
- The network plugins are provided by the Kubernetes ecosystem.
  - Calico
  - Weave
  - Flannel
  - Multus
  - NSX
  - And many more
- Specific plugins may be required for specific ffunctionality.
  - DNAT
  - Network Policy

### 5.7 Kubernetes Scheduling

#### Understanding Scheduling 

- In scheduling, the right node is selected to run a specific workload.
- The kube-scheduler makes the scheduling decision.
- After adding a new Pod to the etcd, the scheduler finds an eligible node to run it.
- After making the scheduling decision, the scheduler communicates to the kubelet on the selected node to start the Pod.

#### Influencing the Scheduling Process

- While scheduling a Pod, a resource request can be make to ensure that the workload is started on a node that meets these requirements.
- Alternatively, taints and tolerations, affinity and node selectors can be used to influence the scheduling process.
- If no requirements were specified, the Pods will be scheduled on the node currently running the least amount of Pods.

### 5.8 Practice Questions

**Question 1:** Which type of Kubernetes networking is handled by the overlay network?
  1. Container-to-Container
  2. **Pod-to-Pod [correct]**
  3. Pod-to-Service
  4. External-to-Service

**Question 2:** Which Kubernetes component is running on a worker node and contacted by the kube-scheduler to run Pods?
  1. The container runtime
  2. **kubelet [correct]**
  3. kube-proxy
  4. service

**Question 3:** In which of the following is Role Based Access Control used?
  1. **Authorization [correct]**
  2. Authentication
  3. Access
  4. Admission

### Lesson 5 Lab: Running Minikube

- Install Minikube to ensure that you have an environment in which you can practice working with Kubernetes.

### Lesson 5 Lab Solution: Running Minikube

Ref: https://minikube.sigs.k8s.io/docs/start/

```bash
#
# Install minikube on Ubuntu.
#

sh minikube-docker-setup.sh
minikube start --vm-driver=docker --cni=calico
minikube status;
kubectl get all;

#
# Install minikube on Windows.
#
# Open PowerShell and type below command to install it.

winget install minikube
minikube start
minikube kubectl -- get pods -A
```
