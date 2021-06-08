# k8sfun

Spin up a home media server by running essential applications in a containerized [k3s](https://github.com/rancher/k3s) cluster. 

Table of Contents

- [Prerequisites](#prerequisites)
- [Install tools](#install-tools)
- [Create a new k3d cluster](#create-a-new-k3d-cluster)
- [Almost there](#almost-there)


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
| [helm v3](https://helm.sh) is the package manager for k8s | [Install helm](https://helm.sh/docs/intro/install) |

## Create a new k3d cluster

```shell
k3d cluster create k8sfun \
--api-port 6443 \
-p 80:80@loadbalancer \
-p 443:443@loadbalancer \
-v /path/to/k3s/manifests:/var/lib/rancher/k3s/server/manifests \
-v /path/to/k3s/storage:/var/lib/rancher/k3s/storage \
-v /path/to/volumes:/mnt/volumes \
-v $HOME/.config/:/mnt/config \
--k3s-server-arg "--no-deploy=traefik"
```

The above command performs the following:

- Creates a new k3s single-node k3s cluster named `k8sfun`.
- Configures k3s's API-Server to listen on port `6443` with that port mapped to the host machine.
- Publishes ports `80` and `443` to the host machine and maps them to the k3s virtual loadbalancer so that we can send external web traffic (`http` and `https`) to the cluster.
- Mounts `/path/to/k3s/manifests` directory on the host machine mounts to `/var/lib/rancher/k3s/server/manifests` so that k8s manifest files can be dropped in here for automatic deployment.
- Mounts `/path/to/k3s/storage` directory from the host machine to `/var/lib/rancher/k3s/storage` as this is the default directory k3s stores data in. We can create k8s [Persistent Volume Claims](https://kubernetes.io/docs/concepts/storage/persistent-volumes) and they will be created here on the host machine.
- `--k3s-server-arg` argument passes `--no-deploy=traefik` flag to k3s when the cluster is created preventing the default Traefik 1.x ingress controller from being installed. We are going install Traefik Proxy v2, which is a huge improvement over Traefik v1, next.

Install Traefik Proxy v2 packaged as a Helm [chart](https://containous.github.io/traefik-helm-chart).

```shell
helm repo add traefik https://containous.github.io/traefik-helm-chart
helm repo update
helm install traefik traefik/traefik
```

## Almost there

### How do I make Traefik v2 Ingress controller work with `/sonarr` subpath?

Set URL Base to `/sonarr` in Sonarr by editting `$HOME/.config/sonarr/config.xml` file.

```xml
<Config>
  ...
  <UrlBase>/sonarr</UrlBase>
  ...
</Config>
```

Apply changes by doing rolling restart of `sonarr` deployment.

```shell
kubectl rollout restart deploy sonarr
```

### How do I make Traefik v2 Ingress controller work with `/jackett` subpath?

Set URL Base to `/jackett` in Jackett by editting `$HOME/.config/Jackett/ServerConfig.json` file.

```json
{
  ...
  "BasePathOverride": "/jackett",
  ...
}
```

Apply changes by doing rolling restart of `jackett` deployment.

```shell
kubectl rollout restart deploy jackett
```