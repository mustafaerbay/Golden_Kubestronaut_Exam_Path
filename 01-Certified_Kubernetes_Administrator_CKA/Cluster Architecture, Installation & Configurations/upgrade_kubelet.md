# Upgrade Kubelet


```
# check the current version of kubelet
kubelet --version
# install kubelet and pin it to version 1.34.2
# you can view this page from K8s docs during the exam: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

sudo apt-get update

# Download the public signing key for the Kubernetes package repositories
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Add the appropriate Kubernetes apt repository. Please note that this repository have packages only for Kubernetes 1.34
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# update packages from local repo sources
sudo apt update

# update package index and install kubelet version 1.34.2
# optionally run apt-cache madison kubelet
# sudo apt-cache madison kubelet

sudo apt install -y kubelet=1.34.2-1.1

# verify the version of kubelet has been upgraded to 1.34.2
kubelet --version
```

```
controlplane:~$ apt-get update
Hit:2 https://packages.mozilla.org/apt mozilla InRelease            
Hit:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.34/deb  InRelease
0% [Waiting for headers] [Waiting for headers]# install kubelet and pin it to version 1.34.2
# you can view this page from K8s docs during the exam: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

sudo apt-get update

# Download the public signing key for the Kubernetes package repositories
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

Hit:3 http://archive.ubuntu.com/ubuntu noble InRelease
Hit:4 http://archive.ubuntu.com/ubuntu noble-updates InRelease
Hit:5 http://security.ubuntu.com/ubuntu noble-security InRelease
Hit:6 http://archive.ubuntu.com/ubuntu noble-backports InRelease
Reading package lists... Done
controlplane:~$ # install kubelet and pin it to version 1.34.2
controlplane:~$ # you can view this page from K8s docs during the exam: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
controlplane:~$ 
controlplane:~$ sudo apt-get update

# Download the public signing key for the Kubernetes package repositories
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

Hit:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.34/deb  InRelease
Hit:2 https://packages.mozilla.org/apt mozilla InRelease            
Hit:3 http://archive.ubuntu.com/ubuntu noble InRelease              
Hit:4 http://archive.ubuntu.com/ubuntu noble-updates InRelease
Hit:5 http://archive.ubuntu.com/ubuntu noble-backports InRelease
Hit:6 http://security.ubuntu.com/ubuntu noble-security InRelease
Reading package lists... Done
controlplane:~$ # Add the appropriate Kubernetes apt repository. Please note that this repository have packages only for Kubernetes 1.34
controlplane:~$ echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /
controlplane:~$ 
controlplane:~$ 
controlplane:~$ apt-cache search kubelet
kubelet - Node agent for Kubernetes clusters
controlplane:~$ 
controlplane:~$ apt
apt                   apt-cache             apt-config            apt-ftparchive        apt-key               apt-sortpkgs
apt-add-repository    apt-cdrom             apt-extracttemplates  apt-get               apt-mark              
controlplane:~$ apt
apt                   apt-cache             apt-config            apt-ftparchive        apt-key               apt-sortpkgs
apt-add-repository    apt-cdrom             apt-extracttemplates  apt-get               apt-mark              
controlplane:~$ apt search kubelet
Sorting... Done
Full Text Search... Done
kubelet/unknown 1.34.3-1.1 arm64
  Node agent for Kubernetes clusters

controlplane:~$ apt install -y kubelet=1.34.3-1.1
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
kubelet is already the newest version (1.34.3-1.1).
0 upgraded, 0 newly installed, 0 to remove and 69 not upgraded.
controlplane:~$ 
controlplane:~$ kubelet --versiob
E0203 17:01:23.945467    8791 run.go:72] "command failed" err="failed to parse kubelet flag: unknown flag: --versiob"
controlplane:~$ kubelet --version
Kubernetes v1.34.3
```