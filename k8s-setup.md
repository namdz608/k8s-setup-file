## _Setup K8s (1 master- 1 worker)_

## resource
- Master Node:  192.168.1.173 – k8smaster.example.net
- First Worker Node:  192.168.1.174 – k8sworker1.example.net
- Second Worker Node:  192.168.1.175 – k8sworker2.example.net

## master node
 ```
 sudo hostnamectl set-hostname "k8smaster.example.net"
 exec bash
 ```
## worker node
 sudo hostnamectl set-hostname "k8sworker1.example.net"   // 1st worker node
 sudo hostnamectl set-hostname "k8sworker2.example.net"   // 2nd worker node
 exec bash

## both node

```
sudo vi /etc/hosts 
```

- 192.168.1.173   k8smaster.example.net k8smaster
- 192.168.1.174   k8sworker1.example.net k8sworker1
- 192.168.1.175   k8sworker2.example.net k8sworker2

![image](https://github.com/namdz608/k8s-setup-file/assets/72740871/f60a09a0-1f56-4b6b-a009-0fce26856cba)

```
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

```
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
```

```
sudo tee /etc/sysctl.d/kubernetes.conf <<EOT
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOT
```

```
sudo sysctl --system
```

```
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
```

```
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

```
sudo apt update
sudo apt install -y containerd.io
```

```
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
```

```
sudo systemctl restart containerd
$ sudo systemctl enable containerd
```

```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

```
sudo apt update
$ sudo apt install -y kubelet kubeadm kubectl
$ sudo apt-mark hold kubelet kubeadm kubectl
```

## master
```
sudo kubeadm init --control-plane-endpoint=k8smaster.example.net --upload-certs
```

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

```
sudo kubeadm token create --print-join-command
```
//copy sang worker

//Test Cluster
```
kubectl cluster-info
```

```
kubectl get nodes
```

//Install Calico Network Plugin

```
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/master/manifests/calico.yaml
```

```
kubectl get pods -n kube-system
```

kubectl get nodes


install helm chart
```
curl -L https://git.io/get_helm.sh | bash -s -- --version v3.8.2
```

## uninstall K8s

```
kubectl delete all --all-namespaces --all
```

```
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube*
```

```
sudo apt-get autoremove
```

```
sudo rm -rf ~/.kube
```

```
sudo rm -rf /etc/cni
```
```
sudo rm -rf /etc/kubernetes
```
```
sudo rm -rf /var/lib/etcd
```
```
sudo rm -rf /var/lib/kubelet
```

```
sudo iptables -t filter -F
sudo iptables -t filter -X
sudo systemctl restart docker
```
