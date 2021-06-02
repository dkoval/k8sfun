# k8sfun

Spin up home media server by running essential applications in a containerized [k3s](https://github.com/rancher/k3s) cluster. 

Table of Contents

To be continued ...


## Prerequisites

- Docker
- k3d/k3s
- kubectl
- helm

## Install tools

| Tool | HOWTO |
| --- | --- |
| [Docker](https://docs.docker.com) is a platform for building, deploying and running containerized apps | [Install Docker](https://docs.docker.com/engine/install) |
| [k3s](https://k3s.io) is a lightweight CNCF certified k8s distribution, whilst [k3d](https://k3d.io) is made for running k3s clusters in Docker | [Install k3d](https://k3d.io/#installation) |
| [kubectl](https://kubernetes.io/docs/reference/kubectl) is a CLI tool for running commands against k8s clusters | [Install kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) |
| [helm](https://helm.sh) is the package manager for k8s | [Install helm](https://helm.sh/docs/intro/install) |

## Create a new k3d cluster

```shell
k3d cluster create k8sfun \
-p 80:80@loadbalancer \
-p 443:443@loadbalancer \
-v /path/to/volumes:/mnt/volumes \
-v $HOME/.config/:/mnt/config \
--api-port 6443 \
--k3s-server-arg "--no-deploy=traefik"
```

What's hust happened:

- `localhost` ports `80` and `443` map to the k3s virtual loadbalancer. This will allow us to reach the ingress resources directly from the `localhost` on the host machine.
- k3s's API-Server is configured to listen on port `6443` with that port mapped to the host system.
- the cluster is deployed without the default Traefik Ingress Controller.