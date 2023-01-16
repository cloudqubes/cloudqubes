multipass launch --cpus 8 --mem 16384M --disk 25G file:///home/dialog/focal-server-cloudimg-amd64-disk-kvm.img --cloud-init k8s_vm.yml -n k8s-11



sudo snap set system proxy.http="http://127.0.0.1:8001"
sudo snap set system proxy.https="http://127.0.0.1:8001"


sudo snap get system proxy.http
sudo snap get system proxy.https


sudo snap unset system proxy.http
sudo snap unset system proxy.https

sudo snap install microk8s --classic --channel=1.25/stable

export https_proxy=http://127.0.0.1:8001
export http_proxy=http://127.0.0.1:8001
export no_proxy=10.0.0.0/8,192.168.0.0/16,127.0.0.1,172.16.0.0/16


sudo systemctl set-environment https_proxy=http://127.0.0.1:8001
sudo systemctl set-environment http_proxy=http://127.0.0.1:8001
sudo systemctl set-environment no_proxy=10.0.0.0/8,192.168.0.0/16,127.0.0.1,172.16.0.0/16