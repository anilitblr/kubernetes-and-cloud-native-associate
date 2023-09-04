# Lesson 8: Integrating Applications in Cloud Native

- [Lesson 8: Integrating Applications in Cloud Native](#lesson-8-integrating-applications-in-cloud-native)
    - [8.1 Understanding Decoupling](#81-understanding-decoupling)
    - [8.2 Kubernetes Storage Options](#82-kubernetes-storage-options)
      - [Managing Persistent Volumes](#managing-persistent-volumes)
    - [8.3 Using Storage](#83-using-storage)
      - [Demo: Using a Pod that Runs PV Storage](#demo-using-a-pod-that-runs-pv-storage)
    - [8.4 Managing Configuration](#84-managing-configuration)
      - [Configuration Options](#configuration-options)
      - [Understanding ConfigMap Use](#understanding-configmap-use)
      - [Understanding Secret Use](#understanding-secret-use)
    - [8.5 Using ConfigMaps and Secrets](#85-using-configmaps-and-secrets)
      - [Demo: Using ConfigMap for Variables](#demo-using-configmap-for-variables)
      - [Demo: Using ConfigMaps for Files](#demo-using-configmaps-for-files)
    - [8.6 Practice Questions](#86-practice-questions)
    - [Lesson 8 Lab: Decoupling Applications](#lesson-8-lab-decoupling-applications)
    - [Lesson 8 Lab Solution: Decoupling Applications](#lesson-8-lab-solution-decoupling-applications)

### 8.1 Understanding Decoupling

- In cloud native, the mission is to run scalable applications.
- Scalable applications cannot have a direct relation with specific servers.
- This is why specific applications need to be storage and configuration.
- To provide storage in a decoupled way, Persistent Volumes and Persistent Volumes Claims can be used.
- To provide configuration in a decoupled way, CongiMap as well as Secrets can be used.

### 8.2 Kubernetes Storage Options

- Pods can be configured with Volumes in the Pod specification.
- The Pod spec Volume points to a specific type of storage.
- This works in simple environments, but makes it impossible to use the same Pod specification in a different cluster, where other storage is used.
- To decouple, site-specific storage can be configured in a Persistent Volume (Pv) resource.
- To dynamically bind to this site-specific persistent Volume, the Pod can be configured with a Persistent Volume Claim (PVC).
- The Persistent Volume Claim contains a request of a specific storage size and access type (read/write) or read-only.
- It does NOT refer to a specific type of storage.

#### Managing Persistent Volumes

- Cluster-specific persistent storage can be created manually.
- StorageClass can be used to automatically provision persistent storage.
- To extend storage funtionality, operators can be used to add specific types of storage.
- Different storage operators are available: Rook is commonly used.

### 8.3 Using Storage

#### Demo: Using a Pod that Runs PV Storage

```bash
cd kcna/lab;

vim pv.yaml;
kubectl create -f pv.yaml;
kubectl get pv;

vim pvc.yaml;
kubectl create -f pvc.yaml;
kubectl get pvc;
kubectl get storageclass;
kubectl describe pv;

vim pv-pod.yaml;
kubectl create -f pv-pod.yaml;
kubectl exec -it pv-pod -- touch /usr/share/nginx/html/hello.txt;
minikube ssh;
ls -l /tmp/hostpath-provisioner/default/pv-claim
exit;

vim pv-pvc-pod.yaml;
kubectl create ns myvol;
kubectl get ns;
kubectl create -f pv-pvc-pod.yaml;
kubectl get pvc -n myvol;
```

### 8.4 Managing Configuration

#### Configuration Options

- Configuration could be built into the container image.
- That would limit the image usability, and would require a new image for every new application instance.
- To decouple configuration from the Pod, a ConfigMap can be used.
- The Secret is a base64-encoded ConfigMap.

#### Understanding ConfigMap Use

- A ConfigMap is used for two purposes.
  - To store environment variables.
  - To store configuration files.
- How the Pod refers to the ConfigMap depends on its use.
  - Use **EnvFrom** to refer to ConfigMap containing variables.
  - ConfigMap containing config files are mounted as volumes.

#### Understanding Secret Use

- Secrets can be used for three main purposes.
  - The **generic** Secret is used a base64 encoded ConfigMap.
  - The **tls** Secret is used to store TLS keys.
  - The **docker-registry** Secret type is used to store container registry access credentials.
- Secrets are not encrypted, they're just base64 encoded.
- Use Hashicorp Vault for a well-integrated encrypted alternative.

### 8.5 Using ConfigMaps and Secrets

#### Demo: Using ConfigMap for Variables

```bash
cd kcna/labs/
vim varsfile;
  MYSQL_ROOT_PASSWORD=password
  MYSQL_USER=anil

kubectl create cm mydbvars --from-env-file=varsfile; # cm stands for configmap.
kubectl get configmaps;
kubectl get cm;
kubectl get cm mydbvars -o yaml;

kubectl create deploy mydb --image=mariadb --replicas=3;
kubectl get all --selector app=mydb;
kubectl logs <POD_NAME>

kubectl set env deploy mydb --from=configmap/mydbvars
kubectl logs <POD_NAME>
kubectl get all --selector app=mydb;
kubectl get deploy mydb -o yaml;
kubectl exec -it <POD_NAME> -- env | grep MYSQL_
```

#### Demo: Using ConfigMaps for Files

```bash
echo "hello world" > index.html;
kubectl create cm myindex --from-file=index.html;
kubectl describe cm myindex;
kubectl create deploy myweb --image=nginx
kubectl edit deploy myweb;
  spec.template.spec
  volumes:
  - name: cmvol
    configMap:
      name: myindex
  spec.template.spec.containers
  volumeMounts:
  - mountPath: /usr/share/nginx/html
    name: cmvol

kubectl exec -it <POD_NAME> -- cat /usr/share/nginx/html
```

### 8.6 Practice Questions

**Question 1:** Which of the following should not be stored in a ConfigMap?
  1. Startup parameters
  2. Directories containing config files
  3. **Passowrds [correct]**
  4. **API keys [correct]**

**Question 2:** What type of volume should be used in the Pod spec if flexible cloud-native access to storage is required?
  1. Decoupled
  2. **PersistentVolumeClaim [correct]**
  3. None: it should be specified in the storage resources
  4. Storageclass

**Question 3:** What is needed to add a new non-currently supported storage type to a cluster?
  1. Storageclass
  2. **Operator [correct]**
  3. Persistent Volume
  4. Storage provisioner

### Lesson 8 Lab: Decoupling Applications

- Create a Secret that contains the variable MYSQL_ROOT_PASSWORD set to any value you like.
- Create a Deployment manifest file where this Secret is used to start a MySQL application. Ensure that all is included in one single manifest file.
- Start the application from the manitest file to verify it works as expected.

### Lesson 8 Lab Solution: Decoupling Applications

```bash
kubectl create secret -h | less
kubectl create secret generic -h | less
kubeclt create secret generic mydbpw --from-literal=MYSQL_ROOT_PASSWORD=password
kubeclt get secret mydbpw -o yaml
echo encrypted-password | base64 -d

kubeclt create deploy mysecretdb --image=mariadb --dry-run=client -o yaml > mysecretdb.yaml
kubeclt create deploy mysecretdb --image=mariadb

kubeclt set env -h | less
kubeclt set env --from=secret/mydbpw deploy/mysecretdb

kubeclt get deploy mysecretdb -o yaml # copy the env section.
vim mysecretdb.yaml # Paste the env seciton under container
kubeclt delete deploy mysecretdb
kubeclt create -f mysecretdb.yaml
kubeclt get all --selector app=mysecretdb
kubeclt get pod <POD_NAME> -o yaml
kubectl exec -it <POD_NAME> -- env | grep MYSQL_
```
