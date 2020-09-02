# DigitalAPI DevOps

Welcome! We are exicted that you are interested in learning DevOps, This README.md document will give you brief about:
1.  [Introduction to DevOps](#introduction-to-docker)
2.  [Docker](#docker)
    -  [Introduction to docker](#introduction-to-docker)
    -  [Docker Architecture](#docker-architecture)
        -  [Docker Daemon](#the-docker-daemon)
        -  [Docker Client](#the-docker-client)
        -  [Docker Registries](#docker-registries)
        -  [Docker Objects](#docker-objects)
            - [Docker Image](#docker-image)
            - [Docker Container](#docker-container)
3.  [Kubernetes](#kubernetes)
    - [Introduction to Kubernetes](#introduction-to-kubernetes)
    - [Feature provided by kubernetes](#feature-provided-by-kubernetes)
    - [Kubernetes Components](#kubernetes=components)
        - [Master Components](#master-components)
        - [Node Components](#node-components)
        - [AddOn Components](#addon-components)
    - [Kubernetes Client](#kubernetes-client)
    - [Kubernetes Objects](#kubernetes-objects)
    - [List of few kubernetes objects](#list-of-few-kubernetes-objects)
        - [Pods](#pods)
        - [Services](#services)
        - [Volume](#volume)
        - [Namespaces](#namespaces)
        - [Deployment](#deployments)
        - [ReplicaSet](#replicaset)
        - [Statefulset](#statefulset)
4.  [Maven](#maven)
5.  [Nexus](#Nexus)
6.  [Sonar](#sonar)
7.  [Jenkins](#jenkins)
8.  [CICD](#cicd)
9.  [Monitoring](#monitoring)
10.  [Reference Link](#reference-link)

## Introduction to DevOps
DevOps is a set of software development practices that combine software development (Dev) and information-technology operations (Ops) to shorten the systems-development life cycle while delivering features, fixes, and updates frequently in close alignment with business objectives

## Docker 
Docker is an open platform for developing, shipping, and running applications.

### Introduction to docker
- Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. 
- With Docker, you can manage your infrastructure in the same ways you manage your applications. 
- Major advantage is it can significantly reduce the delay between writing code and running it in production.
- Containerization Vs Virtualization

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://git.rjil.ril.com/digitalapi/tutorials/raw/master/DigitalAPI-DevOps/images/ContainerVsVm.png" width="300"/> <img src="https://git.rjil.ril.com/digitalapi/tutorials/raw/master/DigitalAPI-DevOps/images/vm.png" width="300"/>

As you can see the difference in the images below, Docker containers run on top of the host's Operation system. This helps you to improves efficiency. Moreover, we can run more containers on the same infrastructure than we can run Virtual machines because containers use fewer resources.

### Docker Architecture

- Docker uses a client-server architecture. 
- The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers
- The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. 
- The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://git.rjil.ril.com/digitalapi/tutorials/raw/master/DigitalAPI-DevOps/images/architecture.png" width="600"/>

#### The Docker Daemon
- The Docker daemon (dockerd) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes.
- A daemon can also communicate with other daemons to manage Docker services.

#### The Docker client
- The Docker client (docker) is the primary way that many Docker users interact with Docker
- The docker command uses the Docker API. The Docker client can communicate with more than one daemon

#### Docker registries
- A Docker registry stores Docker images. 
- Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. 
- You can even run your own private registry and you can configure to docker client
- When you use the docker pull or docker run commands, the required images are pulled from your configured registry. 
- When you use the docker push command, your image is pushed to your configured registry.
- In DigitalAPI we are using nexus as docker registry provider. 

#### Docker Objects

#### Docker Image
- An image is a read-only template with instructions for creating a Docker container. 
- To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it
- Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image

example of docker file

```Dockerfile
# our base image
FROM base-image

# Installations
RUN <some installation command1>

RUN <some installation command2>

# Copy from host to docker container
COPY <host-resources1> <docker-container-location>
COPY <host-resources2> <docker-container-location>


# tell the port number the container should expose
EXPOSE 5000

# run the application
CMD [<some command>]

# OR

ENTRYPOINT[<Some Command>]


```
Following command is used to build a docker image from docker file

```shell
docker build Dockerfile -t <imageTagName>
docker tag <imageTagName> <registryUrl>:<imageTagName>
docker push <registryUrl>:<imageTagName>
```
example
```shell
docker build Dockerfile -t employeemanagementApp
docker tag employeemanagementApp nexus.rjil.ril.com:employeemanagementApp
docker push nexus.rjil.ril.com:employeemanagementApp
```

To use this image you need to download it and the command for the same is
```shell
docker pull nexus.rjil.ril.com:employeemanagementApp
```

#### Docker Container
- A container is a runnable instance of an image
- You can create, start, stop, move, or delete a container using the Docker API or CLI
- Following is the command to run docker 

```shell
docker run <image-name>
```
When you run this command, the following happens
1. Docker downloads the image from configured registry by running this command `docker pull <image-name>`
2. Docker creates a new container, as though you had run a `docker container create` command manually
3. Docker allocates a read-write filesystem to the container
4. Docker creates a network interface to connect the container to the default network
5. Docker starts the container


## Kubernetes

### Introduction to Kubernetes
Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://git.rjil.ril.com/digitalapi/tutorials/raw/master/DigitalAPI-DevOps/images/container_evolution.png" width="800"/>

### Feature provided by kubernetes
- Service discovery and load balancing 
- Storage orchestration 
- Automated rollouts and rollbacks
- Automatic bin packing
- Self-healing
- Secret and configuration management

### Kubernetes Components


##### Master Components

##### Kube-apiserver
- Component on the master that exposes the Kubernetes API. 
- It is the front-end for the Kubernetes control plane.

##### ETCD
- Consistent and highly-available key value store used as Kubernetes’ backing store for all cluster data

##### Kube-scheduler
- Component on the master that watches newly created pods that have no node assigned, and selects a node for them to run on

##### Kube-controller-manager
- Component on the master that runs controllers
- Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process.
- These controllers include:
    - Node Controller
    - Replication Controller
    - Endpoints Controller
    - Service Account & Token Controllers

##### Cloud-controller-manager
- Component on the master that runs as controllers that interact with the underlying cloud providers.

--------------------------------

##### Node Components

##### Kubelet
- An agent that runs on each node in the cluster
- It makes sure that containers are running in a pod

##### Kube-proxy
- Kube-proxy is a network proxy that runs on each node in your cluster, implementing part of the Kubernetes Service concept
- kube-proxy maintains network rules on nodes

##### Container-runtime
- The container runtime is the software that is responsible for running containers.
- Kubernetes supports several container runtimes: Docker, containerd, cri-o, rktlet and any implementation of the Kubernetes CRI (Container Runtime Interface).

---------------------------------
##### AddOn Components

##### Kube DNS
- Cluster DNS is a DNS server, in addition to the other DNS server(s) in your environment, which serves DNS records for Kubernetes services

##### K8s Dashboard
- Dashboard is a general purpose, web-based UI for Kubernetes clusters.

##### Container Resource Monitoring
- Container Resource Monitoring records generic time-series metrics about containers in a central database, and provides a UI for browsing that data.

##### Cluster-level Logging
- A Cluster-level logging mechanism is responsible for saving container logs to a central log store with search/browsing interface.

-----------------------------------
### Kubernetes Client
- Kubenetes API
- kubectl command : the CLI makes the necessary Kubernetes API calls for accessing the desired Object

### Kubernetes Objects
- Kubernetes Objects are persistent entities in the Kubernetes system
- Kubernetes uses these entities to represent the state of your cluster
- To work with Kubernetes objects–whether to create, modify, or delete them–you’ll need to use the Kubernetes API
- Object Spec and Status
    - Object Spec: The spec, which you must provide, describes your desired state for the object–the characteristics that you want the object to have
    - Object Status: The status describes the actual state of the object, and is supplied and updated by the Kubernetes system

Example Deployement Object

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: api-management
  labels:
    app: api-management
spec:
 replicas: 1
 template:
    metadata:
      labels:
        app: api-management
      annotations:
        prometheus.io.scrape: "true"
        prometheus.io.probe: "true"
        prometheus.io.path: "/actuator/prometheus"
    spec:
      containers:
      - name: api-management
        image: nexus.rjil.ril.com:5000/api-management:ST
        env:
        - name: TZ
          value: "Asia/Kolkata"
        ports:
        - containerPort: 4200
          protocol: TCP
        imagePullPolicy: "Always"
        resources:
         requests:
          cpu: 500m
          memory: 1Gi
         limits:
          cpu: 1
          memory: 2Gi
      imagePullSecrets:
      - name: stsecret
```

Example Service Object

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-management
  labels:
    app: api-management
spec:
  ports:
    - protocol: TCP
      port: 4200
      targetPort: 4200
      nodePort: 30998
  selector:
      app: api-management
  type: NodePort

```
#### List of few kubernetes objects

##### Pods
- A Pod is the basic execution unit of a Kubernetes application–the smallest and simplest unit in the Kubernetes object model that you create or deploy
- A Pod represents processes running on your Cluster
- A Pod encapsulates an application’s container/s


##### Services
- An abstract way to expose an application running on a set of Pods as a network service
- Kubernetes ServiceTypes allow you to specify what kind of Service you want.
    - ClusterIP (Default)
    - NodePort
    - LoadBalancer
    - ExternalName

##### Volume
- ConfigMap
- PersistentVolume
- PersistentVolumeClaim
- Secret


##### Namespaces
- Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces

##### Deployment
- A Deployment controller provides declarative updates for Pods and ReplicaSets.

##### ReplicaSet
- A ReplicaSet’s purpose is to maintain a stable set of replica Pods running at any given time

##### StatefulSet
- StatefulSet is the workload API object used to manage stateful applications



## Maven
- Maven is a build automation tool used primarily for Java projects. 
- Maven addresses two aspects of building software: 
    - it describes how software is built <Maven Plugin>
    - it describes its dependencies <Maven Dependencies>

## Nexus
- Nexus is a repository manager. 
- It allows you to proxy, collect, and manage your dependencies so that you are not constantly juggling a collection of JARs
- It makes it easy to distribute your software. 
- Internally, you configure your build to publish artifacts to Nexus and they then become available to other developers

## Sonarqube
- Static Code Analysis 


## Jenkins
- Jenkins is a self-contained, open source automation server which can be used to automate all sorts of tasks related to building, testing, and delivering or deploying software
- We have created the following types of jobs in Jenkins (LOB and Environment wise)
    -   ST Deployment – Deploy latest / selected version code in ST K8s Cluster
    -   SIT/REPLICA/PROD - Deployment job 
    -   Image Promotion – Baseline image for promotion
    -   Log Check – Checks log for deployed services
    -   Service Restart – Restart services without re-deployment
    -   Stop and Start Service – Delete the deployment and Start deployment
    -   Thread Dump – Takes thread dump across all pods
    -   Maven Artifact deployment - Deploys dependencies to Nexus Repository
    -   Scaling – Scale pods


## CICD
- Continuous integration 
![](https://git.rjil.ril.com/digitalapi/tutorials/raw/master/DigitalAPI-DevOps/images/CICD.PNG)

----------------------------------------
- Continuour Deployment
![](https://git.rjil.ril.com/digitalapi/tutorials/raw/master/DigitalAPI-DevOps/images/CICD2.PNG)

## Monitoring
- Prometheus
    - Prometheus is an open-source software application used for event monitoring and alerting. It records real-time metrics in a time series database built using a HTTP pull model, with flexible queries and real-time alerting.
- Grafana
    - Grafana is an open-source, general purpose dashboard and graph composer, which runs as a web application. Matrics collected by prometheus are used by grafana to show graphical representation of matrics.

## Reference Link
- https://docs.docker.com/get-started/
- https://kubernetes.io/docs/concepts
- https://maven.apache.org/

--------------------------------------
# Thank you
