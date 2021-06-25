# Kubernetes

<!-- toc -->

## Questions to know

## Use kubeadm to configure cluster (Ubuntu)

### Install Packages on all nodes

#### Details

Couple notes about the script:
 - `tee` command reads the standard input and writes it to both the standard output and one or more files [Reference](https://www.geeksforgeeks.org/tee-command-linux-example/)
 - You can manually load a module into the kernel using the `modprobe` command, or automatically at boot time using `/etc/modules` or `/etc/modules-load.d/*.conf` files. [Reference](https://linuxize.com/post/modprobe-command-in-linux/)
 - `swapoff` disables swapping on the specified devices and files. When the -a flag is given, swapping is disabled on all known swap devices and files (as found in /proc/swaps or /etc/fstab). [Reference](https://linux.die.net/man/8/swapoff)
 - What is Swap Space? [Reference](https://opensource.com/article/18/9/swap-space-linux-systems)
    - The second type of memory in modern Linux systems is swap space.
    - The primary function of swap space is to substitute disk space for RAM memory when real RAM fills up and more space is needed.
    - Display existing partitions on the hard drive `fdisk -l`

#### Script

```bash
#!/bin/bash

cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

sudo sysctl --system

sudo apt-get update && sudo apt-get install -y containerd

sudo mkdir -p /etc/containerd

sudo containerd config default | sudo tee /etc/containerd/config.toml

sudo systemctl restart containerd

sudo systemctl status containerd

sudo swapoff -a

sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

sudo apt-get update && sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update

sudo apt-get install -y kubelet=1.21.0-00 kubeadm=1.21.0-00 kubectl=1.21.0-00

sudo apt-mark hold kubelet kubeadm kubectl
```

### Initialize k8s on control plane node & install networking

```
sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.21.0
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

### Finally, join worker nodes to cluster

```
export CONTROL_PLANE_IP=10.0.1.101
export CONTROL_PLANE_PORT=6443
export CONTROL_PLANE_TOKEN=glwblk.vb23jji6aov6mfz3
export CONTROL_PLANE_CA_HASH=31bff1af7120e48fca3080984c6c4bb8cd6d91465673e4e53cfa281dc3ed23f9

sudo kubeadm join ${CONTROL_PLANE_IP}:${CONTROL_PLANE_PORT} --token ${CONTROL_PLANE_TOKEN} --discovery-token-ca-cert-hash sha256:${CONTROL_PLANE_CA_HASH}
```

### Delete a node from the cluster

```
kubectl get nodes
kubectl drain k8s-worker1
kubectl drain k8s-worker1 --ignore-daemonsets --delete-local-data
kubectl get nodes
kubectl delete node k8s-worker1
kubectl get nodes
```

#### Useful things

##### Get private IP

Get IP address on private node
```
hostname -I | awk '{print $1}'
```

##### Kubeadm Join errors

```
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR FileAvailable--etc-kubernetes-kubelet.conf]: /etc/kubernetes/kubelet.conf already exists
	[ERROR Port-10250]: Port 10250 is in use
```

In this case, you can use kubeadm to clean up the node.

`kubeadm reset` will run `preflight`, `update-cluster-status`,`remove-etcd-member`, and `cleanup-node`
