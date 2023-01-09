


update apt 
$ sudo apt update

wget https://github.com/containerd/containerd/releases/download/v1.6.15/containerd-1.6.15-linux-amd64.tar.gz
wget https://github.com/containerd/containerd/releases/download/v1.6.15/containerd-1.6.15-linux-amd64.tar.gz.sha256sum

sha256sum containerd-1.6.15-linux-amd64.tar.gz
cat containerd-1.6.15-linux-amd64.tar.gz.sha256sum 

sudo tar Cxzvf /usr/local containerd-1.6.15-linux-amd64.tar.gz 

# running as a deamon

wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service

sudo mkdir /usr/local/lib/systemd
sudo mkdir /usr/local/lib/systemd/system

sudo cp containerd.service /usr/local/lib/systemd/system
sudo systemctl daemon-reload
sudo systemctl enable --now containerd

# install runc

wget https://github.com/opencontainers/runc/releases/download/v1.1.4/runc.amd64


# CNI
wget https://github.com/containernetworking/plugins/releases/download/v1.1.1/cni-plugins-linux-amd64-v1.1.1.tgz

sudo mkdir -p /opt/cni/bin
sudo tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.1.1.tgz

# IPV4 forwarding and let ip tables see bridged traffic

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

# generate the toml file

containerd config default > /etc/containerd/config.toml

# Cgroups

Update /etc/containerd/config.toml.

[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true

restart containerd.
sudo systemctl restart containerd.service

# Install kubeadm
sudo apt-get install -y apt-transport-https ca-certificates curl

mkdir -p /etc/apt/keyrings/
sudo curl -x http://127.0.0.1:8001 -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

multipass launch --cpus 1 --mem 1024M --disk 10G file:///home/indika/Downloads/focal-server-cloudimg-amd64.img -n kube


# initialize cluster

sudo systemctl set-environment HTTP_PROXY=127.0.0.1:8001
sudo systemctl set-environment HTTPS_PROXY=127.0.0.1:8001
sudo systemctl restart containerd.service