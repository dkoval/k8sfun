# k8sfun

## Overview

Spin up home media server by running essential applications in a containerized [k3s](https://github.com/rancher/k3s) cluster. 

## Install k3d

[k3d](https://github.com/rancher/k3d) is made for running a k3s cluster in Docker. Follow through https://github.com/rancher/k3d#get.

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