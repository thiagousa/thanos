
# Prerequisites

- Docker must be installed on your machine
- Kind (Kubernetes IN Docker) must be installed on your machine
- Helm must be installed on your machine

# Step 1: Create a Kubernetes cluster
kind create cluster --config config.yaml

# Step 2: Verify the cluster information and context
kubectl cluster-info --context kind-thanos-demo

# Step 3: Add the Prometheus Community repository to Helm
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Step 4: Create two namespaces
kubectl create namespace europe
kubectl create namespace united-states

# Step 5: Deploy Prometheus in the europe namespace
helm -n europe upgrade -i prometheus-europe prometheus-community/kube-prometheus-stack -f prometheus-europe.yaml

# Step 6: Verify Prometheus deployment in the europe namespace
kubectl --namespace europe get pods -l "release=prometheus-europe"

# Step 7: Deploy Prometheus in the united-states namespace
helm -n united-states upgrade -i prometheus-united-states prometheus-community/kube-prometheus-stack -f prometheus-united-states.yaml

# Step 8: Deploy Thanos in the monitoring namespace
helm -n monitoring upgrade -i thanos banzaicloud/thanos -f query.yaml

# Step 9: Forward the Thanos query HTTP port
kubectl -n monitoring port-forward svc/thanos-query-http 10902:10902

# Step 10: Forward the Prometheus Grafana port in the europe namespace
kubectl -n europe port-forward svc/prometheus-europe-grafana 8080:80
