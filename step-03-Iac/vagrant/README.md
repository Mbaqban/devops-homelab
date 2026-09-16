# Use vagrant


### First install dependencies and vagrant it self.



```bash

sudo apt update
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst


sudo virt-host-validate
```

#### install vagrant from hashicorp
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

# install kvm vagrant plugins

```bash
-> sudo apt install -y ruby-libvirt build-essential libxml2-dev libxslt1-dev zlib1g-dev

-> vagrant plugin install vagrant-libvirt

-> vagrant plugin list                                                                 

vagrant-libvirt (0.12.2, global)
```



### If you wanna test it.

```bash
mkdir test-vm && cd test-vm
vagrant init debian/trixie64
vagrant up --provider=libvirt
```


### Makefile for machines

file is in the this dir