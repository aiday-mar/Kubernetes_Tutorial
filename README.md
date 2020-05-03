# Kubernetes, Docker Tutorial

This is a repository containing code snippets of tutorials I followed while studying kubernetes through the following course : https://www.linkedin.com/learning/learning-kubernetes . First of all, we use the following command in the command prompt in order to set a new alias in the command prompt : `new-alias vi notepad` . A container is defined as : a collection of software processes unified by one namespace, with access to an operating system kernel that is shares with other containers and little to no access between containers. When it concerns Docker, the definition is as follows : it is an instance of a docker image containing the following three things, a docker image, an execution environment, a standard set of instructions.

There are two parts to Docker, there is the Docker store and Docker Engine. The Docker Engine is compromised of the runtime and packaging tools. The Docker Store is an online cloud service where users can store docker images, this is called also docker hub. Containers include the application and all its dependencies. The container is not tied to the platform it only requires docker engine to be installed on the computer. Containers help alleviate platform compatibility issues. There is no more process differences between dev and production environments, scaling applications becomes easier. These containers incorporate a microservice architecture. It helps build continuous integration and deployment pipelines.

Container orchestration solves the problem of deploying multiple containers either by themselves or as part of an application. An orchestrator provides hosts, instantiates containers on a host, restarts failing containers. Kubernetes is an open source platform designed to automate deploying, scaling and operating application containers. Kubernetes can be used to run Docker containers.

During registration, nodes register themselves with the master node. It includes also service discovery, which is the automatic detection of services and endpoints visa DNS. There are TCP, HTTP health checks of the application, which is also monitored by the controller.

The four big players in the orchestration are : Docker Swarm, Kubernetes, Mesos and Rancher. Kubernetes and Mesos are the two platforms most used in big companies.

One feature of Kubernetes is mutli-host container scheduling. This is done by the kube-scheduler, it assigns pods to nodes at runtime.

# Kubernetes Architeture

There is a master node, inside there is an API server, a scheduler linked to the API server which creates pods running on nodes. There is etcd which is a simple distributed key, value store. The command line interface is called kubectl. It has a kubeconfig file. The worker nodes are where the operations operate. There are two processes, kubelete and kube-proxy. The latter is the network proxy on a single node, it performs connections with the TCP, HTTP service. Containers are placed inside a pod. A pod is the smallest unit that can be scheduled into deployment by kubernetes. The kubelet process communicates with the pods, the kube-proxy routes packets to the pods from other services.

**Node**

The node has a kubelet running, container tooling like docker, a kube-proxy process running, and a processor like supervisord. Have at least a three node cluster. Minikube is a lightweight kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. In the Pod there is the Docker application container, storage resources, a unique network IP. There are various pod states : pending (accepted by the kubernetes system), running (when a pod has been scheduled on a node), succeeded (meaning that all the containers in the pod have exit with state zero), crashloopbackoff (when a container fails to start for some reason).

In this course the following controllers are covered : ReplicaSets, Deployments, DaemonSets, Jobs and Services.

ReplicaSets ensure that a specified number of replicas for a pod are running at all times. When a pod working on the job has crashed the ReplicaSet controller will start a new pod. The ReplicatSet declared during deployment and this is declared in a YAML file.

