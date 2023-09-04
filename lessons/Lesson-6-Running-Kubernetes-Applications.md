# Lesson 6: Running Kubernetes Applications

- [Lesson 6: Running Kubernetes Applications](#lesson-6-running-kubernetes-applications)
    - [6.1 Kubernetes Resources](#61-kubernetes-resources)
      - [Understanding Kubernetes Resources](#understanding-kubernetes-resources)
      - [Understanding Resource Definition](#understanding-resource-definition)
      - [Common Objects](#common-objects)
    - [6.2 Using Kubernetes Internal Formats](#62-using-kubernetes-internal-formats)
      - [Understanding Kubernetes Formats](#understanding-kubernetes-formats)
      - [Understanding Required Fields](#understanding-required-fields)
      - [Using YAML](#using-yaml)
      - [Demo: Using YAML](#demo-using-yaml)
    - [6.3 Using kubectl](#63-using-kubectl)
      - [Using kubectl](#using-kubectl)
      - [Using Dashboard](#using-dashboard)
    - [6.4 Options for Running Applications](#64-options-for-running-applications)
      - [Application Resources](#application-resources)
      - [Deploying Applications](#deploying-applications)
      - [Using helm](#using-helm)
    - [6.5 Understanding Pods and Deployments](#65-understanding-pods-and-deployments)
      - [Understanding Pods](#understanding-pods)
      - [Multi-container Pods](#multi-container-pods)
      - [Common Pod Properties](#common-pod-properties)
      - [Understanding Deployments](#understanding-deployments)
    - [6.6 Running Pods](#66-running-pods)
      - [Running Pods](#running-pods)
    - [6.7 Practice Questions](#67-practice-questions)
    - [Lesson 6 Lab: Running Kubernetes Applications](#lesson-6-lab-running-kubernetes-applications)
    - [Lesson 6 Lab Solution: Running Kubernetes Applications](#lesson-6-lab-solution-running-kubernetes-applications)

### 6.1 Kubernetes Resources

#### Understanding Kubernetes Resources

- Kubernetes defines different objects for running resources in a cloud native environment.
- These objects (also referred to as resources) define properties needed to implement specific functionality.
- Usign these objects makes it possible to perform a wide range of specific tasks.
  - Run containerized workloads on specific nodes.
  - Store configuration without having a relation to a server.
  - Provide consistent access to applications that are not always running in the same environment.

#### Understanding Resource Definition

- Kubernetes objects defined in the API.
- Specific resources based on these objects are stored in the etcd.
- As the Kubernetes APIs are extensible, there is no fixed number of objects.
- Objects can be added using Custom Resource Definitions (CRDs)

#### Common Objects

- **Pod**: the minimal entity managed by Kubernetes, used for managing containers.
- **Deployments**: used to add scalability and zero-downtime application updates to applications.
- **Service**: exposes workloads by providing a single point of access.
- **ConfigMap**: used to store configuration.
- **Secret**: used to store sensitive data
```bash
# Start Kubernetes dashboard.
minikube dashboard;
```

- Click on **Plus**
- Chose **Create from form**
- App name: **MyFirstApp**
- Container image: **nginx**
- Number of pods: **3**
- Service: Choose **Internal**
- Port: **80** Target port: **80**
- Namespace: **default**
- Choose **Deploy**
- Go back to the terminial, Press **Ctrl+z** and the **bg**
```bash
kubectl get all;
```
### 6.2 Using Kubernetes Internal Formats

#### Understanding Kubernetes Formats

- Resources are stored in the etcd in JSON format.
- Most users frefer using YAML, which is easier to read.
- In YAML, indentation is used to identify relations between parent and child resources.
- In YAML, identation spaces are used, not tabs.

#### Understanding Required Fields

- **apiVersion**: the version of the API used to define this resource.
- **kind**: the resource kind defined.
- **metadata**: data used to identify the resource; includes the resource name.
- **spec**: the specification of the resource. Notice that the specification options are defined by the specific API version.
- **status**: a cluster-managed field that contains current status information.

#### Using YAML

- Using YAML is convenient for analyzing and using Kubernetes the declarative way.
- You shouldn't write YAML code, you should generate it.

#### Demo: Using YAML

```bash
kubectl create deploy myweb --image=nginx 
kubectl get deploy myweb -o yaml
kubectl create deploy myweb --image=nginx --dry-run=client -o yaml;
kubectl create deploy myweb --image=nginx --dry-run=client -o yaml > myweb.yaml; 
```

### 6.3 Using kubectl

#### Using kubectl

- **kubectl** is the main Kubernetes command line interface.
- Most people will never need to use any other tool.
- Use **source <(kubectl completion bash)** to set up convenient command line completion.
- **kubectl** works with many commands.
- Use **kubectl -h** (or tab completion) to get an overview of all of these commands.
- **kubectl api-resources** shows available resources.
- **kubectl api-versions** shows versions
- **kubectl explain <resource>** is used to get more information about resource properties.
- **kubectl explain <resource>.spec** shows fields in the resource spec.
- **kubectl explain --recursive <resource>** shows all fields available.

#### Using Dashboard

- Kubernetes Dashboard is provided by the Kubernetes project as a web-based interface for managing Kubernetes.
- Other web-based and graphical tools are available within specific distributions.
- Most Kubernetes users focus on using **kubectl** because of the broad functionality as well as standardization it offers. 
```bash
kubectl -h | less
kubectl completion -h | less
source <(kubectl completion bash)
kubectl api-resources | less
kubectl api-versions;
kubectl explain pod;
kubectl explain pod.spec;
kubectl explain --recursive pod.spec;
```

### 6.4 Options for Running Applications

#### Application Resources

- **Deployment**: is the standard resource for running scalable applications with the option to perform zero-downtime application updates.
- **StatefulSet**: is an alternative for the Deployments, commonly used for stateful applications like databases.
- **DaemonSet**: is an alternative for the Deployment that ensures that one application instance is started on each cluster node.
- **Job**: is used for single-shot application.
- **CronJob**: is used for scheduled applications.

#### Deploying Applications

- Applications can be created in declarative or imperative way, using the **kubectl** command.
```bash
kubectl create deployment myweb-1 --image=nginx --replicas=3
kubectl get all
kubectl create -f myweb.yaml # Create new resources.
kubectl apply -f myweb.yaml  # Update resources.
```

#### Using helm

- Commonly the **helm** utility is used to create applications.
- **helm** works with helm charts, which are declarative files that describe how the application should be created.
- **artifacthub.io** is a common resource where ready-for-use helm charts can be obtained.

### 6.5 Understanding Pods and Deployments

#### Understanding Pods

- The Pod is the smallest, managed entity in Kubernetes.
- Within a Pod, one or more containers can be started within the same namespace.
- IP addresses are assigned to Pods.
- Single-container Pods are the standard.

#### Multi-container Pods

- Multi-container Pods are common when a helper container extends the functionality of the primary container.
  - sidecar: extends and enhances the functionality of containers.
  - ambassador: represents data generated by a container.
  - adapter: adapts a container to a required format.
- An init container is a container that prepares something for the main container, which is started from the same Pod.

#### Common Pod Properties

- Pods are used as the basic unit in Kubernetes and add cluster functionality to the containers using different properties.
  - **resources**: used to define resource usage boundaries.
  - **securityContext**: used to grant specific permissions to the Pod.
  - **Probes**: test programs that can be used from within the Pod.
    - Liveness
    - Readiness
    - Startup

#### Understanding Deployments

- Deployments are a common way to run applications.
- They provide different useful features.
  - Zero-downtime application updates.
  - Scalability.
  - Availability.
- Don't run naked Pods, run deployments instead.
```bash
kubectl get pods;
kubectl get pods <pod-name> -o yaml | less
kubectl describe pod <pod-name>
```

### 6.6 Running Pods

#### Running Pods

- **kubectl run myapp --image=nginx** will run a Pod with the name myapp
- **kubectl run myapp --image=nginx --dry-run=client -o yaml > myapp.yaml** will generate a YAML file allowing you to run the application the declarative way.
- **kubectl create -f myapp.yaml** will implement the application.
```bash
kubectl run myapp --image=nginx
kubectl get pods;
kubectl delete pod <pod-name>
kubectl get pods;
kubectl run myapp --image=nginx --dry-run=client -o yaml > myapp.yaml
kubectl create -f myapp.yaml
```

### 6.7 Practice Questions

**Question 1:** Which resource would you use to create an application that runs one application instance on each cluster node?
  1. Statefulset
  2. Deployment
  3. **Daemonset [correct]**
  4. Job

**Question 2:** Which application resource should you use to implement a replicated database?
  1. **Statefulset [correct]**
  2. Deployment
  3. Daemonset
  4. Job

**Question 3:** Which resource should you use to run an application to completion?
  1. Statefulset
  2. Deployment
  3. Daemonset
  4. **Job [correct]**

### Lesson 6 Lab: Running Kubernetes Applications

- Create a YAML file to run an nginx application that starts 3 replicas, and run the YAML file to create the application.

### Lesson 6 Lab Solution: Running Kubernetes Applications

```bash
kubectl create deploy lab6 --image=nginx --replicas=3 --dry-run=client -o yaml > lab6.yaml
kubectl create -f lab6.yaml
kubectl delete -f lab6.yaml
```
