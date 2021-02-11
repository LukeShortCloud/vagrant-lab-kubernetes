# vagrant-lab-kubernetes

A [Vagrantfile](Vagrantfile) with related scripts are provided to setup a local lab environment with VirtualBox virtual machines. Ubuntu 20.04 is used for all of the virtual machines base image. This project was original forked from [michalgutowski/cka-lab](https://github.com/michalgutowski/cka-lab).

## Lab Diagram

Nodes:

- load-balancer (x1) = 1 vCPUs, 512 MiB RAM, 128 GiB storage, nic0 = NAT, nic1 = private Kubernetes network.
    - Packages installed: haproxy and  nfs-kernel-server.
    - HAProxy configured to proxy requests to `nodePort` Services in Kubernetes.
    - NFS export for use with PersisentVolumes in Kubernetes: `/opt/sfw/`.
- master (x3) = 2 vCPUs, 4 GiB RAM, 128 GiB storage, nic0 = NAT, nic1 = private Kubernetes network.
    - Packages installed: nfs-common.
- worker (x1) = 1 vCPUs, 2 GiB RAM, 128 GiB storage, nic0 = NAT, nic1 = private Kubernetes network.
    - Packages installed: nfs-common.

## Networking

Every node is assigned a static IP address.

```
$ grep cka /etc/hosts
192.168.56.100 ckalb
192.168.56.101 ckamaster1 ckamaster
192.168.56.102 ckamaster2
192.168.56.103 ckamaster3
192.168.56.104 ckaworker1
```

For the Kubernetes Container Networking Interface (CNI), a manifest file for Calico is provided: [kubernetes/calico.yaml](kubernetes/calico.yaml). The manifest will use the CIDR `10.244.0.0/16` for the pod networking on the private interface (nic1).

## Setup

- Deploy either a small or full lab.

```
$ vagrant up ckalb ckamaster1 ckaworker1
```

```
$ vagrant up
```

- Either connect to the lab automatically using `vagrant` or manually via the `student` user and the related SSH key. The password for the `student` user is `welcome1`. The root users in the virtual machines also have SSH access to all nodes.

```
$ vagrant ssh <NODE>
```

```
$ ssh student@<NODE_IP> -i id_rsa
```

## Getting Started with Kubernetes


- Log into the first master node. Then install either the containerd or CRI-O [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/).

```
$ vagrant ssh ckamaster1
```

- Then install the required packages for Kubernetes start the installation with `kubeadm`.

```
$ sudo apt install kubeadm=1.20.2-00 kubectl=1.20.2-00 kubelet=1.20.2-00
$ sudo apt-mark hold kubelet kubeadm kubectl
$ sudo kubeadm init --config=kubeadm-config.yaml --upload-certs 2> /dev/stdout | tee kubeadm-init.out
```

- On the other nodes, install the same container runtime and Kubernetes packages. The original `kubeadm init` command from the previous setup will have outputted instructions on how to join new nodes to the cluster.
