---
layout: post
title:  "K8s安装国内源"
date:   2019-06-27 16:11:36 +0800
categories: 计算机
---
# 安装kubelet kubeadm kubectl
* 官方安装
  
        apt-get update && apt-get install -y apt-transport-https curl
        curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
        cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
        deb https://apt.kubernetes.io/ kubernetes-xenial main
        EOF
        apt-get update
        apt-get install -y kubelet kubeadm kubectl
        apt-mark hold kubelet kubeadm kubectl

    *目前只有Ubuntu16.04的源*

* 国内安装

        apt-get update && apt-get install -y apt-transport-https curl
        curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
        cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
        deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
        EOF 
        apt-get update
        apt-get install -y kubelet kubeadm kubectl
        apt-mark hold kubelet kubeadm kubectl

    *目前只有Ubuntu16.04的源*
# 设置Container runtimes
    # Setup docker daemon.
    cat > /etc/docker/daemon.json <<EOF
    {
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "100m"
        },
        "storage-driver": "overlay2"
    }
    EOF

    mkdir -p /etc/systemd/system/docker.service.d

    # Restart docker.
    systemctl daemon-reload
    systemctl enable docker.service
    systemctl restart docker

# 关闭Swap的设备
    swapoff -a

# 下载docker镜像
* gcr官方镜像（被墙）
  
        docker pull k8s.gcr.io/kube-apiserver:v1.15.0 
        docker pull k8s.gcr.io/kube-controller-manager:v1.15.0 
        docker pull k8s.gcr.io/kube-scheduler:v1.15.0 
        docker pull k8s.gcr.io/kube-proxy:v1.15.0 
        docker pull k8s.gcr.io/pause:3.1 
        docker pull k8s.gcr.io/etcd:3.3.10 
        docker pull k8s.gcr.io/coredns:1.3.1

* 采用docker官方镜像，之后tag改名
  
        docker pull mirrorgooglecontainers/kube-apiserver:v1.15.0 
        docker pull mirrorgooglecontainers/kube-controller-manager:v1.15.0 
        docker pull mirrorgooglecontainers/kube-scheduler:v1.15.0 
        docker pull mirrorgooglecontainers/kube-proxy:v1.15.0 
        docker pull mirrorgooglecontainers/pause:3.1 
        docker pull mirrorgooglecontainers/etcd:3.3.10 
        docker pull coredns/coredns:1.3.1

# 使用 kubeadm 创建一个单主集群
    kubeadm init <args>
# 执行以下命令
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
# 添加节点加入集群
    kubeadm join <master ipaddress>:6443 --token <token key> \
    --discovery-token-ca-cert-hash sha256:<ca-hash key>

* _token key_：
  
        kubeadm token create
* _ca-hash key_： 

        openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //' 

