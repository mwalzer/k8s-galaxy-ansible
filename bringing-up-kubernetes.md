
## SElinux disabled?
disabled needs reboot / permissive w/o reboot

## docker groups??
```
sudo groupadd docker
sudo usermod -aG docker $(whoami)
```

### docker cgroup issue!!!
```
sudo docker info |grep -i cgroup
sudo kubelet logs
```

## debug and trace down
* kubeadm reset
* sudo systemctl daemon-reload
* sudo systemctl restart kubelet
* journalctl
* sudo kubelet logs
* systemctl show --property=Environment kubelet | cat

## issue with installs on nodes!!! (maybe upgrade??) seems to be a network/repo issue
failed: [k8s-1] (item=[u'docker', u'kubelet', u'kubeadm', u'kubectl', u'kubernetes-cni', u'nfs-utils']) => {
    "failed": true, 
    "item": [
        "docker", 
        "kubelet", 
        "kubeadm", 
        "kubectl", 
        "kubernetes-cni", 
        "nfs-utils"
    ], 
    "module_stderr": "Shared connection to 192.168.2.88 closed.\r\n", 
    "module_stdout": "/usr/bin/python: symbol lookup error: /lib64/libnsssysinit.so: undefined symbol: PR_GetEnvSecure\r\n", 
    "msg": "MODULE FAILURE", 
    "rc": 0
}


## one of these did the trick!
```
sudo systemctl status kubelet
less 
systemctl show --property=Environment kubelet | cat
sudo yum remove kubelet
sudo yum install kubeadm-1.7.3-1.x86_64
sudo yum install kubelet-1.7.3-1.x86_64
sudo yum install kubeadm-1.7.3-1.x86_64
sudo systemctl daemon-reload
sudo systemctl restart kubelet
sudo systemctl status kubelet
sudo kubeadm reset
sudo kubeadm init
sudo kubelet log
sudo reboot
sudo kubelet log
sudo yum update
sudo reboot
sudo system status kubelet
sudo systemctl status kubelet
sudo systemctl status kubelet -l
sudo kubelet log
sudo su
echo -e "server=8.8.8.8\nserver=8.8.4.4" > /etc/dnsmasq.conf
echo -e "$(hostname -i)\tlocalhost.$(hostname -d)" >> /etc/hosts
systemctl restart dnsmasq
sudo kubelet log
sudo systemctl status kubelet -l
sudo less /etc/resolv.conf 
sudo less /etc/dnsmasq.conf 
sudo less /etc/host
sudo less /etc/hosts
kubeadm 
sudo kubeadm init
sudo kubeadm reset
sudo kubeadm init
```

## weave installed?
```
export kubever=$(kubectl version | base64 | tr -d '\n')  # yields usable result but ends with error state when no server present
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$kubever"
```

