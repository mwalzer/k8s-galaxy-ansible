
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
sudo yum remove kubelet -y
sudo yum install kubelet-1.7.3-1.x86_64 -y
sudo yum install kubeadm-1.7.3-1.x86_64 -y
sudo systemctl daemon-reload
sudo systemctl restart kubelet
sudo systemctl status kubelet
sudo kubeadm reset
sudo kubeadm init
sudo kubelet log
sudo reboot
sudo kubelet log
sudo yum update -y
sudo reboot
sudo systemctl status kubelet
sudo kubelet log
sudo su
echo -e "server=8.8.8.8\nserver=8.8.4.4" > /etc/dnsmasq.conf
echo -e "$(hostname -i)\tlocalhost.$(hostname -d)" >> /etc/hosts
echo "1" > /proc/sys/net/bridge/bridge-nf-call-iptables
systemctl restart dnsmasq
sudo systemctl status kubelet
sudo less /etc/resolv.conf 
sudo less /etc/dnsmasq.conf 
sudo less /etc/hosts
sudo kubeadm init
sudo kubeadm reset
sudo kubeadm init
```
this does it without reboot
``` 
sudo yum remove kubelet -y
sudo yum install kubelet-1.7.3-1.x86_64 -y
sudo yum install kubeadm-1.7.3-1.x86_64 -y
sudo systemctl daemon-reload
sudo systemctl restart kubelet
sudo systemctl status kubelet
sudo yum update -y
sudo systemctl status kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet
sudo systemctl status kubelet
sudo kubeadm reset
sudo kubeadm init
```
the above is neccessary for the nodes too!
also, the kubelet takes some time to get the pod ready

Updating:
 kubeadm                   x86_64                   1.7.4-0                     Kubernetes                   8.6 M
 kubelet                   x86_64                   1.7.4-0                     Kubernetes                    17 M

NB: intially(1.7.4)  kubelet is
kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/etc/systemd/system/kubelet.service; enabled; vendor preset: disabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: activating (auto-restart) (Result: exit-code) since Sun 2017-08-27 16:00:18 UTC; 9s ago
     Docs: http://kubernetes.io/docs/
  Process: 10920 ExecStart=/usr/bin/kubelet --cloud-provider=openstack --cloud-config=/etc/kubernetes/cloud-config $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_NETWORK_ARGS $KUBELET_DNS_ARGS $KUBELET_EXTRA_ARGS (code=exited, status=1/FAILURE)
 Main PID: 10920 (code=exited, status=1/FAILURE)


## weave installed?
```
export kubever=$(kubectl version | base64 | tr -d '\n')  # yields usable result but ends with error state when no server present
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$kubever"
```

