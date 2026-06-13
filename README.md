# 📊 GitOps Observability Platform

Centralized observability stack built using Grafana, Loki, Mimir, Prometheus and OpenTelemetry.

This repository deploys the monitoring and logging backend used to observe applications running on AWS.

---

## 🚀 Features

✅ Metrics Collection

✅ Log Aggregation

✅ Centralized Monitoring

✅ Dashboard Visualization

✅ OpenTelemetry Integration

✅ GitOps-Based Deployment

---

## 🏛️ Architecture

```text
Tomcat Application
        │
        │ Metrics + Logs
        ▼
OpenTelemetry Collector
        │
 ┌──────┴──────┐
 │             │
 ▼             ▼
Mimir         Loki
 │             │
 └──────┬──────┘
        ▼
     Grafana
```

---

## 🛠️ Components

| Component | Purpose |
|------------|----------|
| Grafana | Dashboards & Visualization |
| Loki | Log Aggregation |
| Mimir | Metrics Storage |
| Prometheus | Metrics Collection |
| Promtail | Log Shipping |
| OpenTelemetry Collector | Telemetry Processing |

---

## 📋 Prerequisites

- Observability Hub deployed
- Tomcat Node deployed
- Docker installed
- SSH access available

---

# Phase 1 – Deploy Observability Hub

## SSH to Hub

```bash
ssh -i <your-key.pem> ubuntu@<observability_hub_public_ip>
```

## Start Stack

```bash
cd ~/gitops-observability-apps
sudo docker-compose up -d
```

## Verify Containers

```bash
sudo docker ps -a
```

Expected:

- grafana
- mimir
- loki
- prometheus
- promtail

---

# Phase 2 – Enable Tomcat Telemetry

## SSH to Tomcat Node

```bash
ssh -i <your-key.pem> ubuntu@<tomcat_public_ip>
```

## Grant Permissions

```bash
sudo usermod -aG adm otelcol-contrib
sudo usermod -aG tomcat otelcol-contrib
```

## Restart Collector

```bash
sudo systemctl restart otelcol-contrib
```

## Generate Test Data

```bash
curl http://localhost:8080/trigger-test-error

sudo bash -c 'echo "SEVERE [main] LOKI TEST: Boot sequence verified" >> /var/log/tomcat9/catalina.out'
```

---

# Phase 3 – Configure Grafana

Open:

```text
http://<observability_hub_public_ip>:3000
```

Default credentials:

```text
admin / admin
```

---

## Configure Mimir

Type:

```text
Prometheus
```

URL:

```text
http://mimir:9009/prometheus
```

Header:

```text
X-Scope-OrgID = tenant-tomcat
```

---

## Configure Loki

Type:

```text
Loki
```

URL:

```text
http://loki:3100
```

---

## ✅ Validation Checklist

### Metrics

```promql
up
```

Expected:

```text
tomcat-jmx-exporter = 1
```

### Logs

```logql
{job="tomcat-logs"}
```

Expected:

- Tomcat Access Logs
- catalina.out Logs

---

## 📸 Screenshots

Add screenshots of:

1. Grafana Dashboard
2. JVM Metrics
3. Loki Logs
4. Infrastructure Metrics
5. Docker Containers

---

## 🎯 Skills Demonstrated

- Docker Compose
- Grafana
- Loki
- Mimir
- Prometheus
- OpenTelemetry
- Log Aggregation
- Metrics Monitoring
- Observability Engineering
- GitOps

---

## 🔗 Related Repositories

- terraform-modules
- observability-infra-proj

---

## 👨‍💻 Author

**Pushpak Badadale**

📧 [Email](mailto:itspushpaksworld496@gmail.com)

💼 [LinkedIn](https://www.linkedin.com/in/pushpak-badadale-492409179)

🐙 [GitHub](https://github.com/its-pushpaks-world)
