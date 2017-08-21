# k8s-galaxy on OpenStack

customised way to deploy k8s-galaxy on top of a inline provisioned Kubernetes cluster on top of an OpenStack cloud.

  * `ansible`
  * `kubeadm`
  * `weave-net`
  * `k8s-galaxy`

Ansible to provision the cloud machines, kubeadm to get the cluster running, networking with weave, helming k8s-galaxy

## Prerequisites & TODOs
* k8s-galaxy development files must be provided to ansible pre zipped at fixed external location 
* still needs the helm chart locally, not yet deployed
* pvc provisioning is not solved through helm

### References

  * https://kubernetes.io/docs/getting-started-guides/kubeadm/
  * https://www.weave.works/docs/net/latest/kube-addon/
  * https://github.com/kubernetes/dashboard#kubernetes-dashboard
  * François Deppierraz' : https://github.com/infraly/k8s-on-openstack
  * mantl: https://github.com/mantl/mantl
  * http://docs.ansible.com/ansible/latest/os_server_module.html
  * Pablo Moreno's : https://github.com/pcm32/galaxy-helm-charts
