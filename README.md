### Prometheus Install:
https://github.com/prometheus-community/helm-charts/

1. `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts` - add the repository
2. `helm search repo prometheus-community` - to see the charts
3. `helm repo update` - same as "apt-get update"
4. `helm pull prometheus-community/prometheus` - download prometheus-25.0.0.tgz
5. `tar zxf prometheus-25.0.0.tgz` - unzip
6. `rm prometheus-25.0.0.tgz` - delete file
7. `cp prometheus/values.yaml prometheus-values.yaml` - copy file
8. `helm upgrade --install --create-namespace --values prometheus-values.yaml prometheus -n monitoring prometheus-community/prometheus` - install prometheus with creating namespace
9. `kubectl get pods -n monitoring` - monitoring pods
10. `kubectl get services -n monitoring` - monitoring services
11. `docker exec -it minikube /bin/sh` + `docker ps` - list monitoring containers