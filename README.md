# gitops-observability-apps

This repository contains the Docker Compose stack that powers the central observability backend. It runs Grafana for visualization, Mimir for metrics, Loki for logs, Promtail, and Prometheus.

## Prerequisites
* The Observability Hub EC2 instance provisioned via `observability-infra-proj`.
* SSH access to both the Hub and Tomcat EC2 instances.

## Phase 1: Deploy Observability Hub

1. **SSH into the Observability Hub Node**
   ```bash
   ssh -i <your-key.pem> ubuntu@<observability_hub_public_ip>
   ```

2. **Start the Docker Stack**
   Navigate to this repository's directory and boot the containers in detached mode.
   ```bash
   cd ~/gitops-observability-apps
   sudo docker-compose up -d
   ```

3. **Verify Container Health**
   Ensure all 5 containers are running without restart loops.
   ```bash
   sudo docker ps -a
   ```

## Phase 2: Activate Tomcat Telemetry (App Node)

The infrastructure script automatically installed the OpenTelemetry Collector on the Tomcat node, but Linux permissions must be applied so the collector can read the secure log files.

1. **SSH into the Tomcat Node**
   ```bash
   ssh -i <your-key.pem> ubuntu@<tomcat_public_ip>
   ```

2. **Grant Log File Permissions**
   Add the OpenTelemetry user to the required groups.
   ```bash
   sudo usermod -aG adm otelcol-contrib
   sudo usermod -aG tomcat otelcol-contrib
   ```

3. **Apply Configurations**
   Restart the collector to apply the new permissions and begin shipping telemetry data to the Hub.
   ```bash
   sudo systemctl restart otelcol-contrib
   ```

4. **Generate Test Data**
   Force Tomcat to write to its access and server logs to verify the pipeline is flowing to Loki.
   ```bash
   # Trigger a 404 Access Log (.txt)
   curl http://localhost:8080/trigger-test-error
   
   # Trigger a Server Log (catalina.out)
   sudo bash -c 'echo "SEVERE [main] LOKI TEST: Boot sequence verified" >> /var/log/tomcat9/catalina.out'
   ```

## Phase 3: Grafana Configuration

1. Open Grafana in your web browser: `http://<observability_hub_public_ip>:3000` (Default login: `admin` / `admin`).
2. Navigate to **Connections > Data sources**.

**Add Mimir (Metrics):**
* Type: `Prometheus`
* URL: `http://mimir:9009/prometheus`
* Custom HTTP Header: `X-Scope-OrgID` = `tenant-tomcat`
* Click **Save & test**.

**Add Loki (Logs):**
* Type: `Loki`
* URL: `http://loki:3100`
* Click **Save & test**.
