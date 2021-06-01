# k8sfun

## Overview

Spin up home media server by running essential applications in a containerized [k3s](https://github.com/rancher/k3s) cluster. 

## Install k3d

[k3d](https://github.com/rancher/k3d) is made for running a K3s cluster in Docker. Follow through https://github.com/rancher/k3d#get.

## Create a new k3d cluster

```shell
k3d cluster create k8sfun -p 80:80@loadbalancer -p 443:443@loadbalancer -v /path/to/volumes:/mnt/volumes -v $HOME/.config/:/mnt/config
```
