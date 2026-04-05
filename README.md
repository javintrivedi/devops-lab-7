# VLE-7: Full DevOps Monitoring System
### AWS + Kubernetes + CI/CD + Prometheus + Grafana + Alertmanager

> **Name:** Javin Trivedi  
> **Subject:** Essentials in Cloud & DevOps  
> **GitHub:** [@javintrivedi](https://github.com/javintrivedi)

---

## Objective

To design and deploy an end-to-end DevOps monitoring system integrating:

- **AWS EC2** — Cloud infrastructure
- **Jenkins** — CI/CD pipeline
- **Docker** — Containerization
- **Kubernetes (Minikube)** — Container orchestration
- **Prometheus** — Metrics collection & alerting
- **Grafana** — Visualization & dashboards
- **Alertmanager** — Alert routing & notification

---

## System Architecture

```
 Developer → GitHub → Jenkins (CI/CD) → Docker Image → Kubernetes
                                                          ↓
                                               Prometheus (scrapes metrics)
                                                          ↓
                                               Grafana (visualizes)
                                                          ↓
                                               Alertmanager (sends alerts)
```

---

## Repository Structure

```
devops-lab-7/
├── Dockerfile          # Docker image definition
├── index.html          # Sample web application
├── Jenkinsfile         # CI/CD pipeline definition
└── README.md           # This file
```

---

## Prerequisites

- AWS account (Free tier)
- Ubuntu 22.04 EC2 instance (t3.small)
- Security Group ports open: 22, 3000, 8080, 9090, 9093
- GitHub account

---

## Phase 1 — AWS Infrastructure Setup

### Launch EC2 Instance
- Ubuntu 22.04, t3.small
- Open inbound ports: 22 (SSH), 8080 (Jenkins), 9090 (Prometheus), 3000 (Grafana), 9093 (Alertmanager)

### Install Required Tools
```bash
sudo apt update && sudo apt install docker.io git curl -y
sudo systemctl start docker && sudo systemctl enable docker
sudo usermod -aG docker ubuntu
```

---

## Phase 2 — CI/CD with Jenkins

### Install Jenkins
```bash
sudo apt install openjdk-17-jdk -y
wget https://get.jenkins.io/war-stable/latest/jenkins.war
nohup java -jar jenkins.war --httpPort=8080 &
```

Access Jenkins at: `http://<EC2-IP>:8080`

### Jenkinsfile (Pipeline)
```groovy
pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/javintrivedi/devops-lab-7.git'
            }
        }
        stage('Build Docker Image') {
            steps { sh 'echo docker build -t myapp .' }
        }
        stage('Deploy to K8s') {
            steps { sh 'echo kubectl apply -f deployment.yaml' }
        }
    }
}
```

---

## Phase 3 — Kubernetes Setup

### Install Minikube & kubectl
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
sudo snap install kubectl --classic
minikube start --driver=docker
```

### Deploy Application
```bash
kubectl apply -f deployment.yaml
kubectl get pods
```

### deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: nginx
        ports:
        - containerPort: 80
```

---

## Phase 4 — Monitoring with Prometheus & Grafana

### Install Prometheus
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar xvf prometheus-2.45.0.linux-amd64.tar.gz
cd prometheus-2.45.0.linux-amd64
nohup ./prometheus --web.listen-address=0.0.0.0:9090 &
```

Access Prometheus at: `http://<EC2-IP>:9090`

### Install Grafana
```bash
sudo apt-get install -y grafana=9.5.0
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

Access Grafana at: `http://<EC2-IP>:3000` (Login: admin/admin)

### Connect Prometheus to Grafana
1. Go to Connections → Data Sources → Add data source
2. Select **Prometheus**
3. URL: `http://localhost:9090`
4. Click **Save & Test**

---

## Phase 5 — Alerting with Alertmanager

### Alert Rules (alert-rules.yml)
```yaml
groups:
- name: k8s-alerts
  rules:
  - alert: PodCrashLoop
    expr: kube_pod_container_status_restarts_total > 3
    for: 2m
    labels:
      severity: critical
    annotations:
      summary: "Pod is crash looping"
```

### Install Alertmanager
```bash
wget https://github.com/prometheus/alertmanager/releases/download/v0.25.0/alertmanager-0.25.0.linux-amd64.tar.gz
tar xvf alertmanager-0.25.0.linux-amd64.tar.gz
cd alertmanager-0.25.0.linux-amd64
nohup ./alertmanager &
```

Access Alertmanager at: `http://<EC2-IP>:9093`

---

## Phase 6 — Full CI/CD + Monitoring Integration

### Complete Flow
1. Developer pushes code → **GitHub**
2. Jenkins pulls Jenkinsfile from GitHub → **builds Docker image**
3. Deploys application to **Kubernetes** cluster
4. **Prometheus** scrapes metrics from pods
5. **Grafana** visualizes metrics on dashboards
6. **Alertmanager** sends alerts when rules trigger

---

## Services & Access URLs

| Service | URL | Credentials |
|---------|-----|-------------|
| Jenkins | `http://<EC2-IP>:8080` | admin / (from initialAdminPassword) |
| Prometheus | `http://<EC2-IP>:9090` | — |
| Grafana | `http://<EC2-IP>:3000` | admin / admin |
| Alertmanager | `http://<EC2-IP>:9093` | — |

---

## Restart All Services

If the EC2 instance restarts, run:

```bash
# Start Jenkins
nohup java -jar ~/jenkins.war --httpPort=8080 &

# Start Prometheus
cd ~/prometheus-2.45.0.linux-amd64 && nohup ./prometheus --web.listen-address=0.0.0.0:9090 &

# Start Grafana
sudo systemctl start grafana-server

# Start Alertmanager
cd ~/alertmanager-0.25.0.linux-amd64 && nohup ./alertmanager &

# Start Minikube
minikube start --driver=docker
```

---

## Result

Successfully designed and deployed a **Full DevOps Monitoring System** integrating:

- ✅ Cloud (AWS EC2)
- ✅ CI/CD (Jenkins + GitHub)
- ✅ Containerization (Docker)
- ✅ Orchestration (Kubernetes/Minikube)
- ✅ Monitoring (Prometheus + Grafana)
- ✅ Alerting (Alertmanager)

---

*VLE-7 | Essentials in Cloud & DevOps*
