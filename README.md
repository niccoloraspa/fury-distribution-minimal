# Fury Distribution Minimal

A minimal deployment of the [Fury Kubernetes Distribution](https://github.com/sighupio/fury-distribution) in a minikube environment.

## Tools required

- kustomize `>= 3.6`
- furyctl `>= 0.4.0`
- minikube `>= v1.18`

To install `furyctl` please refer to this [guide](https://github.com/sighupio/furyctl#install).

## Modules

The following modules of the distribution are deployed:

| Module                                                                    | Version                                  |
| -------------                                                             | -------------                            |
| [Fury Monitoring](https://github.com/sighupio/fury-kubernetes-monitoring) | v1.11.1                                  |
| [Fury Logging](https://github.com/sighupio/fury-kubernetes-logging)       | v1.7.1                                   |
| [Fury Ingress](https://github.com/sighupio/fury-kubernetes-ingress)       | d34f8228dc8b108526f8dbfbcf598c74f842832e |

You can change the versions by modifying the `Furyfile.yml` and then update the vendor folder via `furyctl vendor -H.`

### Packages Deployed

You can inspect the `Furyfile.yml` to see the list of the packages deployed.

#### Logging Packages

```shell-session
$ cat Furyfile.yml | grep logging
  logging: v1.7.1
  - name: logging/elasticsearch-single
  - name: logging/fluentd
  - name: logging/kibana
```

#### Monitoring Packages

```shell-session
$ cat Furyfile.yml | grep monitoring
  monitoring: v1.11.1
  - name: monitoring/prometheus-operator
  - name: monitoring/prometheus-operated
  - name: monitoring/alertmanager-operated
  - name: monitoring/node-exporter
  - name: monitoring/kube-state-metrics
  - name: monitoring/grafana
  - name: monitoring/goldpinger
  - name: monitoring/kubeadm-sm
  - name: monitoring/configs
```

#### Ingress Packages

```shell-session
$ cat Furyfile.yml | grep ingress
  ingress: d34f8228dc8b108526f8dbfbcf598c74f842832e
  - name: ingress/forecastle
  - name: ingress/nginx
```

## Start minikube cluster

At the root level of this repository, execute:

```bash
export REPO_DIR=$(PWD)
```

Start a minikube cluster:

```bash
cd $REPO_DIR/minikube
make setup
```

By default, it will spin up a one node Kubernetes cluster of version `v1.19.4` in a VirtualBox VM (CPUs=4, Memory=4096MB, Disk=20000MB).

You can pass additional parameters to change the default values:

```bash
make setup cpu=2 memory=2048
```

If you reduce the `memory` and `cpu` of the node, remember to adjust resources requests and limits accordingly.

Please referer to this [Makefile](minikube/Makefile) for additional details on the cluster creation.

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

Wait for all the pods to be up and running (it may take some time):

```bash
kubectl get pods -A -w
```

While you wait, use `minikube ip` to get the external IP of the cluster:

```shell-session
$ minikube ip     
192.168.99.113
```

Add ingresses to `/etc/hosts` file by adding the following line to the bottom of the file:

```bash
192.168.99.113 directory.fury.info alertmanager.fury.info goldpinger.fury.info grafana.fury.info prometheus.fury.info >> /etc/hosts
```

Replace `192.168.99.113` with your actual IP.
If you are adventurous enough, you might want to use this one-liner:

```bash
sudo bash -c "echo $(minikube ip) directory.fury.info alertmanager.fury.info goldpinger.fury.info grafana.fury.info prometheus.fury.info >> /etc/hosts"
```

## Explore the distribution

Open a browser and go to `directory.fury.info`. This is the forecastle page with all the ingresses. You can have a look around and:

- Inspect the logs in Kibana
- See the alerts in Alertmanager
- Explore the metrics in Prometheus
- Visualize the dashboards in Grafana

### Create Kibana index patterns

Kibana requires an index pattern to access the Elasticsearch data that you want to explore. An index pattern selects the data to use and allows you to define the properties of the fields.
We are going to create the following index patterns:

- `kubernetes-*`
- `system-`
- `nginx-ingress-controller-*`

```bash
# kubernetes-*
curl -X POST kibana.fury.info/api/saved_objects/index-pattern/kubernetes  -H 'kbn-xsrf: true' -H 'Content-Type: application/json' -d '
{
  "attributes": {
    "title": "kubernetes-*"
  }
}'

# system-*
curl -X POST kibana.fury.info/api/saved_objects/index-pattern/system  -H 'kbn-xsrf: true' -H 'Content-Type: application/json' -d '
{
  "attributes": {
    "title": "system-*"
  }
}'

# nginx-ingress-controller-*
curl -X POST kibana.fury.info/api/saved_objects/index-pattern/nginx-ingress-controller  -H 'kbn-xsrf: true' -H 'Content-Type: application/json' -d '
{
  "attributes": {
    "title": "nginx-ingress-controller-*"
  }
}'
```

## Cleanup

To clean up:

- delete the `minikube` cluster:

```bash
cd $REPO_DIR/minikube
make delete
```

- delete the line you added in `/etc/hosts.
