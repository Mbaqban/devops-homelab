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

## 