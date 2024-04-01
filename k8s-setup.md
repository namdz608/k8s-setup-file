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
printf "overlay\nbr_netfilter\n" >> /etc/modules-load.d/containerd.conf
modprobe overlay
modprobe br_netfilter
```

```
printf "net.bridge.bridge-nf-call-iptables = 1\nnet.ipv4.ip_forward = 1\nnet.bridge.bridge-nf-call-ip6tables = 1\n" >> /etc/sysctl.d/99-kubernetes-cri.conf
```

```
sudo sysctl --system
```

```
wget https://github.com/containerd/containerd/releases/download/v1.7.13/containerd-1.7.13-linux-amd64.tar.gz -P /tmp/
tar Cxzvf /usr/local /tmp/containerd-1.7.13-linux-amd64.tar.gz
```

```
wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -P /etc/systemd/system/
```

```
systemctl daemon-reload
systemctl enable --now containerd
```

```
wget https://github.com/opencontainers/runc/releases/download/v1.1.12/runc.amd64 -P /tmp/
install -m 755 /tmp/runc.amd64 /usr/local/sbin/runc
```

```
wget https://github.com/containernetworking/plugins/releases/download/v1.4.0/cni-plugins-linux-amd64-v1.4.0.tgz -P /tmp/
mkdir -p /opt/cni/bin
tar Cxzvf /opt/cni/bin /tmp/cni-plugins-linux-amd64-v1.4.0.tgz
```

```
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml
```

Tim va sua SystemdCgroup tu false ==> true
```
vi /etc/containerd/config.toml
```

```
systemctl restart containerd
sudo swapoff -a
```

```
apt-get install -y apt-transport-https ca-certificates curl gpg
mkdir -p -m 755 /etc/apt/keyrings
```

```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

```
apt-get update
```
```
apt-get install -y kubelet=1.29.1-1.1 kubeadm=1.29.1-1.1 kubectl=1.29.1-1.1
apt-mark hold kubelet kubeadm kubectl
```

## master
```
kubeadm init --pod-network-cidr 10.10.0.0/16 --kubernetes-version 1.29.1 --node-name k8s-control
```

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
// tạo lại token thì làm lại 3 bước trên
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
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/tigera-operator.yaml
wget https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/custom-resources.yaml
```

Custom custom-resource.yaml ==>> fix cidr: theo ip mang ma kubeadm init --pod-network-cidr 10.10.0.0/16
```
vi custom-resources.yaml
```

```
kubectl apply -f custom-resources.yaml
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
