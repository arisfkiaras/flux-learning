# Flux Learning - Provision Kubernetes Monitoring Stack


This repository contains [Flux](https://fluxcd.io) configuration to bootstrap a fresh Kubernetes cluster with a basic monitoring and observability stack.

It provisions:
- **Loki** for logs storage
- **Vector** to collect and send pod logs to Loki
- **Prometheus** with exporters to collect and store metrics
- **Grafana** for visualization

> **For learning purposes only. Should not be used in production.**


## Prerequisites

Access to a kubernetes cluster with a default storage class. (example: [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/))
Installed flux cli. https://fluxcd.io/flux/installation/


Have tested this setup with:
- kind
- minikube

## How to bootstrap a cluster

**Generate a GitHub Personal Access Token**
  - Go to [https://github.com/settings/tokens](https://github.com/settings/tokens)
  - Select **repo** scope
  - Click Generate token at buttom of the page

**Configure token as an environment variable**

```bash
export GITHUB_TOKEN=your_token...
```

**Call flux to bootstrap the cluster**

```bash
flux bootstrap github \
    --token-auth  \
    --owner=arisfkiaras  \
    --repository=flux-learning  \
    --branch=main  \
    --path=clusters/my-cluster \
    --personal
```

**Wait for flux**
- If everything was installed correctly you should see 
```bash
✔ all components are healthy
```

- Execute ```kubectl get pods -A``` to see the status of individual pods. 

## How to access Grafana

***Forward the Grafana service to your local machine***

```bash
kubectl port-forward -n prometheus service/prometheus-server 3000:80
```

***Then open http://localhost:3000 in your browser.***

Grafana default credentials:
```
user: demo
pass: demo
```

You can browse the pre-installed dashboards or use the explore functionality of grafana, or create yoour own dashbaords.

## Repo structure


## Vector

A custom DaemonSet was created to deploy vector [apps/vector/daemon-set.yaml](./apps/vector/daemon-set.yaml)

Sources and sinks are configured in a Config Map: [apps/vector/config-map.yaml](./apps/vector/config-map.yaml)

The setup was inspired by [Vector Helm charts](https://github.com/vectordotdev/helm-charts/blob/develop/charts/vector)


### Useful links

- [Kubernetes Logs Source Documentation](https://vector.dev/docs/reference/configuration/sources/kubernetes_logs/)
- [Loki Sink Documentation](https://vector.dev/docs/reference/configuration/sinks/loki/)

## Loki

Loki was installed using the [Loki Helm chart](https://github.com/grafana/loki/blob/main/production/helm/loki) by Grafana. 

The Monolithic - Single Repilca setup was chosen.

[MinIO](https://min.io/) is used as an S3-compatible object storage.

Helm Values can be configured on Loki HelmRelease manifest: [apps/loki/release.yaml](./apps/loki/release.yaml)

### Useful Links

- [Loki Helm Installation Guide](https://grafana.com/docs/loki/latest/setup/install/helm/)
- [Loki Helm Chart Repository](https://github.com/grafana/loki/tree/main/production/helm/loki)
- [Loki Helm Chart Default Values](https://github.com/grafana/loki/blob/main/production/helm/loki/values.yaml)


## prometheus
The chart by prometheus-community was used.
It installs prometheus, kube-state-metrics to colllect info like pods running
node_exporter to collect host metrics like memory usage.

To modify prometheus settings you can directly edit the values under release.yaml


## Grafana
Grafana was installed using the helm charts of grafana.
For easy oof use datasources are automatically configured during setup.
For easy of use two dashboards are created automatically

https://grafana.com/grafana/dashboards/ to find dashboards
The explore functioonality you can browse loki and prometheus data and create your own.

https://grafana.com/docs/grafana/latest/setup-grafana/installation/helm/

Future improvements:
- create script that will check token, check if storageclss is enabled, run flux bootstrap, print instruction on how to connect
- Clean up. Move structure from my cluster to demo
- Add overview of what is deployed, some screenshots of grafana
- Add list of services including upstream documentation links, how to configure and how to access.
- Add how to fix too many open files issue if it comes up.
Nice to haves:
- Install ingress so its easier through one proxy to access all the apps.
- Add a few dashboards, provide better default grafana setup so user ends up directlyon dashboards.
- Add an nginx demo app and collect nginx metrics
