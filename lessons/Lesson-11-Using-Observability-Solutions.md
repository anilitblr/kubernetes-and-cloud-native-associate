# Lesson 11: Using Observability Solutions

- [Lesson 11: Using Observability Solutions](#lesson-11-using-observability-solutions)
    - [11.1 Exploring Prometheus](#111-exploring-prometheus)
      - [Understanding Prometheus](#understanding-prometheus)
      - [Understanding Prometheus Monitoring](#understanding-prometheus-monitoring)
    - [11.2 Running Prometheus](#112-running-prometheus)
      - [Understanding Core Metrics](#understanding-core-metrics)
      - [Demo: Running Standalone Prometheus](#demo-running-standalone-prometheus)
      - [Demo: Using the Expression Browser](#demo-using-the-expression-browser)
    - [11.3 Monitoring with Prometheus](#113-monitoring-with-prometheus)
      - [Extending Monitoring Options](#extending-monitoring-options)
      - [Configuring Alerting](#configuring-alerting)
      - [Demo: Configuring Alerting](#demo-configuring-alerting)
    - [11.4 Monitoring Kubernetes with Prometheus](#114-monitoring-kubernetes-with-prometheus)
      - [Understanding Requirements](#understanding-requirements)
      - [Prometheus Kubernetes Installation Options](#prometheus-kubernetes-installation-options)
      - [Additional Configuration Requirements](#additional-configuration-requirements)
      - [Demo: Running the Prometheus Operator](#demo-running-the-prometheus-operator)
      - [Demo: Exposing Prometheus](#demo-exposing-prometheus)
      - [Exploring Kubernetes Resources](#exploring-kubernetes-resources)
    - [11.5 Using Grafana](#115-using-grafana)
      - [Understanding Grafana](#understanding-grafana)
    - [11.6 Tracing and Jaeger](#116-tracing-and-jaeger)
      - [Understanding Tracing](#understanding-tracing)
    - [11.7 Optimizing Costs](#117-optimizing-costs)
    - [11.8 Practice Questions](#118-practice-questions)
    - [Lesson 11 Lab: Running Prometheus](#lesson-11-lab-running-prometheus)
    - [Lesson 11 Lab Solution: Running Prometheus](#lesson-11-lab-solution-running-prometheus)

### 11.1 Exploring Prometheus

#### Understanding Prometheus

- Prometheus is the de facto standard for metrics-based monitoring in a Kubernetes environment.
- Prometheus scrapes data for a specific endpoint, and represents this data in the Prometheus web interface that typically runs on port 9090.
- It presents different datatypes.
  - Gauge: the current value of any parameter (like memory usage)
  - Counter: the total amount of X (like network packets)
  - Summary: a summary
- Using this data, Prometheus is used for monitoring.
- Prometheus uses powerful expressions, written in PromQL to monitor different parts of the system.

#### Understanding Prometheus Monitoring

- Based on the data it gathers, the Prometheus monitoring system can be used for different purposes.
  - Observe statistics based on the gathered metrics.
  - Debug application activity.
  - Send alerts when critical threshoulds are reached.
- Prometheus is highly pluggable, and works with different solutions to extend its functionality.
- Some components are installed directly from the Prometheus website.
- Other components are provided from the ecosystem around Prometheus.

### 11.2 Running Prometheus

- Before integrating it with Kubernetes, it makes sense to run stand-alone Prometheus.
- Learn how it works, and configure some targets and rules before trying to connect it to Kubernetes.
- To understand what needs to be done in Kubernetes, it is essential to have insight in the local working.
- Prometheus uses its own query language, Prometheus Query Language (PromQL) to create queries.
- Grafana is often used with Prometheus to present data in a graphical way.
- AlertManager can be used with Prometheus to generate alerts.

#### Understanding Core Metrics

Prometheus works with 4 core metrics.
- **Counter**: an increasing value (packets received)
- **Gauge**: a value that can increase or decrease (memory usage)
- **Histogram**: a graphical representation of data.
- **Summary**: an overview of totals.

#### Demo: Running Standalone Prometheus

- Get the prometheus installation tarball from https://prometheus.io/download
- tar -xvf prometheus*.tar.gz
- cd prometheus*
- Create a prometheus.yml with the contents as in teh course git repository prometheus-1.yml file (copy it from the course git repo)
- Use **./prometheus** to start Prometheus
- Access the web interface at http://localhost:9090/
- Check **Status** > **Targets** to see the currently monitored targets
- Read the Metrics at http://localhost:9090/metrics

#### Demo: Using the Expression Browser

- In the upper left corner, click on **Prometheus**
- In the expression browser, type **Up** and notice how the expression browser automatically suggests completion options.
- Click **Execute** and observe the result, which at the moment is just showing Prometheus ifself.
- Type **process_resisdent_memory_bytes** and observe how, in table view, this is giving a number of bytes.
- Click the Graph view and see how memory usage evolved over time.

### 11.3 Monitoring with Prometheus

#### Extending Monitoring Options

- Different metric endpoints can be defined in Prometheus to extend the monitoring options.
- Node exporter is provided by the Prometheus project to monitor metrics from UNIX-like systems.
- To use it, it needs to be installed, and included in the prometheus.yml scrape config.

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar xvf node_exporter-1.6.1.linux-amd64.tar.gz
cd node_exporter-1.6.1.linux-amd64
./node_exporter
cp kcna/labs/prometheus-2.yml prometheus.yml # Copy prometheus-2.yml file from kcna project directory to the prometheus directory from where the service is started.
cd prometheus-2.36.2.linux-amd64
./prometheus
```
- Access the web interface at http://localhost:9090/
- Check **Status** > **Targets** to see the currently monitored targets
- Choose **Prometheus** to go back to the home page.
- Click on search box, Type **process_resisdent_memory_bytes{job="node"}** and choose **Execute**

#### Configuring Alerting

- Alerting allows for using different systems to send alerts on specific events.
- You can get information about failing systems from the interface manually, but that is undoable if many systems are monitored.
- For instance, query for **up == 0** in the expression browser to find targets that currently are not responding.
- To automate alerting, use an alert manager and rules to observe specific metrics.

#### Demo: Configuring Alerting

- Download alert manager from https://promethesus.io/download
- tar xvf alertmanager*
- cd alertmanager
- Edit alertmanager.yml with the desired sepcific alerting Configuration.
  - Notice that you will need an external system to cooperate, like an open SMTP smarthost that will send email without authentication.
- Edit the prometheus.yml file according to the prometheus-2.yml file to activte alerting.
- Create a rules.yml in the prometheus directory to define alerting rules.
- Ensure node exporter is currently stopped and check **Status** > **Alerts**

```bash
wget https://github.com/prometheus/alertmanager/releases/download/v0.25.0/alertmanager-0.25.0.linux-amd64.tar.gz
tar xvf alertmanager-0.25.0.linux-amd64.tar.gz
cd alertmanager-0.25.0.linux-amd64

cd prometheus-2.36.2.linux-amd64
cp kcna/labs/prometheus-3.yml prometheus.yml
cp kcna/labs/rules.yml .
./prometheus
```
- Access the web interface at http://localhost:9090/
- Chose **Alert** in the home page.

### 11.4 Monitoring Kubernetes with Prometheus

#### Understanding Requirements

- To scrape metrics from Kubernetes, it makes sense to run Prometheus as an application (Development) in Kubernetes.
- You will need a service to make it accessible, and optionally an Ingress resource as well.
- You will need some role Based Access Control (RBAC) settings to allow it to get information out of the Kubernetes etcd.
- You will need a ConfigMap to provide a custom prometheus.yml that tells Prometheus to scrape data from Kubernetes resources. 

#### Prometheus Kubernetes Installation Options

- Create the required manifest file yourself.
- Use one of the many examples you will find in the labs/kubernetes-prometheus
- Use a helm chart.
  - https://artifacthub.io/packages/helm/prometheus-community/prometheus#configuration
- Use an operator.
  - https://github.com/prometheus-operator/kube-prometheus

#### Additional Configuration Requirements

- Kubernetes components need annotations to allow metrics scraping.
- use the Prometheus Operator to work with a pre-configured environment that will collect metrics for all resources.

#### Demo: Running the Prometheus Operator

- Make sure the Kube worker node has 4 CPUs to host the metrics scraper Pods.
```bash
git clone https://github.com/prometheus-operator/kube-prometheus
cd kube-prometheus/
kubectl create -f manifests/setup/
kubectl create -f manifests/
kubectl get pods -n monitoring
kubectl get svc -n monitoring
```

#### Demo: Exposing Prometheus

- After installing the Prometheus operator, ClusterIP Services are started to provide access.
- Before being able to actually access the components, these Services need to be exposed.
- Recommended: use **kubectl port-forward** to expose a local port tht forwards to the services.
  - **kubectl -n monitoring port-forward svc/prometheus-k8s 9090**
  - **kubectl -n monitoring port-forward svc/grafana 3000**
  - Access Prometheus at http://127.0.0.1:9090
- As an alternative, the Service can be configured as NodePort or an Ingress can be used to provide access.

#### Exploring Kubernetes Resources

- With the basic configuration, different Kubernetes cluster-related resources can be discovered.
- Use the metrics explorer (globe symbol) for a list of all available resources and search for "kube".
```bash
kubectl get all -n monitoring;
kubectl -n monitoring port-forward svc/prometheus-k8s 9090 &
kubectl -n monitoring port-forward svc/grafana 3000 &
```
- Access Prometheus at http://127.0.0.1:9090
- Click **Globe icon** next to search box
- Search for **kube_daemonset_created**

### 11.5 Using Grafana

#### Understanding Grafana

- Grafana is an analytics and data visualization web application.
- It connects to data sources and provides charts, graphs, and alerts based on the settings that have been made.
- Grafana is commonly used with Prometheus to visualize the data that Prometheus has gathered.
- When using the Prometheus operator, Grafana is automatically installed. 

### 11.6 Tracing and Jaeger

#### Understanding Tracing

- Traces are useful to get insight into how a request is processed in a microservices architecture.
- In a trace, different events will be shown to indicate how exactly a request is processed.
- Traces can be stored any analyzed using a tracing system like Jaeger.
- Tracing is standardized in the OpenTelemetry project.

### 11.7 Optimizing Costs

Different methods can be used to optimize cloud usage costs.
- **Delete unused resources**: container using autoscalers to do this automatically.
- **Optimize capacity use**: there is no need to use machines with much more capacity then needed.
- **Reserve in advance**: pricing is often cheaper if resources are reserved in advance.
- **Use spot resources**: spot resources are cheap but non-guaranteed resources.

### 11.8 Practice Questions

**Question 1:** What data type would you use to observe the current value of a counter?
  1. Counter
  2. Summary
  3. **Gauge [correct]**
  4. Statics

**Question 2:** Which tool would you use to visualize data that has been collected?
  1. Prometheus
  2. Jaeger
  3. Traeffik
  4. **Grafana [correct]**

**Question 3:** Which took would you use to configure alerting?
  1. **Grafana [correct]**
  2. Prometheus
  3. Jaeger
  4. Traeffik

### Lesson 11 Lab: Running Prometheus

- Use the Prometheus configuration that is running in Kubernetes to show a list of all deployments that have been created in your cluster.

### Lesson 11 Lab Solution: Running Prometheus

- Access the web interface at http://localhost:9090/
- Type **kube_deployment_created** in search
- Click **Execute** and observe the result
