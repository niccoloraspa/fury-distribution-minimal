# Fury Distribution Minimal

A minimimal deployment of the [Fury Kubernetes Distribution](https://github.com/sighupio/fury-distribution)

## Tools required

- kustomize `>= 3.6`
- furyctl `>= 0.4.0`
- minikube `>= v1.18`

## Modules

The following modules of the distribution are deployed:

| Module                                                                    | Version                                  |
| -------------                                                             | -------------                            |
| [Fury Monitoring](https://github.com/sighupio/fury-kubernetes-monitoring) | d4ac34771b33611490d8c64cb458cabe874c2956 |
| [Fury Logging](https://github.com/sighupio/fury-kubernetes-logging)       | v1.7.1                                   |
| [Fury Ingress](https://github.com/sighupio/fury-kubernetes-ingress)       | d34f8228dc8b108526f8dbfbcf598c74f842832e |

You change the version by modifying the `Furyfile.yml` and then update the vendor folder via `furyctl vendor -H`

## Start minikube cluster

At the root level of this repository execute:

```bash
export REPO_DIR=$(PWD)
```

Start a minikube cluster:

```bash
cd $REPO_DIR/minikube
make setup
```

By default it will spin up a one node Kubernetes cluster of version `v1.19.4` in virtualbox VM (CPUs=4, Memory=4096MB, Disk=20000MB).

``
You can pass addition parameters to change the default values:

```bash
make setup cpu=2 memory=2048
```

Remember to adjust resources requests and limits accordingly if you reduce the `memory` and `cpu` of the node.

Please referer to this [Makefile](minikube/Makefile) for additional details.

## Deploy Fury

```bash
cd $REPO_DIR/fury
make fury
```

You might see some errors. You need to wait for some CRD to be created and then reapply the manifests:

```bash
# wait 10s 
make fury
```

## Explore the distribution

Use `minikube ip to` get the external IP

```shell-session
$ minikube ip     
192.168.99.113
```

Add ingresses to `/etc/hosts` file by adding the following line to the bottom of the file:

```bash
192.168.99.113 hello-world.info directory.fury.info alertmanager.fury.info goldpinger.fury.info grafana.fury.info prometheus.fury.info >> /etc/hosts
```

Replace `192.168.99.113` with your actual IP.
If you are adventurous enough you can do:

```bash
sudo bash -c "echo $(minikube ip) hello-world.info directory.fury.info alertmanager.fury.info goldpinger.fury.info grafana.fury.info prometheus.fury.info >> /etc/hosts"
```

## Cleanup

To clean up just delete the `minikube` cluster:

```bash
cd $REPO_DIR/minikube
make delete
```

Delete the line you added in `/etc/hosts`.
