# Lesson 2: Understanding Cloud Native

- [Lesson 2: Understanding Cloud Native](#lesson-2-understanding-cloud-native)
    - [2.1 Cloud Native Architecture](#21-cloud-native-architecture)
    - [2.2 Cloud Native Components](#22-cloud-native-components)
      - [Understanding Cloud Native](#understanding-cloud-native)
      - [Analyzing Core Cloud Native Components](#analyzing-core-cloud-native-components)
    - [2.3 Serverless Computing](#23-serverless-computing)
      - [Understanding Serverlss](#understanding-serverlss)
      - [Understanding FaaS](#understanding-faas)
    - [2.4 Scalability](#24-scalability)
      - [Understanding Scalability](#understanding-scalability)
      - [Understanding Scalability Requirements](#understanding-scalability-requirements)
    - [2.5 Microservices](#25-microservices)
      - [Microservices applications](#microservices-applications)
      - [Cloud Native Microservices](#cloud-native-microservices)
    - [2.6 The 12-Factor App](#26-the-12-factor-app)
      - [Understanding 12-factor App](#understanding-12-factor-app)
      - [Identifying the 12 factors](#identifying-the-12-factors)
    - [2.7 Standards in Cloud Native Computing](#27-standards-in-cloud-native-computing)
      - [Understanding Standardization](#understanding-standardization)
      - [Understanding OCI Standards](#understanding-oci-standards)
      - [Other standads in cloud native](#other-standads-in-cloud-native)
    - [2.8 The Role of the CNCF](#28-the-role-of-the-cncf)
      - [Understanding CNCF](#understanding-cncf)
      - [Sandboxed Projects](#sandboxed-projects)
      - [Incubating Projects](#incubating-projects)
      - [Graduated Projects](#graduated-projects)
      - [Understanding Minimal Viable Governance](#understanding-minimal-viable-governance)
    - [2.9 Job Roles in a Cloud Native Environment](#29-job-roles-in-a-cloud-native-environment)
      - [Understanding Cloud Native Job Roles](#understanding-cloud-native-job-roles)
      - [Understanding the SRE](#understanding-the-sre)
    - [2.10 Practice Questions](#210-practice-questions)

### 2.1 Cloud Native Architecture

- Explanation about cloud.

### 2.2 Cloud Native Components

#### Understanding Cloud Native

- Cloud native is a solution that allows organizations to run scalable applications in cloud. It uses containers, service meshes, microservices, immutable infrastructure and declarative APIs or realize this.
- In cloud natvie, decoupled applications are used.
  - Decoupled applications have no direct relations with servers.
  - Decoupled applications are generic and have no site specific information.
- By applying automation, changes can be implemented frequently with minimul impact.
  - This is implemented using DevOps and GitOps procecures.
  - Declarative APIs allow cloud native systems to be used as disired state systems.

#### Analyzing Core Cloud Native Components

- **Declarative APIs**: used to push the desired state to the system.
- **Containers**: runnable packages of software that container all elements necessary to run in any environment.
- **Scalable Applications**: applications that have the potential to grow when needed.
- **Microservices**: an architecture for building distributed applications consisting of different components, typically based on containers.
- **Service Mesh**: an infrastructure layer that implements traffic between different services.
- **Change Management**: use DevOps procedures to make high-impact changes frequently and predictably with minimal effort. 

### 2.3 Serverless Computing

#### Understanding Serverlss

- The term "Serverless" indicates to the developers that they just have to provide the code and don't worry about anything ralated to infrastructure.
- Behind serverless computing, there is the reality of networks, storage, and more of the raditional computing components that needs to be drefined in the cloud.
- In the end, to implement the cloud, datacenters with physical servers are still needed. 

#### Understanding FaaS

- Cloud provides are offering serverless computing as Function as a Service (FaaS).
- In FaaS, the software developer just has to deliver a package in the rith format to run their application in the cloud.
- Container images are a common format to do so.
- Knative is the Kubernetes add-on for serverless computing.
- CloudEvents provides the open standard for FaaS. 

### 2.4 Scalability

#### Understanding Scalability

- Scalability is about the option to increase capacity when needed.
- Cloud applications can be scaled manually or automatically.
- Autoscalling increases the resilience and availability of cloud native applicatins.
- Automatic scalling can happen in two ways.
  - Horizonal scalling relates to scalling of additional instances to meet with increasing demand.
    - Application instances can be spawned on demand.
    - Virtual or physical compute resources can also be scaled.
  - Vertical scalling is about increasing hardware resources in compute instances.
    - Virtual machies can dynamically get more RAM and CPU.
    - In some cases, this is also possible for physical hardware.

#### Understanding Scalability Requirements

- Automated scalling requires measuring the use of resources.
- Performance metrics must be gathered to trigger automatic resource scalling.
- Also, minimum and maximum requirements should be defined.

### 2.5 Microservices

#### Microservices applications

- Clould native applications are mircoservices-oriented by nature.
- The application is offered as different components.
- These different components are independently developed.
  - This makes it easier to develop the application.
  - Also, indivudual components can be updated individually.
- Cloud native platform resources are used to connect these components together.
  - Think of variables and ConfigMaps is Kubernetes environments.

#### Cloud Native Microservices

- Because application components are independent, maintenance occurs on parts of the application and net the entire application.
- Application updates have less impact on application accessibility.
- Because of the scalability offered by the platform, in many cases zero-downtime application updates are offered.
- To ensure smooth updates, CI/CD pipelines are used.
- The 12-factor app is commonly used as an application development model to check if an application is well designed for cloud native environments.

### 2.6 The 12-Factor App

#### Understanding 12-factor App

- The 12-factor app is a development methodology for building cloud applications that.
  - Use declarative formats.
  - Offer maximum portability.
  - Are suitable for deployment on cloud platforms.
  - Enable continuous deployment, which minimizes divergence
  - Allow for easy scaling of applications.
- 12-factor app-based DevOps explains why orchestrating containerized workloads inKubernetes is essential.
- See 12factor.net for more details.

#### Identifying the 12 factors

- **Codebase**: One codebase, tracked in revision control: Git, declarative code, Dockerfile.
- **Dependencies**: Explicitly declare and isolate dependencies, Kubernetes probes, init containers.
- **Config**: Store config in the environment: ConfigMap
- **Backing Services**: Treat backing services as attached resources: Service resources, pluggable networking.
- **Build, release, run**: Strictly separate build and run stages: CI/CD S21, Git branches, Helm.
- **Processes**: Execute the app as one or more stateless processes: Microservices, Linux kernel namespaces.
- **Port Bunding**: Export services via port binding: K8s Services, Routes.
- **Concurrency**: Scale out via the process model: K8s ReplicaSets.
- **Disposability**: Maximize robustness with fast startup and graceful shutdown: K8s probes.
- **Dev/prod parity**: Keep development, staging, and production, as similar as possible: containers.
- **Logs**: Treat logs as event streams: logs stored in the orchestration layer. 
- **Admin processes**: Run admin/management tasks as one-off processe: Ansible playbooks, Kubernetes Jobs.

### 2.7 Standards in Cloud Native Computing

#### Understanding Standardization

- To guarantee interoperability, open standards are common in cloud native.
- Much is based on open source technology, which allows anyone to use and participate in developing the resulting software, and prevents vendor lock-in.
- Containers have become the standard for packaging applications.
- Container formats are standardized by the Open Containers initiative (OCI)
- OCI is a Linux Foundation project that was started by Docker in 2015.

#### Understanding OCI Standards

- Some important specifications were defined within OCI
  - The image spec defines how to build and package container images.
  - The runtime spec defines how to run a container.
  - The distribution spec is more generic and defines the distribution of content, including container images.

#### Other standads in cloud native

- Container Network Interface (CNI) specifies how to implement networking for containers.
- Container Runtime Interface (CRI) defines how to implement container runtimes in orchestration systems.
- Container Storage Interface (CSI) specifies how to implement storage in container orchestration systems.
- Service Mesh Interface (SMI) describes how to implement service mesh in orchestration systems.

### 2.8 The Role of the CNCF

#### Understanding CNCF

- Cloud Native Computing Foundation (CNCF) is a project within Linux Foundation.
- It hosts different projects relevant for cloud native.
- To indicate the current status of a project, it goes through three phases.
  - Sandboxed.
  - Incubated.

#### Sandboxed Projects

- The project is in a very early stage.
- It shows significant code maturity and community involvement.
- It is adopted because it offers unrealized potential.
- Sandbox projects receive minimal funding.
- They are subject to removal every twelve months.

#### Incubating Projects

- A sandboxed project can become incubating if it shows growth and maturity.
- Incubating projects are in production use by at least three companies.
- They have a healthy development team that approves and accept regular contributions from the community.

#### Graduated Projects

- Incubated projects can be voted by the Technical Oversight Committee (TOC) to have reached graduation.
- Graduated projects have shown thriving adoption.
- They have committers from at least two organizations.
- Governance processes are documented and structured.
- They meet the Linux Foundation best practices.
- See https://www.cncf.io/projects for a current list of incubating and graduated projects.

#### Understanding Minimal Viable Governance

- The CNCF (TOC) has a central role.
  - Approves new projects.
  - Defines common practices.
  - Maintains the technical vision.
- The CNCF TOC practives Minimal Viable Governance
  - CNCF project should be self-governing and community owned.
  - The CNCF provides guidelines on how to implement governance.
  - The idea is that project members impose rules on themselves and enforce them.

### 2.9 Job Roles in a Cloud Native Environment

#### Understanding Cloud Native Job Roles

With the rise of cloud native, new job roles have emerged.
- **Cloud architect**: designs the cloud infrastructure.
- **DevOps Engineer**: responsible for the entire application.
- **Security engineer**: takes care of cloud security.
- **DevSecOps engineer**: responsible for the application lifecycle, including security.
- **Data engineer**: manages the large amounts of data that are found in cloud environments.
- **Full stack developer**: takes care of the entire stack.
- **Site Reliability Engineer (SRE)**: responsible for reliability and scalability of software.

#### Understanding the SRE

- Site reliability Engineer (SRE) as a concept was introduced by Google (https://sre.google/sre-book/service-level-objectives/)
- The SRE focuses on intergration of software in the cloud.
- To measure application reliability and performance, the following metrics are typically used.
  - **Service Level Indicators (SLI)**: "An SLI is a service level indicator a carefully defined quantitative measure of some aspect of the level of service that is provided", expressed as latency, error rate of system throughput.
  - **Service Level Objectives (SLO)**: "A target value or range of values for a service level that is measured by an SLI", such as "reaching a latency of less than 50ms".
  - **Service Level Agreements (SLA)**: "An explicit or implicit contract with your users tht includes consequences of meeting (or missing) the SLOs they contain."

### 2.10 Practice Questions

**Question 1:** To indicate the current status of a project, CNCF uses different phases. Which of the following is the firt phase?
  1. Introduced
  2. Incubated
  3. **Sandboxed [correct]**
  4. Graduated

**Question 2:** "Obtain a latency of less than 50ms" is an example of what?
  1. Service Level Indicator
  2. **Service Level Objectives [correct]**
  3. Service Level
  4. Service Level

**Question 3:** What is the name of the foundation that aims at standardization in the container landscape?
  1. Container Foundation
  2. **Open Containers Initiative [correct]**
  3. Cloud Native Computing Foundation
  4. Linux Foundation
