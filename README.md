### Prometheus Install:
https://github.com/prometheus-community/helm-charts/ - helm chart
https://www.youtube.com/watch?v=n6kS5R6jKuk - steps

1. `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts` - add the repository
2. `helm search repo prometheus-community` - to see the charts
3. `helm repo update` - same as "apt-get update"
4. `helm pull prometheus-community/prometheus` - download prometheus-25.0.0.tgz
5. `tar zxf prometheus-25.0.0.tgz` - unzip
6. `rm prometheus-25.0.0.tgz` - delete file
7. `cp prometheus/values.yaml prometheus-values.yaml` - copy file
8. `helm upgrade --install --create-namespace --values prometheus-values.yaml prometheus -n monitoring prometheus-community/prometheus` - install prometheus with creating namespace

### Prometheus Check:
1. `kubectl get pods -n monitoring` - monitoring pods
2. `kubectl get services -n monitoring` - monitoring services
3. `docker exec -it minikube /bin/sh` + `docker ps` - list monitoring containers
4. `kubectl get pvc -n monitoring` - CAPACITY 8Gi
5. `kubectl get pv -n monitoring` - 

### Grafana Install:
1. `helm repo add grafana https://grafana.github.io/helm-charts` - 
2. `helm repo update` - same as "apt-get update"
3. `helm pull grafana/grafana` - donwload grafana-6.60.1.tgz
4. `tar zxf grafana-6.60.1.tgz` - unzip
5. `rm grafana-6.60.1.tgz` - delete file
6. `cp grafana/values.yaml grafana-values.yaml` - copy file
7. `helm upgrade --install --create-namespace --values grafana-values.yaml grafana -n monitoring grafana/grafana` - install grafana with creating namespace
    1. `kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo` - Get your 'admin' user password
        1. `QnTLKSwkBERRrvl3RI3XCii9J5ml8g9jyPBmOgiu` - password
        2. `admin` - login

### Grafana Check:
1. `kubectl get pods -n monitoring` - monitoring pods
2. `kubectl get services -n monitoring` - monitoring services
3. `docker exec -it minikube /bin/sh` + `docker ps` - list monitoring containers
4. `kubectl get pvc -n monitoring` - CAPACITY 8Gi
5. `kubectl get pv -n monitoring` - 