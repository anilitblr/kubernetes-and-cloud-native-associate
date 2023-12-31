# Lesson 10: Exploring Cloud Native Observability

- [Lesson 10: Exploring Cloud Native Observability](#lesson-10-exploring-cloud-native-observability)
    - [10.1 Cloud Native Observability](#101-cloud-native-observability)
      - [Understanding teh Challenges](#understanding-teh-challenges)
      - [What is Observability](#what-is-observability)
      - [Monitoring versus Observability](#monitoring-versus-observability)
      - [Why Observability is Needed](#why-observability-is-needed)
      - [Connonly Observed Parameters](#connonly-observed-parameters)
    - [10.2 Telemetry](#102-telemetry)
      - [Understanding Telemetry](#understanding-telemetry)
    - [10.3 Logging in Cloud Native Environments](#103-logging-in-cloud-native-environments)
      - [Understanding Logging](#understanding-logging)
      - [Logging in Containerized Applications](#logging-in-containerized-applications)
    - [10.4 Managing Logs in Cloud Native Environments](#104-managing-logs-in-cloud-native-environments)
      - [Cloud Native Logging Requirements](#cloud-native-logging-requirements)
      - [Tools for Logging](#tools-for-logging)
    - [10.5 Practice Questions](#105-practice-questions)
    - [Lesson 10 Lab: Cloud Native Logging](#lesson-10-lab-cloud-native-logging)
    - [Lesson 10 Lab Solution: Cloud Native Logging](#lesson-10-lab-solution-cloud-native-logging)

### 10.1 Cloud Native Observability

#### Understanding teh Challenges

- For stand-alone applications it may be sufficient to monitor performance-related properties.
- In cloud native environments, the essence of observability is to monitor requests that move through the different components of a distributed system.
- In microservices-based environments, tracing is used to keep track of application activity.
- There is a focus for network related parameters.
  - Network latency
  - Network throughput
  - Application restarts

#### What is Observability

- Observability is about the ability to measure a system's current state based on the data that is generates.
- Observability watches the following data types
  - Logs
  - Metrics
  - Traces
- Observability relies on telemetry generated by instrumentation that comes from the endpoints in cloud systems.
- Telemetry is about measuring data at remote points, which are next transmitted to a central location for further analysis.

#### Monitoring versus Observability

- In monitoring, the mission is to be informed when expected performance issues arise.
- The key assumption in monitoring is that you are able to predict what kind of problems will occur.
- In observability, it is about the flexibility to explore what is going on to investigate situations that couldn't have been predicted.

#### Why Observability is Needed

- As a result of observability, system parameters can be modified to offer the same performance if the environment has changed.
  - Changing system parameters is not an integrated part of observability.
- The amount of processing power on a complex system depends on the requirements of other systems.
  - If an application starts a heavy I/O transaction, it will have impact on the availability of resources for other applications.
- It is the mission in observability to observe this, and automatically reconfigure the system so that the required performance can still be offered.

#### Connonly Observed Parameters

In observability, the following parameters are commonly observed.
- General platform stability.
- Impact for other applications if one application has increasing resource requests.
- Metrics thresolds.

### 10.2 Telemetry

#### Understanding Telemetry

- Telemetry means measuring at a distance.
- Data is gathered on remote systems and collected centrally for further analysis.
- In a container-based cloud environment, different data types need to be measured.
  - Messages that are generated in error situations.
  - Quantitative measurements over time.
  - Progression of requests as they pass through the system.

### 10.3 Logging in Cloud Native Environments

#### Understanding Logging

- Logging is about collecting data in specific situations.
- Logging has been happening on stand-alone application, and typically data is collected in different situations.
  - debug
  - info
  - warning
  - error
- On Linux systems, log messages are normally written to the STDOUT or STDERR or to a log file.

#### Logging in Containerized Applications

- In a containerized application, the STDERR doesn't exist, as the application is cloud based.
- In these applications, the cloud or container platform captures logs that the application would have written to STDERR or STDOUT.
- To read these logs, use **docker logs, crictl logs **or **kubectl logs**
```bash
kubectl get pods;
kubectl logs <POD_NAME>
minikube ssh;
sudo crictl ps;
sudo crictl logs <PID>
exit;
```

### 10.4 Managing Logs in Cloud Native Environments

#### Cloud Native Logging Requirements

- In a cloud native environment, logs need to be stored centrally.
- Three common approaches are used.
  - Node-level logging: the logs are stored on a node from which they can be transmitted to a central location.
  - Sidecar containers: an additional container is injected in a Pod to collect and transmit logs.
  - Application level logging: each individual application takes care of forwarding it own logs.
- Notice that application logging is the least favourable, as it would require each application to be configured according to the cloud system needs.

#### Tools for Logging

- **Fluentd**: an open source tool written for data collection.
- **Filebeat**: an agent-based log collector and forwarder.
- **Grafana Loki**: a log aggregation system inspired by Prometheuse.
- **OpenSearch**: a distributed open source and analytics suite for real-time application monitoring.

### 10.5 Practice Questions

**Question 1:** You need a log collection system. Which of the following does not apply?
  1. Fluentd
  2. Grafana Loki
  3. **Prometheus [correct]**
  4. OpenSearch

**Question 2:** Observability tools are commonly used for multiple purposes. Which of the following is not commonly monitored by them?
  1. **Log files [correct]**
  2. Network latency
  3. Network throughput
  4. Application restarts

**Question 3:** Which logging option should be used to collect log information directly from a Pod?
  1. Application level logging
  2. Node level logging
  3. Cluster level logging
  4. **Sidecar container-based logging [correct]**

### Lesson 10 Lab: Cloud Native Logging

- Start a mariadb container in your Kubernetes environment without providing any further parameters.
- Use the appropriate solution to analyze application behavior.

### Lesson 10 Lab Solution: Cloud Native Logging

```bash
kubectl run maria --image=mariadb
kubectl get pods;
kubectl describe pod maria;
kubectl logs maria;
```
