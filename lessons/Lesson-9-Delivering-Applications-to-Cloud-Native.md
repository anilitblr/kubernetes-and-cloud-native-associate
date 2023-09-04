# Lesson 9: Delivering Applications to Cloud Native

- [Lesson 9: Delivering Applications to Cloud Native](#lesson-9-delivering-applications-to-cloud-native)
    - [9.1 The Cloud Native Application Lifecycle](#91-the-cloud-native-application-lifecycle)
      - [Cloud Native and DevOps](#cloud-native-and-devops)
      - [Understanding the applications Lifecycle](#understanding-the-applications-lifecycle)
      - [Understanding Infrastructure as Code](#understanding-infrastructure-as-code)
    - [9.2 Git](#92-git)
      - [Understanding Git](#understanding-git)
    - [9.3 CI/CD and Pipelines](#93-cicd-and-pipelines)
      - [Understanding CI/CD](#understanding-cicd)
      - [Common CI/CD Solutions](#common-cicd-solutions)
      - [What are CI/CD Pipelines?](#what-are-cicd-pipelines)
      - [Demo: Using Jenkins](#demo-using-jenkins)
      - [Demo: Running a Pipeline](#demo-running-a-pipeline)
    - [9.4 Understanding GitOps](#94-understanding-gitops)
      - [GitOps and Infrastructure as Code](#gitops-and-infrastructure-as-code)
      - [GitOps and CI/CD](#gitops-and-cicd)
      - [GitOps and Kubernetes](#gitops-and-kubernetes)
    - [9.5 Practice Questions](#95-practice-questions)
    - [Lesson 9 Lab: Using Jenkins](#lesson-9-lab-using-jenkins)
    - [Lesson 9 Lab Solution: Using Jenkins](#lesson-9-lab-solution-using-jenkins)

### 9.1 The Cloud Native Application Lifecycle

#### Cloud Native and DevOps

- To deliver applications in cloud native environments, DevOps has been an important framework.
- DevOps provides solutions such as Continuous Integration / Continuous Delivery (CI/CD) and GitOps to automate the process of building and updating applications.

#### Understanding the applications Lifecycle

- Source code is normally managed in a version control system.
- Git is the most common version control system used for developing software.
- After developing the software, it must be built into a container image.
- Next, the software should be automatically tested.
- In the last step, the application needs to be delivered.
  - A Dockerfile can be used to deliver to a container registry.
  - A YAML file can be used to deliver to Kubenetes.

#### Understanding Infrastructure as Code

- Infrastructure as Code (IaC) describes the infrastructure you want to deploy.
- Next, the infrastructure can automatically be delivered by addressing the cloud vendor API.
- Hashicorp Terraform is a common solutions for IaC.
- As a subject of IaC, there is Configuration as Code (CaC), where the desired state of the configuration is described in a manifest file and delivered to the infrastructure.
- Different CaC solutions are commonly used.
  - Ansible
  - Puppet
  - Chef
  - SaltStack

### 9.2 Git

#### Understanding Git

- Git is decentralized version control system.
- Git can work with branches or forks of the source code.
- Individual developers can work in a branch, where the changes can be merged back into the main code tree.

### 9.3 CI/CD and Pipelines

#### Understanding CI/CD

- Continuous Integration / Continuous Delivery (CI/CD) is about delivering applications in an automated way.
- CI/CD procedures can be integrated in Kubernetes.
- It starts with code that is checked out to Git.
- From there, software builds into container images can be automated using webhooks, gitlab actions or OpenShift S2I.
- Different tools, like Jenkins can be added to do the testing and as a CI/CD implementation.
- Deployment can be automated using tools like Jenkins, Tokton, ArgoCD or many others.
- The result is a running containerized application, which will automatically be updated whenever that is needed.
- In this procedure, Kubernetes is taking care of a smooth zero-downtime application update.

#### Common CI/CD Solutions

- Jenkins
- Spinnaker
- GitLabs
- OpenShift
- Tekton
- Argo CD

#### What are CI/CD Pipelines?

- A CI/CD pipeline is a series of steps that must be performed to deliver a new version of software.
- The CI/CD pipeline typically includes the following stages.
  - **Build**: the stage where the application is comppiled.
  - **Test**: the stage where code is tested.
  - **Release**: the stage where the application is delivered to the repository.
  - **Deploy**: the stage where the code is deployed to production.

#### Demo: Using Jenkins

```bash
sudo apt install openjdk-11-jdk;
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update
sudo apt-get install jenkins
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
- Access Jenkins at **http://localhost:8080** skip over initial user creation.
- Install suggested plugins.

#### Demo: Running a Pipeline

- From the Dashboard, select **New Item**
- Enter an item name: **myfirstpipeline**
- Select **Pipeline**, click **OK**
- Select **Pipeline** set **Definition** to **Pipeline Script**
- In the script code, manually copy contents of **labs/firstpipeline**
- Click **Apply**, **Save** 
- In the menu on the left, select **Build Now** It should run successfully.
- Click the build time and date, from there select **Console Output** to see console output
- Use **sudo docker images** to verify an image was created.

### 9.4 Understanding GitOps

#### GitOps and Infrastructure as Code

- Many things can be delivered as code and stored in a single Git repository.
- GitOps is a framework that takes DevOps best practices and applies them to infrastructure automation.
- These best practices include.
  - Version control
  - Collaboration
  - Compliance CI/CD

#### GitOps and CI/CD

GitOps and CI/CD can be integrated in two ways.
- **Push based**: the pipeline is the trigger to implement changes to the platform.
- **Pull based**: the agent watches the git repository for changes and compares the current state in the infrastructure with the state that is described in the Git repository.
- Flux and ArgoCD are common tools that implement a GitOps pull-based approach.

#### GitOps and Kubernetes

- Kubernetes and GitOps go very well together.
  - Kubernetes provides an API.
  - It is designed for declarative provisioning.
- This makes it easy to define the desired state of your Kubernetes cluster in manifest files in git and use that to define what your cluster is doing.

### 9.5 Practice Questions

**Question 1:** Which of the following is not a solution for CI/CD?
  1. **Terraform [correct]**
  2. Jenkins
  3. Spinnaker
  4. Tekton

**Question 2:** In a CI/CD pipeline, different stages are normally defined. In which stage is the software published in a repository for common use?
  1. Build
  2. Test
  3. **Release [correct]**
  4. Deploy

**Question 3:** You are looking to work with a GitOps-based solution where the git repository is monitored for changes. Which of the following should you use?
  1. **Flux [correct]**
  2. ArgoCD
  3. Terraform
  4. Tekton

### Lesson 9 Lab: Using Jenkins

- Use Jenkins to run the demo pipeline it provides.

### Lesson 9 Lab Solution: Using Jenkins

- From the Dashboard, select **New Item**
- Enter an item name: **myfirstpipeline**
- Select **Pipeline**, click **OK**
- Select **Pipeline** set **Definition** to **Pipeline Script**
- In the script code, choose **by sample Pipeline**, **Hello World** 
- Click **Apply**, **Save** 
- In the menu on the left, select **Build Now** It should run successfully.
- Click the build time and date, from there select **Console Output** to see console output
