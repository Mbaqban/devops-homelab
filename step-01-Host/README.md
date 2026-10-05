    # DevOps Homelab

    A personal DevOps homelab built to design, automate, operate, and document a production-inspired infrastructure environment on a single physical workstation.

    The project focuses on **Linux, virtualization, infrastructure as code, configuration management, Kubernetes, GitOps, containerization, monitoring, and high availability**.

    The goal is to continuously evolve the environment while keeping the infrastructure reproducible and documented.

    ---

    ## 🖥️ Physical Host

    The entire lab runs on a dedicated Debian workstation.

    | Component                 | Specification            |
    | ------------------------- | ------------------------ |
    | OS                        | Debian 13                |
    | CPU                       | Intel Core i5-13400      |
    | CPU Cores                 | 10 cores / 16 threads    |
    | RAM                       | 32 GB DDR5               |
    | Storage                   | 500 GB NVMe SSD          |
    | Virtualization            | KVM / QEMU               |
    | Virtualization Management | libvirt                  |
    | Container Runtime         | containerd               |

    The host provides the compute and storage resources for the virtualized infrastructure.

    ---

    ## 🚀 Application Deployment

    The lab includes a Django application called **Shiriniha**.

    The application container image is stored in the private Harbor registry.

    The intended deployment flow is:

    ```text
                        GitHub
                        │
                        │ GitOps manifests
                        ▼
                        Argo CD
                        │
                        │ Kubernetes manifests
                        ▼
                    Kubernetes Cluster
                        │
                        │ image pull
                        ▼
                        Harbor
                        │
                        ▼
                    Shiriniha Image
    ```

    Argo CD is responsible for maintaining the desired Kubernetes state defined in Git.

    ---

    ## 🛠️ Current Technologies

    | Category          | Technologies       |
    | ----------------- | ------------------ |
    | Host OS           | Debian 13          |
    | Virtualization    | KVM, QEMU, libvirt |
    | VM Provisioning   | Vagrant            |
    | IaC               | Terraform          |
    | Configuration     | Ansible            |
    | Containers        | containerd         |
    | Orchestration     | Kubernetes         |
    | Cluster Bootstrap | kubeadm            |
    | CNI               | Flannel            |
    | Kubernetes VIP    | kube-vip           |
    | GitOps            | Argo CD            |
    | Registry          | Harbor             |
    | Secrets           | SOPS + age         |
    | Application       | Django             |
    | Version Control   | Git / GitHub       |


    ---

    ## 🧪 Failure & Recovery Testing

    An important part of this project is not only deploying infrastructure, but also intentionally breaking it and recovering it.

    Planned scenarios include:

    * Control-plane failure
    * Worker-node failure
    * Kubernetes node recovery
    * Container runtime failure
    * Network failure
    * Application pod failure
    * Image pull failures
    * Certificate problems
    * Kubernetes upgrade/recovery procedures
    * Persistent workload recovery

    The purpose is to understand how the infrastructure behaves under failure rather than only testing successful deployments.

    ---

    ## 📚 Learning Objectives

    This project is intended to provide practical experience with:

    * Kubernetes
    * High availability
    * Infrastructure as Code
    * Configuration management
    * GitOps
    * Secrets management
    * Observability
    * Troubleshooting
    * Disaster recovery
    * Automation

    The lab is continuously evolving as new technologies and operational scenarios are added.

    ---

    ## 📖 Philosophy

    > **Build it. Break it. Automate it. Recover it. Document it.**

    This homelab is not intended to reproduce a production environment perfectly.

    Instead, it is a controlled environment for experimenting with production-oriented DevOps and SRE concepts, understanding failure modes, and turning manual infrastructure into reproducible automation.




    ## 🏗️ Architecture

    The current environment is built around a Kubernetes cluster running on virtual machines.

    ```text
    ┌─────────────────────────────────────────────────────────────┐
    │                    Physical Host                            │
    │                                                             │
    │  Debian 13                                                  │
    │  Intel i5-13400                                             │
    │  32 GB DDR5                                                 │
    │  1 TB NVMe                                                  │
    │                                                             │
    │  ┌───────────────────────────────────────────────────────┐  │
    │  │                  KVM / QEMU                           │  │
    │  │                  libvirt                              │  │
    │  │                                                       │  │
    │  │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐   │  │
    │  │   │  Master 1   │  │  Master 2   │  │  Master 3   │   │  │
    │  │   │  Kubernetes │  │  Kubernetes │  │  Kubernetes │   │  │
    │  │   └─────────────┘  └─────────────┘  └─────────────┘   │  │
    │  │                                                       │  │
    │  │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐   │  │
    │  │   │  Worker 1   │  │  Worker 2   │  │  Worker 3   │   │  │
    │  │   │  Kubernetes │  │  Kubernetes │  │  Kubernetes │   │  │
    │  │   └─────────────┘  └─────────────┘  └─────────────┘   │  │
    │  │                                                       │  │
    │  └───────────────────────────────────────────────────────┘  │
    │                                                             │
    └─────────────────────────────────────────────────────────────┘
    ```
