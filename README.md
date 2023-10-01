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
5. `kubectl get pv -n monitoring`


### Grafana Install:
1. `helm repo add grafana https://grafana.github.io/helm-charts` - 
2. `helm repo update` - same as "apt-get update"
3. `helm pull grafana/grafana` - donwload grafana-6.60.1.tgz
4. `tar zxf grafana-6.60.1.tgz` - unzip
5. `rm grafana-6.60.1.tgz` - delete file
6. `cp grafana/values.yaml grafana-values.yaml` - copy file
7. `helm upgrade --install --create-namespace --values grafana-values.yaml grafana -n monitoring grafana/grafana` - install grafana with creating namespace
    1. `kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo` - Get your 'admin' user password
        1. `D5YOr5Ra2KDVcOkLv00uVTgw4tRpb6VaIcR9Y75y` - password
        2. `admin` - login

### Grafana Check:
1. `kubectl get pods -n monitoring` - monitoring pods
2. `kubectl get services -n monitoring` - monitoring services
3. `docker exec -it minikube /bin/sh` + `docker ps` - list monitoring containers
4. `kubectl get pvc -n monitoring` - CAPACITY 8Gi
5. `kubectl get pv -n monitoring`
6. ### `minikube service grafana -n monitoring` - open grafana service UI!


### Trickster Install:
1. `helm repo add tricksterproxy https://helm.tricksterproxy.io` - 
2. `helm repo update` - same as "apt-get update"
3. `helm pull tricksterproxy/trickster` - donwload trickster-1.5.4.tgz
4. `tar zxf trickster-1.5.4.tgz` - unzip
5. `rm trickster-1.5.4.tgz` - delete file
6. `cp trickster/values.yaml trickster-values.yaml` - copy file
7. `sudo vi trickster-values.yaml` - change originURL: http://prometheus-server:80 (get dns and port from `kubectl get services -n monitoring`)
8. `helm upgrade --install --create-namespace --values trickster-values.yaml trickster -n monitoring tricksterproxy/trickster` - install trickster with creating namespace
    1. Trickster can be accessed via `port:8480` on the following DNS name from within your cluster
    2. `export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=trickster,component=trickster" -o jsonpath="{.items[0].metadata.name}")` - Get the trickster URL
    3. `kubectl --namespace monitoring port-forward $POD_NAME 9090`

### Trickster Check:
1. `kubectl get pods -n monitoring` - monitoring pods
2. `kubectl get services -n monitoring` - monitoring services
    1. Grafana берёт информацию из trickster сервис (http://trickster:8480), а trickster сервис берёт инфор-ию из http://prometheus-server:80 
3. `docker exec -it minikube /bin/sh` + `docker ps` - list monitoring containers
4. `kubectl get pvc -n monitoring` - CAPACITY 8Gi
5. `kubectl get pv -n monitoring`


### Ingress Install:
1. `touch grafana-ingress.yaml` 
    1. add host: `monitoring.195-208-185-64.sslip.io`
    2. add ip's: `whitelist-source-range: 80.72.28.185`
2. `kubectl apply -f grafana-ingress.yaml -n monitoring` - add ingress to cluster (ingress.networking.k8s.io/grafana created) ingress.networking.k8s.io/ingress-nginxservice-a created
3. ### `minikube service grafana -n monitoring` - open ingress url to open Grafana and other services
4. `kubectl get ingress -n monitoring` - check ingress `host: monitoring.195-208-185-64.sslip.io` and `ports: 80, 443`
5. `kubectl describe ingress grafana -n monitoring` - ingress describe rules
6. `kubectl get endpoints -n monitoring` - check endpoints for ingress
7. `kubectl delete ingress grafana -n monitoring` - ingress.networking.k8s.io "grafana" deleted
    1. `kubectl delete ingress myhelmapp-grafana-ingress -n default` - ingress.networking.k8s.io "myhelmapp-grafana-ingress" deleted


1. `touch deployment.yaml` - create deployment without HELM
2. `kubectl apply -f deployment.yaml -n monitoring` - deployment.apps/nginx-deployment created
3. `kubectl delete -f deployment.yaml -n monitoring` - delete deployment


### Myhelmapp port-forward:
1. `kubectl --namespace default port-forward service/myhelmapp 8888:80` - url - http://127.0.0.1:8888/, http://127.0.0.1/
2. `kubectl --namespace prod port-forward service/myhelmapp 8889:80` - url - http://127.0.0.1:8889/
3. `kubectl --namespace dev port-forward service/myhelmapp 8890:80` - url - http://127.0.0.1:8890/


1. `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`
2. `helm repo update`
3. `kubectl create namespace ingress-nginx`
4. `helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx`
5. `kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller` - status
6. `kubectl get pods -n ingress-nginx`
7. `helm pull ingress-nginx/ingress-nginx`
7. `tar zxf ingress-nginx-4.8.0.tgz`
7. `rm ingress-nginx-4.8.0.tgz`
8. `cp ingress-nginx/values.yaml ingress-nginx-values.yaml`
9. `helm upgrade --install --create-namespace --values ingress-nginx-values.yaml ingress-nginx -n ingress-nginx ingress-nginx/ingress-nginx`