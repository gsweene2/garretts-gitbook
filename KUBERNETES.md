# Kubernetes

<!-- toc -->

## Questions to know

## Use kubeadm to configure cluster (Ubuntu)

### Install Packages (all nodes)

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

echo "[step] creating containerd configuration"
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

echo "[step] loading overlay and br_netfilter modules"
sudo modprobe overlay
sudo modprobe br_netfilter

echo "[step] Setting system configuration for networking, and applying settings"
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
sudo sysctl --system

echo "[step] Updating packages and installing containerd"
sudo apt-get update && sudo apt-get install -y containerd

echo "[step] Creating a default configuration file for containerd"
sudo mkdir -p /etc/containerd

echo "[step] generating a default containerd configuration and save to config.toml"
sudo containerd config default | sudo tee /etc/containerd/config.toml

echo "[step] restarting containerd"
sudo systemctl restart containerd

# To check the containerd status
# sudo systemctl status containerd

echo "[step] disabling swap"
sudo swapoff -a

echo "[step] disabling swap in /etc/fstab (affects startup)"
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

echo "[step] updating packages and installing apt-transport-https, curl"
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

echo "[step] downloading and adding GPG key"
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

echo "[step] adding kubernetes to the repository list"
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

echo "[step] updating packages"
sudo apt-get update

echo "[step] installing kubernetes packages kubelet, kubeadm, kubectl"
sudo apt-get install -y kubelet=1.21.0-00 kubeadm=1.21.0-00 kubectl=1.21.0-00

echo "[step] disabling automatic updates for kubelet kubeadm kubectl"
sudo apt-mark hold kubelet kubeadm kubectl
```

### Initialize k8s (control plane node)

```bash
#!/bin/bash
sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.21.0
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

### Finally, join worker nodes to cluster (worker nodes)


#### Part 1: Create token for kubeadm join (control plane node)

```bash
kubeadm token create --print-join-command
```

Expected Output:
```bash
kubeadm join 10.0.1.101:6443 --token bac4le.ryigteenwo5l3a0l --discovery-token-ca-cert-hash sha256:2f56a2f1b6b0bca1c3d54f338b7d4d21ef768ef5b01d14ec373b931480310f67
```

#### Part 2: Run the command to join workers (worker nodes)

Note: run with sudo, the above join does not print with sudo

```bash
#!/bin/bash
export CONTROL_PLANE_IP=10.0.1.101
export CONTROL_PLANE_PORT=6443
export CONTROL_PLANE_TOKEN=bac4le.ryigteenwo5l3a0l
export CONTROL_PLANE_CA_HASH=2f56a2f1b6b0bca1c3d54f338b7d4d21ef768ef5b01d14ec373b931480310f67

sudo kubeadm join ${CONTROL_PLANE_IP}:${CONTROL_PLANE_PORT} --token ${CONTROL_PLANE_TOKEN} --discovery-token-ca-cert-hash sha256:${CONTROL_PLANE_CA_HASH}
```

### Confirm success (control plane node)

```bash
kubectl get nodes
```

Expected output:
```bash
NAME          STATUS   ROLES                  AGE     VERSION
k8s-control   Ready    control-plane,master   4m56s   v1.21.0
k8s-worker1   Ready    <none>                 36s     v1.21.0
k8s-worker2   Ready    <none>                 99s     v1.21.0
```

### Delete a node from the cluster

```bash
kubectl drain k8s-worker1 --ignore-daemonsets --delete-local-data
kubectl delete node k8s-worker1
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

## Namespaces

- How to create a new namespace in an existing cluster
- List current namespaces
- Locate a pod with a specific name and find namespace

### Create Namespace

```
kubectl create namespace dev
```

### List Namespaces

```
kubectl get namespace
```

And write to file

```
kubectl get namespace > namespaces.txt
```

### Find a pod in a specific namespace

```
kubectl get pods -n <namespace>
```

### Find a pod in all namespaces

```
kubectl get pods --all-namespaces
```
