# Use vagrant OR terraform for Iac

we can use vagrant or terraform for creating or machiens on KVM.

> [!note]
> The goal is to create a single **Debian 13 Linux base image** containing all the basic dependencies required for Kubernetes.

We install **kubeadm, kubelet, and kubectl** using the same [version](https://v1-36.docs.kubernetes.io/docs/) (v1.36) on the base machine. Once the image is ready, it can be used to quickly create new Kubernetes nodes with the required packages already installed.

This allows us to use the same prepared image for the control-plane and worker nodes, reducing installation time and ensuring that all nodes start with a consistent Kubernetes environment.



- [Vagrant](./vagrant/README.md)
- [Terraform]()