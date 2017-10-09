# Overview

Welcome to Sandy the sandpit. The end goal is a Kafka cluster with Docker and Kubernetes. Ordering of the individual components is important. Hand cranked deployments for now. [Concourse](http://www.concourse.ci) soon.
- [sandy-k8s-registry/README.md(this)](https://github.com/Klazomenai/sandy-k8s-registry)  Configuring k8s cluster and starting up a Docker registry
- [sandy-puppetserver/README.md](https://github.com/Klazomenai/sandy-puppetserver) Configuring the puppetserver
- [sandy-zookeeper/README.md](https://github.com/Klazomenai/sandy-zookeeper) Zookeper for Kafka. [Why?](https://stackoverflow.com/questions/23751708/kafka-is-zookeeper-a-must)
- [sandy-kafka/README.md](https://github.com/Klazomenai/sandy-kafka) Kafka cluster

# Environment

Ubuntu 17.04, Centos 6.9 guest. Using the bloated Centos 6.9 image as those were the initial requirements. Will eventually move to a more streamlined base image.

# It's hand cranking time :(

 - Install Docker. Tested using Ubuntu 17.04, instructions for Ubuntu and Docker-CE are [here](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/). Also tested using Centos 7.4 and Docker-CE, instructions are [here](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#install-docker-ce)
 - Install kubectl and Minikube as per the Kubernetes instructions are [here](https://kubernetes.io/docs/tasks/tools/install-minikube/)
 - Install a hypervisor, VirtualBox used for the time being. Install from [here](https://www.virtualbox.org/wiki/Linux_Downloads)

 - Looks like VirtualBox requires kernel-devel
```sh
sudo yum -y install kernel-devel
```

We'll be running an insecure Docker registry in the K8s cluster, add the following to `/etc/docker/daemon.json` on your local host.
```sh
{
  "insecure-registries" : ["registry:5000"]
}
```

 - Start the K8s cluster. Puppet needs quite a bit of memory, so the nodes need a minimum limit
```sh
minikube start --memory 4096 --insecure-registry=registry:5000
```

 - Create new Docker registry on the cluster
```sh
kubectl create -f registry.yaml
```

 - Get the node InternalIP...
```sh
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}'
```

 - ... then add it to /etc/hosts on both laptop and minikube VirtualBox host for `registry` entry

# La Fin

Once you're done with playing in the sand, delete minikube env, and bring `/etc/docker/daemon.json` to its initial state.
```sh
minikube stop
minikube delete
```
