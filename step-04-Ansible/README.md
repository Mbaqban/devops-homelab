# Use ansible to config k8s cluster



## Install ansible

```bash
sudo apt update
sudo apt install -y ansible sshpass
ansible --version
```
```
output :


ansible [core 2.19.11]
  config file = None
  configured module search path = ['/home/mbaqban/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/mbaqban/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.13.5 (main, Aug 10 2026, 12:06:59) [GCC 14.2.0] (/usr/bin/python3)
  jinja version = 3.1.6
  pyyaml version = 6.0.2 (with libyaml v0.2.5)
```

## Install Kubectl and connect to cluster
I used this [Doc](https://v1-32.docs.kubernetes.io/docs/tasks/tools) to install kubectl and prefer install using [native package management](https://v1-32.docs.kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management)



```bash
cd ../vagrant/cluster

vagrant ssh k8s-master -c "sudo cat /etc/kubernetes/admin.conf" > ~/.kube/config

kubectl get pods -A
```

```
output: 

NAMESPACE      NAME                                  READY   STATUS              RESTARTS   AGE
kube-flannel   kube-flannel-ds-47dnh                 1/1     Running             0          19m
kube-flannel   kube-flannel-ds-t8zp2                 1/1     Running             0          19m
kube-flannel   kube-flannel-ds-x5znc                 1/1     Running             0          20m
kube-system    coredns-589f44dc88-4b8kb              0/1     ContainerCreating   0          21m
kube-system    coredns-589f44dc88-5kbnb              0/1     ContainerCreating   0          21m
kube-system    etcd-k8s-master                       1/1     Running             2          22m
kube-system    etcd-k8s-master2                      1/1     Running             2          18m
kube-system    kube-apiserver-k8s-master             1/1     Running             2          22m
kube-system    kube-apiserver-k8s-master2            1/1     Running             4          18m
kube-system    kube-controller-manager-k8s-master    1/1     Running             6          22m
kube-system    kube-controller-manager-k8s-master2   1/1     Running             7          18m
kube-system    kube-proxy-4s9fq                      1/1     Running             0          21m
kube-system    kube-proxy-v98qt                      1/1     Running             0          19m
kube-system    kube-proxy-xgjq2                      1/1     Running             0          19m
kube-system    kube-scheduler-k8s-master             1/1     Running             5          22m
kube-system    kube-scheduler-k8s-master2            1/1     Running             5          18m
kube-system    kube-vip-k8s-master                   1/1     Running             13         21m
kube-system    kube-vip-k8s-master2                  1/1     Running             13         18m
```