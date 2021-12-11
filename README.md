# prometheus-alertmanager-kubernetes

# Pre-Requisites:
    EKS-Cluster
# Install helm 
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    cp /usr/local/bin/helm /usr/bin
    which helm
# Deploy RMQ on EKS
    helm repo add stable https://charts.helm.sh/stable
    kubectl create namespace rabbit
    helm install mu-rabbit stable/rabbitmq --namespace rabbit
    
    Now login to RMQ pod and enable rabbitmq_management plugin, which will expose RabbitMQ metrics to /metrics endpoint.
    
    rabbitmq-plugins enable rabbitmq_management
    
    k exec mu-rabbit-rabbitmq-0 -n rabbit -- rabbitmq-plugins enable rabbitmq_prometheus
    k exec mu-rabbit-rabbitmq-0 -n rabbit -- curl -v -H "Accept:text/plain" "http://localhost:15692/metrics"
        
# Deploy Node Exporter 
    kubectl apply -f node-exporter
# Deploy kube-state-metrics
    kubectl apply -f kube-state-metrics
# Deploy alertmanager
    kubectl apply -f alertmanager
# Deploy prometheus
    kubectl apply -f prometheus
# Note:
  Here I kept three rules: (we can get alaram)
  3. Messages in Queue if goes beyond 10
  2. CPU Utilization High for Nodes
  3. Pod status Pending/Unknown

# Alarm1: Create a new Queue TestQueue and post more than 12 messages, will get alarm



![image](https://user-images.githubusercontent.com/74225291/145672055-71d27167-8319-4ca9-bfce-e99d08148faa.png)

# Alaram2: Connect to node and try to increase CPU Utilization, will get Alaram
# Alaram3: Deploy nginx
    kubectl apply -f nginx-deployment.yml
  Nginx Pod status will be under pending state, So will get Alaram
