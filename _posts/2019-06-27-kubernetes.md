---
layout: post
title:  "K8s安装国内源"
date:   2019-06-27 16:11:36 +0800
categories: 计算机
---

# 官方安装

    apt-get update && apt-get install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubelet kubeadm kubectl
    apt-mark hold kubelet kubeadm kubectl

*目前只有Ubuntu16.04的源*

# 国内安装

    apt-get update && apt-get install -y apt-transport-https curl
    curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
    EOF 
    apt-get update
    apt-get install -y kubelet kubeadm kubectl
    apt-mark hold kubelet kubeadm kubectl

*目前只有Ubuntu16.04的源*    