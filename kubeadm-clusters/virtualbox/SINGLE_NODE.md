# Single node cluster

## control plane

POD_CIDR=10.244.0.0/16
SERVICE_CIDR=10.96.0.0/16

kubeadm init --pod-network-cidr $POD_CIDR --service-cidr $SERVICE_CIDR --apiserver-advertise-address $PRIMARY_IP

kubectl --kubeconfig /etc/kubernetes/admin.conf \
    apply -f "https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s-1.11.yaml"

# ./bashrc setup

mkdir ~/.kube
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
chmod 600 ~/.kube/config

cat <<EOF >> ~/.bashrc
source <(kubectl completion bash)
alias k=kubectl
complete -F __start_kubectl k
EOF
source ~/.bashrc

## remote taint from master

kubectl taint nodes --all node-role.kubernetes.io/control-plane-

