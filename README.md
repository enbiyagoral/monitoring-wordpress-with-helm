# Monitoring-Wordpress-with-Helm
This guide explains how to set up a WordPress instance and monitor it using Prometheus and Grafana on a Kubernetes cluster.

## Prerequisites
- Kubernetes cluster
- Helm installed
- Minikube or any other Kubernetes cluster

## Setup Instructions
Create Namespaces:
```bash
kubectl create namespace wordpress
kubectl create namespace monitoring
```
Add Helm Repositories:
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
Deploy WordPress using Helm:
```bash
helm upgrade --install wordpress bitnami/wordpress -f wordpress-values.yaml -n wordpress
```

Update the grafana-values.yaml file to set the WordPress service URL:
```bash
- targets:
    - http://<YOUR_WORDPRESS_SERVICE_URL>
```

Deploy the Prometheus Blackbox Exporter and Grafana using Helm:
```bash
helm upgrade --install prometheus-blackbox prometheus-community/prometheus-blackbox-exporter -f black-values.yaml -n monitoring

helm upgrade --install grafana prometheus-community/kube-prometheus-stack -f grafana-values.yaml -n monitoring
```

Forward ports to access Grafana and Prometheus:
```bash
kubectl --namespace monitoring port-forward svc/grafana-kube-prometheus-st-prometheus 9090
kubectl --namespace monitoring port-forward svc/grafana-kube-prometheus-st-alertmanager 9093
kubectl --namespace monitoring port-forward svc/grafana 8080:80
```

Access Grafana:
- Grafana can be accessed at http://localhost:8080.
- Username: admin
- Password: prom-operator

Notes
Ensure that the Minikube service URL for WordPress is correctly updated in the grafana-values.yaml file.
Adjust port-forwarding if necessary to fit your environment.