# Kubernetes, Docker Tutorial

This is a repository containing code snippets of tutorials I followed while studying kubernetes through the following course : https://www.linkedin.com/learning/learning-kubernetes . First of all, we use the following command in the command prompt in order to set a new alias in the command prompt : `new-alias vi notepad` . A container is defined as : a collection of software processes unified by one namespace, with access to an operating system kernel that is shares with other containers and little to no access between containers. When it concerns Docker, the definition is as follows : it is an instance of a docker image containing the following three things, a docker image, an execution environment, a standard set of instructions.

There are two parts to Docker, there is the Docker store and Docker Engine. The Docker Engine is compromised of the runtime and packaging tools. The Docker Store is an online cloud service where users can store docker images, this is called also docker hub. Containers include the application and all its dependencies. The container is not tied to the platform it only requires docker engine to be installed on the computer. Containers help alleviate platform compatibility issues. There is no more process differences between dev and production environments, scaling applications becomes easier. These containers incorporate a microservice architecture. It helps build continuous integration and deployment pipelines.

Container orchestration solves the problem of deploying multiple containers either by themselves or as part of an application. An orchestrator provides hosts, instantiates containers on a host, restarts failing containers. Kubernetes is an open source platform designed to automate deploying, scaling and operating application containers. Kubernetes can be used to run Docker containers.

During registration, nodes register themselves with the master node. It includes also service discovery, which is the automatic detection of services and endpoints visa DNS. There are TCP, HTTP health checks of the application, which is also monitored by the controller.

The four big players in the orchestration are : Docker Swarm, Kubernetes, Mesos and Rancher. Kubernetes and Mesos are the two platforms most used in big companies.

One feature of Kubernetes is mutli-host container scheduling. This is done by the kube-scheduler, it assigns pods to nodes at runtime.

# Kubernetes Architeture

There is a master node, inside there is an API server, a scheduler linked to the API server which creates pods running on nodes. There is etcd which is a simple distributed key, value store. The command line interface is called kubectl. It has a kubeconfig file. The worker nodes are where the operations operate. There are two processes, kubelete and kube-proxy. The latter is the network proxy on a single node, it performs connections with the TCP, HTTP service. Containers are placed inside a pod. A pod is the smallest unit that can be scheduled into deployment by kubernetes. The kubelet process communicates with the pods, the kube-proxy routes packets to the pods from other services.

The node has a kubelet running, container tooling like docker, a kube-proxy process running, and a processor like supervisord. Have at least a three node cluster. Minikube is a lightweight kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. In the Pod there is the Docker application container, storage resources, a unique network IP. There are various pod states : pending (accepted by the kubernetes system), running (when a pod has been scheduled on a node), succeeded (meaning that all the containers in the pod have exit with state zero), crashloopbackoff (when a container fails to start for some reason).

In this course the following controllers are covered : ReplicaSets, Deployments, DaemonSets, Jobs and Services.

ReplicaSets ensure that a specified number of replicas for a pod are running at all times. When a pod working on the job has crashed the ReplicaSet controller will start a new pod. The ReplicatSet declared during deployment and this is declared in a YAML file. A deployment controller provides declarative updates for pods and replica sets. The deployment manages a replica set which manages a pod.

DaemonSets ensure that all nodes run a copy of a specific pod. A job is a supervisor process for pods carrying out batch jobs. Jobs are used to run individual processes that run once and complete successfully. Services allow the communication between one set of deployments with another. Generally you use a service to get two pods in the deployment to talk to each other.

There are different kinds of services : internal (IP is only reachable within the cluster), external (endpoint availale through node IP). Labels are key/value pairs that are attached to objects like pods, services and deployments. 

Selectors are of two types : equality based and set based (IN, NOTIN, EXISTS). Namespaces are a way to divide cluster resources between users. The Kubelet on the other hand has several roles : it communicates with the API server to see if pods have been assigned to nodes, it executes pod containers via a container engine. To describe a pod we have a YAML type file called Podspec. The kubelet takes the Podspecs provided by the kube-apiserver and ensures the containers described are running. 

The network proxy is callde the kube-proxy. The service cluster IPs are found through the Docker-link compatible environment. There are three modes of kube-proxy : user space mode, Iptables mode, Ipvs mode. Kube-proxy watched the API server for the addition and removal of services. For each new service the kube-proxy opens a randomly chosen ports on the local node.

# Coding in Kubernetes

Check the minikube version with `minikube version`, and verify the kubectl version with `kubectl version`. We start the work by writing :

```
minikube start --kubernetes-version="v1.8.0" --vm-driver="hyperv" --hyperv-virtual-switch="Minikube"
```

I get into setting up the environment at the beginning :

```
minikube start 
// which is starting the Kubernetes 1.8 cluster, this way we are in a local kubernetes environment

kubectl get nodes 
// will return a list containing the name of the nodes with the status, the role and the version
```

The following code will deploy a basic hello world appplication, and this will be named as `hw`. The application will be deployed on port 80.

```
kubectl run hw --image=karthequian/helloworld --port=80
```

We can visualize the deployments, the set replicas, the pods, the services using the following commands : 

```
kubectl get deployments
kubectl get rs
kubectl get pods
kubectl get services
```

We can expose the service as follows by typing : 

```
kubectl expose deployment hw --type=NodePort
```

When typing `minikube service hw` this will open the web-browser and run this specific service.

When you run `kubectl get all` this will show all the deployed apps. It is also possible to get the corresponding YAML file of the deployment by running the following command : 

```
kubectl get deploy/hw -o yaml
```

When you type `vi helloworld-all.yml` this shows the document relative to the deployment and the services of the hello world application. We can scale the application as follows :

```
kubectl scale --replicas=3 deploy/hellowworld-deployment
```
So in the above we have then three replicas. This command will also create three pods.

# Kubernetes for production

We show the labels of the pods :

```
kubectl get pods --show-labels
```
Suppose we want to add the label of the pod helloworld to helloworldapp

```
kubectl label po/helloworld app=helloworldapp --overwrite
```
To check whether the pod has been labelled we execute

```
kubectl label pod/helloworld app-
```
When you want to create pods you write this in a `file.yml` file and then you can run the following command :

```
kubectl create -f file.yml
```
If we want to find all our pods which are in the production stage :

```
kubectl get pods --selector env=production --show-labels
```

Suppose for example you want to find the pods having a release version between 1.0 and 2.0, then you can write :

```
kubectl get pods -l 'release-version in (1.0, 2.0)'
```
Suppose you want to delete the pods having a specific dev-lead (the person who owns the pod) :

```
kubectl delete pods -l dev-lead={name}
```
When there is an error with a pod the `get pods` will return the corresponding pod as beint not completely ready.

When you did changes to your application you can upgrade the image of the application by upgrading the image. Suppose that we want to upgrade the deployment `navbar-deployment` then you can write :

```
kubectl set image deployment/navbar-deployment helloworld=karthequian/helloworld:blue
```

In the above `karthequian` is the user and the `blue` at the end specifies that we want a blue navigation bar. Now suppose we want to see the history related to a deployment then we write :

```
kubectl rollout history deployment/navbar-deployment
```
You can also undo changes, for example to undo a deployment you can type :

```
kubectl rollout undo deployment/navbar-deployment
```

When we want to run a pod we can write :

```
kubectl exec -it {pod name} {directory}
```

# Advanced Kubernetes 

We can visualize the list of addons by typing : `minikube addons list`. You can visualize kubernetes in the dashboard by typing : `minikube dashboard`. We can see all the namespaces by tyling : 

`kubectl get pods --all-namespaces`

We can create a configmap as follows and deploy it. Below the `log_level` is set to debug meaning that the errors at debug time are written to this log

```
kubectl create configmap logger --from-literal=log_level=debug
vi reader-configmap-deployment.yaml
kubectl create -f reader-configmap-deployment.yaml
kubectl get configmap/logger -o yaml
```
You can only find the logs corresponding to pods not deployments.

I can also create secrets through kubernetes by doing so :

```
kubectl create secret generic {key name} --from-literal=api_key={key}
```

We can find the yaml file for the key by writing :

```
kubectl get secret {key name} -o yaml
```
You can show the cronjobs by running the following command : `kubectl get cronjobs` . There is also the idea of `stateful sets`.

# Advanced Topics

You can install Kubernetes using the kubeadm tool. Kops is a library used to automate a cluster provisioned in AWS, it deploys high-availability masters. It generates configuration files for AWS CloudFormation and Terraform configuration. These virtual clusters are called namespaces.

cAdvisor is an open-source resource usage collector that was built for containers. The software auto-discovers all the containers in the given node, collects CPU, memory, network usage statistics.

There are some authentication module : client certs, static token file, OpenID connect, Webhook mode. The first, also called the client certificate, is enabled by passing the 

```
--client-ca-file=FILENAME 
```

option to the API server. This file name is the username of the request. The token file example is defined as follows :

```
--token-auth-file = FILE_WITH_TOKENS
```

The Webhook token service is a kube-apiserver which calls out to a service defined by you to tell it whether a token is valid or not.

There are some popular authorization modules for entreprises. These include : ABAC (attribute-based access control), RBAC (role-based access control), webhook. Role bindings grant permission to users, they group the users and give them role.

The webhook mode is such that the kube-apiserver calls out to a service defined by you to tell it whether a specific action can be performed - it sends the token and the action the token is trying to perform.
