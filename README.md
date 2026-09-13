This project is the evolution of my previous Kubernetes homelab. The first version focused on understanding Kubernetes workloads. This version focuses on building a reproducible infrastructure using Infrastructure as Code and configuration management.



Step 1 — Debian Host

Installed Debian 13 as the host operating system and allocated 500 GB of disk space for the new homelab infrastructure. This system will serve as the foundation for the virtualization and infrastructure layer.


Step 2 — Set up KVM/libvirt

- Check virtualization support:
```bash 
lscpu | grep -E 'Virtualization|vmx|svm'

Virtualization:           VT-x 

# also check kvm
ls -l /dev/kvm
crw-rw----+ 1 root kvm 10, 232 Sep 13 13:02 /dev/kvm
```

- then install utils 

```bash
apt update 
sudo apt install -y \
    qemu-system-x86 \
    qemu-utils \
    libvirt-daemon-system \
    libvirt-clients \
    virt-manager \
    bridge-utils
```

- Enable libvirt

```bash
sudo systemctl enable --now libvirtd

Synchronizing state of libvirtd.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable libvirtd
```

- Check

```bash
systemctl status libvirtd

● libvirtd.service - libvirt legacy monolithic daemon
     Loaded: loaded (/usr/lib/systemd/system/libvirtd.service; enabled; preset: enabled)
     Active: active (running) since Sun 2026-09-13 13:34:36 EDT; 58s ago
 Invocation: 56497e718f244410afcb145c1868a692
TriggeredBy: ● libvirtd-admin.socket
             ● libvirtd.socket
             ● libvirtd-ro.socket
       Docs: man:libvirtd(8)
             https://libvirt.org/
   Main PID: 22624 (libvirtd)
```

- Add your user to the groups

```bash
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
```

```bash
sudo reboot
```

- Check libvirt kvm group

```bash
groups

cdrom floppy sudo audio dip video plugdev users netdev scanner bluetooth lpadmin >> libvirt kvm <<<
```

- Check list of VMs

```bash
virsh list --all

 Id   Name   State
--------------------
```

no virtual machines yet


- Check the default network

```bash
sudo virsh net-list --all

Name   State   Autostart   Persistent
----------------------------------------
```

if *default* not there active it


```bash
sudo virsh net-start default

Network default started

# then make it auto start
sudo virsh net-autostart default

Network default marked as autostarted

sudo virsh net-list --all

Name      State    Autostart   Persistent
--------------------------------------------
default   active   yes         yes
```


- Check KVM acceleration

```bash
sudo apt install -y cpu-checker

sudo kvm-ok

INFO: /dev/kvm exists
KVM acceleration can be used
```


- In the end of step 2 we have 

```bash 
virsh list --all
virsh net-list --all
kvm-ok


 Id   Name   State
--------------------

 Name      State    Autostart   Persistent
--------------------------------------------
 default   active   yes         yes

INFO: /dev/kvm exists
KVM acceleration can be used
```

```
Debian 13
    │
    └── KVM / QEMU
            │
         libvirt
            │
      default NAT network

```