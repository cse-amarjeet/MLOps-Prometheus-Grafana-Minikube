# 🚀 MLOps Monitoring Stack: Flask App + Prometheus + Grafana on Minikube

This project demonstrates how to **containerize a Flask application**, deploy it on **Minikube**, and set up a **monitoring and visualization stack** using **Prometheus** and **Grafana**. It’s designed for **developers exploring MLOps**, **DevOps practitioners**, or **recruiters** seeking proof of hands-on Kubernetes, observability, and infrastructure automation.

---

## 🔧 Tech Stack

- **Flask** - Lightweight Python web framework
- **Docker** - Containerization of the app
- **Kubernetes (Minikube)** - Local K8s cluster for deployment
- **Prometheus** - Metrics collection and scraping
- **Grafana** - Visualization dashboards for real-time monitoring
- **Helm** - Package manager for Kubernetes

---



---

## 🏁 Getting Started

### ✅ 1. Run Flask App Locally

You can start with testing the Flask app locally before moving to the Kubernetes environment.

---

## 🐳 Deploy Flask App on Minikube

### 🔨 2. Build Docker Image

```bash
docker build -t flask-metrics-app:latest .
```

### ⚙️ 3. Start Minikube

```bash
minikube start
minikube image load flask-metrics-app:latest
```

### 📦 4. Deploy App to K8s

```bash
kubectl apply -f flask-app.yaml
minikube service flask-metrics-app --url
```

---

## 📈 Install Prometheus Using Helm

### 💾 5. Install Helm

```bash
# Windows
winget install Helm.Helm

# Or use:
scoop install helm
choco install kubernetes-helm
```

### 📡 6. Deploy Prometheus

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
```

### 🧭 7. Access Prometheus

```bash
kubectl port-forward -n monitoring svc/prometheus-server 9090:80
# Visit: http://localhost:9090
```

---

## 🔄 Configure Prometheus to Scrape Flask App

### 🛠️ 8. Add Scrape Target

```yaml
# kubectl edit configmap prometheus-server -n monitoring

scrape_configs:
  - job_name: 'flask-app'
    static_configs:
      - targets: ['<FLASK_CLUSTER_IP>:5000']
```

💡 Get Cluster IP:

```bash
kubectl get svc
```

### 🔁 Restart Prometheus Pod

```bash
# Option 1: By Label
kubectl get pods -n monitoring --show-labels
kubectl delete pod -l app.kubernetes.io/name=prometheus -n monitoring

# Option 2: By Deployment
kubectl rollout restart deployment prometheus-server -n monitoring
```

---

## 📊 Install & Access Grafana

### 📥 9. Install Grafana via Helm

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana -n monitoring --create-namespace
```

### 🔌 10. Port Forward to Access

```bash
kubectl port-forward svc/grafana -n monitoring 3000:80
# Visit: http://localhost:3000
```

### 🔐 11. Login to Grafana

```bash
# Get password (Linux/macOS)
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

# Windows PowerShell
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}"
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("<base64_password>"))
```

Username: `admin`

---

## 📡 Add Prometheus as a Data Source

### ➕ 12. Connect Prometheus to Grafana

* Navigate to:
  `Connections > Data Sources > Add data source > Prometheus`

* Use this URL:
  `http://prometheus-server.monitoring.svc.cluster.local:80`

* Click **Save & Test**

---

## 📈 Create Custom Dashboards

### 🧩 13. Create Your First Dashboard

* Navigate to:
  `Dashboard > New Dashboard > Add New Panel`
* Add your metric query like:

  ```
  total_api_requests_total
  ```

---

## ✅ Final Result

You now have:

* A Flask app exposing custom metrics ✅
* Prometheus scraping those metrics ✅
* Grafana visualizing them beautifully ✅
* Everything running on minikube(K8s) ✅

---

## 💡 Use-Cases

* Monitor ML model inference metrics
* Track API usage over time
* Analyze system health in real-time
* Build an end-to-end reproducible MLOps workflow

---



## 📝 License

This project is licensed under the MIT License.

---

## ⭐ Star This Repo

If you found this useful or learned something new, consider giving it a ⭐ on GitHub. It helps others find it too!

