# td-helloworld

1. Docker Build
```
docker build -t janson-helloworld .
docker tag janson-helloworld jansonluan/jl-helloworld
docker push jansonluan/jl-helloworld
```

2. Install Ingress Controller
```
helm repo add bitnami https://charts.bitnami.com/bitnami
#install ingress controller (ingress-nginx)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/cloud/deploy.yaml
```

3. Setup namespace from file
```
kubectl create namespace teledoc2
kubectl create -f manifest.yaml

```

4. Setup K8s Dashboard
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF

cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF

kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"

kubectl proxy
```

- To login to dashboard with secret:
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

5. Setup Prometheus /Grafana
```
helm install stable/prometheus-operator --generate-name
  kubectl get ns
  kubectl -n default get all
  helm list
  kubectl get pods
  kubectl get svc
  kubectl edit svc prometheus-operator-161519-prometheus
  kubectl edit svc prometheus-operator-1615194653-grafana
  kubectl get svc
```
Open Grafana
- Open 127.0.0.1:{port from service for grafana}
- you can use a premade dashboard that already has pod monitoring or you can setup your own dashboards