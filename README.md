# DevOps Homelab

This project is the evolution of my previous Kubernetes homelab.

The goal is to build a reproducible infrastructure from bare metal
and understand how the components interact, fail, and are troubleshot
in a real infrastructure environment.

## Roadmap

* [x] **Step 1 — Debian 13 Host**</br>
  Prepare the Debian 13 host system as the foundation for the homelab.

* [x] **[Step 2 — KVM / QEMU / libvirt](./step-02-kvm/README.md)**</br>
  Set up virtualization with KVM, QEMU, and libvirt for running the lab VMs.

* [ ] **[Step 3 — Vagrant or Terraform](./step-03-Iac/README.md)**</br>
  Automate VM creation and infrastructure provisioning using Infrastructure as Code.

* [ ] **[Step 4 — Kubernetes with kubeadm](./step-04-Ansible/README.md)**</br>
  Use Ansible to configure the prepared Kubernetes nodes and automate cluster initialization with kubeadm.
  
* [ ] **Step 5 — GitOps / Argo CD**</br>
  Implement GitOps with Argo CD to continuously synchronize Kubernetes resources from Git.
