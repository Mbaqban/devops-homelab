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


- We have this for now 

```bash 
virsh list --all
virsh net-list --all
kvm-ok


 Id   Name   State
-----n 13
    │
    └── KVM / QEMU
            │
         libvirt
            │
      default NAT network

```---------------

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
