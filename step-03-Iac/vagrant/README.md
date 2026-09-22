# First install dependencies and vagrant it self.

```bash
sudo apt update
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst

sudo virt-host-validate
```

## Install vagrant from hashicorp
```log
wget -O- https://apt.releases.hashicorp.com/gpg | \
  gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
  https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt install -y vagrant

vagrant --version
```
```
output:

Vagrant 2.4.9
```


## Install kvm vagrant plugins

```bash
sudo apt install -y ruby-libvirt build-essential libxml2-dev libxslt1-dev zlib1g-dev

vagrant plugin install vagrant-libvirt

vagrant plugin list                                                                 
```
```
output:

vagrant-libvirt (0.12.2, global)
```

> If you wanna test it.

```bash
# add box to archive to avoid download it every time
vagrant box add debian/trixie64

mkdir test-vm && cd test-vm
vagrant init debian/trixie64
vagrant up --provider=libvirt

# you can check status of machine via virsh command or virt GUI app.
virsh list --all
```


## Make a custom box in vagrant
```
to avoid download and install kubeadm and its dependencies
we will make the custom box that have all of them installed and ready to use.
```


### Run base image
> [!note]
> copy or write yourself this [dir/base/vagrantfile](https://github.com/Mbaqban/devops-homelab/blob/main/step-03-Iac/vagrant/base/vagrantfile)
you can do it better than me :)
```bash
mkdir base && cd base
nano vagrantfile

# after save the file 
vagrant up
```

# Install kubeadm on the box

> [!important]
> Using k8s [v1.36 Docs](https://v1-36.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)


> [!important]
> Following commands will run in box
```bash
vagrant ssh default
```

## Turn off swap
We should turn off the swap base on [Docs](https://v1-36.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#swap-configuration)

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab

free -h

              total        used        free      shared  
Swap:             0B          0B          0B
```

## Kernel modules & network settings

- to be sure that the setting will apply in boot we put them in files for next boots

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
sudo sysctl --system
```


## Install containerd

```bash 
sudo apt-get update
sudo apt-get install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

## Change cgroup driver 

```bash 
# By setting SystemdCgroup = true in containerd’s config, we make containerd use the same cgroup driver (systemd) as kubelet and the OS itself — keeping everything consistent and avoiding those conflicts.

nano /etc/containerd/config.toml
change -> SystemdCgroup = true


sudo systemctl restart containerd
sudo systemctl enable containerd
```


## Add Kubernetes repo

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.36/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.36/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet

# verfiy 
kubeadm version
kubectl version --client
```

```
output:

kubeadm version: &version.Info{Major:"1", Minor:"36", EmulationMajor:"", EmulationMinor:"", MinCompatibilityMajor:"", MinCompatibilityMinor:"", GitVersion:"v1.36.4", GitCommit:"bb826b1d48562f110659e64e8ec444327433db95", GitTreeState:"clean", BuildDate:"2026-08-20T03:08:41Z", GoVersion:"go1.26.5", Compiler:"gc", Platform:"linux/amd64"}

Client Version: v1.36.4

Kustomize Version: v5.8.1
```
## Clean up phase

```bash 
# Reset machine-id so each clone gets a unique one (otherwise DHCP/DNS and some cluster components can get confused):

truncate -s 0 /etc/machine-id
rm /var/lib/dbus/machine-id
ln -s /etc/machine-id /var/lib/dbus/machine-id

# Clear any cached kubeadm state (just in case you tested anything):
kubeadm reset -f
rm -rf /etc/kubernetes /var/lib/etcd

history -c
```

> [!note]
> Now we can logout from machine and take image of it :)
```bash
logout
```

> [!note]
> following commands are run in host machine

## Save the custom box

```bash
sudo apt-get update
sudo apt-get install -y libguestfs-tools
sudo chmod +r /boot/vmlinuz-*

# in dir of machine
vagrant halt

vagrant package --output k8s-base.box

vagrant box add k8s-base k8s-base.box --provider libvirt
vagrant box list
```
```
output:

debian/trixie64 (libvirt, 13.20260519.1, (amd64))
k8s-base        (libvirt, 0, (amd64))
```



# Use the prebuild box to create the cluster
```
We need 4 machines
one master and 3 workers 
```

## Create a network 

```
create file name k8s-net.xml
and put this in it 

<network>
  <name>k8s-net</name>
  <forward mode="nat"/>
  <bridge name="virbr-k8s" stp="on" delay="0"/>
  <ip address="192.168.56.1" netmask="255.255.255.0"/>
</network>
```

```bash
sudo virsh net-define k8s-net.xml
sudo virsh net-start k8s-net  
sudo virsh net-autostart k8s-net

sudo virsh net-list --all
```
```
output:

Name              State      Autostart   Persistent
------------------------------------------------------
default           active     yes         yes
k8s-net           active     yes         yes

```

> [!note]
> copy or write yourself this [dir/base/vagrantfile](https://github.com/Mbaqban/devops-homelab/blob/main/step-03-Iac/vagrant/cluster/vagrantfile) into a vagrantfile
you can do it better than me :)


```bash
vagrant up

virsh list --all
```
```
output:

Id   Name                  State
--------------------------------------
2    cluster_k8s-master    running
3    cluster_k8s-worker1   running
4    cluster_k8s-worker2   running
5    cluster_k8s-worker3   running
```
---

- directory 
```
.
├── base
│   ├── k8s-base.box
│   └── Vagrantfile
└── cluster
   ├── k8s-net.xml
   └── vagrantfile
```
- diagram
```
Debian 13
    │
    └── KVM / QEMU
            │
        libvirt
            │
      k8s-net NAT network
            │
            ├── cluster_k8s-master 
            ├── cluster_k8s-worker1
            ├── cluster_k8s-worker2
            └── cluster_k8s-worker3
```