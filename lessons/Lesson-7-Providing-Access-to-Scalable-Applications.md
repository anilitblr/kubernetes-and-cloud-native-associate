# Lesson 7: Providing Access to Scalable Applications

- [Lesson 7: Providing Access to Scalable Applications](#lesson-7-providing-access-to-scalable-applications)
    - [7.1 Understanding Scalability](#71-understanding-scalability)
      - [Understanding Scalability](#understanding-scalability)
    - [7.2 Scaling Applications Manually](#72-scaling-applications-manually)
      - [Manual Scalability](#manual-scalability)
    - [7.3 Configuring Autoscaling](#73-configuring-autoscaling)
      - [Configuring Autoscanning](#configuring-autoscanning)
      - [Demo: Configuring AutoScaler](#demo-configuring-autoscaler)
    - [7.4 Understanding Application Access Options](#74-understanding-application-access-options)
      - [Application Access Options](#application-access-options)
    - [7.5 Using Services](#75-using-services)
      - [Understanding Service Types](#understanding-service-types)
      - [Demo: Using Services](#demo-using-services)
    - [7.6 Exploring Ingress and NetworkPolicy](#76-exploring-ingress-and-networkpolicy)
      - [Understanding Ingress](#understanding-ingress)
      - [Understanding NetworkPolicy](#understanding-networkpolicy)
      - [Install Ingress](#install-ingress)
    - [7.7 Practice Questions](#77-practice-questions)
    - [Lesson 7 Lab: Exposing Scalable Applications](#lesson-7-lab-exposing-scalable-applications)
    - [Lesson 7 Lab Solution: Exposing Scalable Applications](#lesson-7-lab-solution-exposing-scalable-applications)

### 7.1 Understanding Scalability

#### Understanding Scalability

- Scalability is about increasing capacity when needed.
- Pods as well as cluster nodes can be scaled either manually or automatically.
- Horizontal scaling is about adding more machines or instances to the pool of resources.
  - This is also referrred to as scaling out.
- Vertical scaling refers to scaling by adding more power to machines
  - This is referred to as scaling up.

### 7.2 Scaling Applications Manually

#### Manual Scalability

- Use **kubectl scale** to scale the number of Pods in a ReplicaSet or Deployment, manually.
- Afternatively, use **kubectl edit** to change the number of Pods in a Deployment or ReplicaSet.
- To scale the number of nodes in a cluster, add the node using **kubeadm join**
- In Minikube, use **minikube node add** to add a node.
```bash
kubectl get deploy;
kubectl scale deployment myweb --replicas=3;
kubectl get deploy;
kubectl edit deploy myweb; # Change the replicas to 2.
kubectl get deploy;
```

### 7.3 Configuring Autoscaling

#### Configuring Autoscanning

- The HorizontalPodAutoscaler resource can be configured for automatically scaling Pods.
- Scaling happens based on application usage parameters.
- To gather these metrics, additional software is needed.
  - The Metrics server provides a relatively easy way to gather metrics.
  -  Alternatively, Prometheus Adapter for Kubernetes APIs can be used.
  -  Kubernetes-based Event Driven Autoscaler is a relatively new joint Red Hat / Microsoft project that can scale a wide range of objects automatically.

#### Demo: Configuring AutoScaler

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml # will install metrics server.
kubectl edit deploy -n kube-system metrics-server # Add it "--kubelet-insecure-tls" in container section.
kubectl get pods -n kube-system
kubectl top pods;
kubectl get pods;

kubectl apply -f https://k8s.io/examples/application/php-apache.yaml
kubectl get all;
kubectl autoscale deploy php-apache --cpu-percent=50 --min=1 --max=10
kubectl get hpa
# From another terminal.
kubectl run -it load-generator --rm --image=busybox --restart=Never -- sh -c "while sleep 0.10; do wget -q -O- http://php-apache; done"
kubectl get hpa;
kubectl get deploy php-apache

# back to the original terminal.
sleep 60; kubectl get hpa

kubectl get deploy php-apache;
kubectl delete pod load-generator;
kubectl get hpa # may need 2 minutes.
kubectl get deploy php-apache;
```

### 7.4 Understanding Application Access Options

#### Application Access Options

- Port-forwarding can be used to expose individual Pods on cluster node ports.
  - This is not scalable and nto cloud native.
  - Use for testing only.
- Services are the common way to access application.
  - A Service provides a single IP address to access applications.
  - The Service load balances traffic to the Pods it exposes.
  - Different Service types are available.
- Ingress can be used to provide Kubernetes integrated access to HTTP/HTTPS services.
  - Using Ingress is optionsl, not mandatory.

### 7.5 Using Services

#### Understanding Service Types

- Different Service types are availabe.
  - **ClusterIP**: accessible on an IP address that is visible to the cluster node only.
  - **NodePort**: port-forwards incoming traffic from node ports to a ClusterIP IP address to provide external access.
  - **LoadBalancer**: works in an environment that has an API to configure a loadbalancer instance. Common in public clouds.
  - **Headless**: Service without an IP address. For internal use between frontend and backend applications.
  - **ExternalName**: uses the internal Kubernetes DNS Pod to provide a DNS alias.

#### Demo: Using Services

```bash
kubectl create deployment nginxsvc --image=nginx
kubectl scale deployment nginxsvc --replicas=3
kubectl get all;
kubectl get all --show-labels
kubectl get all --selector app=nginxsvc
kubectl expose deployment nginxsvc --port=80
kubectl get svc;
kubectl get all --selector app=nginxsvc
kubectl describe svc nginxsvc # look for endpoints
kubectl get svc nginxsvc -o=yaml
kubectl get svc
kubectl get endpoints;
minikube ssh;
curl http://svc-ip-address
exit

kubectl edit svc nginxsvc # update the below content
  protocol: TCP
  nodePort: 32000
 type: nodePort
kubectl get svc;
curl http://$(minikube ip):32000 # from host machine.
```

### 7.6 Exploring Ingress and NetworkPolicy

#### Understanding Ingress

- Ingress provides external HTTP/HTTPS routes to Services within the cluster.
- To do so, an Ingress controller is used.
- Also, an external DNS server needs to be configured to forward traffic to the Ingress controller IP address.
- Different Ingress solutions are provided by the Kubernetes ecosystem.
- Each of them provide the following features, apart from Ingress controller specific additional features.
  - Load balancing.
  - TLS offloading and termination.
  - Name-based virtual hosting.
  - Path-based virtual hosting.

#### Understanding NetworkPolicy

- NetworkPolicy can be added to filter incoming and outgonig traffic.
- As traffic between Namespaces by default is not filtered, NetworkPolicy can be used to apply inter-namespace traffic filtering.
- Using a NetworkPolicy needs to be supported by the network agent that is used.

#### Install Ingress

```bash
minikube addons list;
minikube addons enable ingress;
kubectl get ns; # get Namespaces
kubectl get all -n ingress-nginx;
```

### 7.7 Practice Questions

**Question 1:** Which additional component is required to configure HorizontalPodAutoscaler?
  1. Service
  2. **MetricsServer [correct]**
  3. Ingress
  4. Daemonset

**Question 2:** If a microservice consists of a frontend and a backend part, which Service types would be appropriate for allowing access from the frontend to the backend?
  1. ExternalName
  2. **Headless [correct]**
  3. **ClusterIP [correct]**
  4. NodePort
  5. LoadBalancer

**Question 3:** Which resource needs to be configured to filter traffic between namespaces?
  1. None: It is disallowed by default.
  2. Ingress
  3. Service
  4. **NetworkPolicy [correct]**

### Lesson 7 Lab: Exposing Scalable Applications

- Run an nginx deployment that is configured with 3 Pods.
- Expose this application in such a way that it can be accessed by addressing cluster node ports.

### Lesson 7 Lab Solution: Exposing Scalable Applications

```bash
kubectl create deploy lab7 --image=nginx --replicas=3
kubectl get all --selector app=lab7
kubectl expose deploy -h | less
kubectl expose deploy lab7 --type=NodePort --port=80
kubectl get svc;
curl http://$(minikube ip):port
```
