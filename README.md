# Rough notes

flux bootstrap github   --token-auth   --owner=arisfkiaras   --repository=flux-learning   --branch=main   --path=clusters/my-cluster   --personal   --private

kubectl port-forward -n grafana service/prometheus-server 3000:80
kubectl port-forward -n prometheus service/prometheus-server 3000:80

Tested with:
kind
minikube

Will probably work on rancher since kind is using the rancher storage provisioner.
Will probably work with cloud since they give you a default storage class.
Will probably not work with vanilla cluster setup with kubeadm.


Todo:
- create script that will check token, check if storageclss is enabled, run flux bootstrap, print instruction on how to connect
- Clean up. Move structure from my cluster to demo


Nice to haves:
- Install ingress so its easier through one proxy to access all the apps.
- Add a few dashboards, provide better default grafana setup so user ends up directlyon dashboards.
- Add an nginx demo app and collect nginx metrics
