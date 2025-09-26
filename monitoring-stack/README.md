
# Docker Monitoring Stack with Grafana, Prometheus, Loki and Alertmanager

![Monitoring Stack Banner](https://img.shields.io/badge/Monitoring-Stack-blue?style=flat-square)  
[![Docker](https://img.shields.io/badge/Docker-%230db7ed.svg?style=flat&logo=docker&logoColor=white)](https://www.docker.com/) [![Grafana](https://img.shields.io/badge/Grafana-%23F46800.svg?style=flat&logo=grafana&logoColor=white)](https://grafana.com/) [![Prometheus](https://img.shields.io/badge/Prometheus-%23E6522C.svg?style=flat&logo=prometheus&logoColor=white)](https://prometheus.io/)

This repository provides a comprehensive Docker-based monitoring stack for observing your server's resources, Docker containers, metrics, logs, and alerts. It includes tools like Prometheus for metrics, Loki and Promtail for logs, Grafana for visualization, cAdvisor for container metrics, Node Exporter for host metrics, and Alertmanager for notifications (including email alerts via Gmail).

📊 **Perfect for**: Monitoring applications like web services (e.g., `frontend`, `backend`), databases (e.g., Postgres), queues (e.g., RabbitMQ), and caches (e.g., Redis) in a production or development environment.

## Features 🚀
- **Metrics Collection** 📈: Prometheus scrapes host-level (Node Exporter) and container-level (cAdvisor) metrics.
- **Log Aggregation** 📜: Promtail collects logs from all Docker containers and ships them to Loki for querying.
- **Visualization** 🖼️: Grafana dashboards for metrics and logs, with pre-provisioned data sources.
- **Alerting** ⚠️: Alertmanager sends email notifications (via Gmail SMTP) for issues like high CPU or container downtime.
- **Automatic Discovery** 🔍: Monitors all running containers.
- **Email Notifications** 📧: Configurable alerts to your email (e.g., `abc@gmail.com`).
- **Easy Deployment** 🐳: Single `docker compose up -d` command.

## Prerequisites 🛠️
Before setting up, ensure:
- Docker and Docker Compose are installed on your server.
- Server has sufficient resources: At least 4GB RAM, 2-4 CPU cores, and 20GB free disk space.
- Firewall allows inbound traffic on ports: 3000 (Grafana), 9090 (Prometheus), 9100 (Node Exporter), 8080 (cAdvisor), 9093 (Alertmanager), 3100 (Loki), 9080 (Promtail).
- A Gmail account with an [App Password](https://support.google.com/accounts/answer/185833) for Alertmanager (enable 2-Step Verification first).
- Your existing containers are running and logging to stdout/stderr for full log capture.

## Directory Structure 📂
The repository includes the following files and directories:
```
monitoring-stack/
├── docker-compose.yml          # Main Docker Compose file defining all services
├── prometheus/
│   ├── prometheus.yml          # Prometheus scraping and alerting config
│   └── rules.yml               # Custom alert rules
├── alertmanager/
│   └── config.yml              # Alertmanager notification setup
├── loki/
│   └── config.yaml             # Loki log storage config
├── promtail/
│   └── config.yaml             # Promtail log scraping config
└── grafana/
    └── provisioning/
        └── datasources/
            └── datasources.yaml  # Grafana data source provisioning
```

## Setup Instructions 📝
1. **Clone the Repository**:
   ```
   git clone <your-repo-url> /opt/monitoring-stack
   cd /opt/monitoring-stack
   ```

2. **Understand and Customize Configurations**:
   The configuration files are already in the repository. Here's an overview of each file, what it does, and why it's used:

   - **prometheus/prometheus.yml**: Configures Prometheus to scrape metrics from targets like itself, Node Exporter, and cAdvisor. It also sets up alerting to Alertmanager. *Why?* This centralizes metric collection, enabling queries for CPU, memory, and custom app metrics. Adjust scrape_configs to add your app's metrics endpoints if available.
   
   - **prometheus/rules.yml**: Defines alert rules, such as high CPU usage or container downtime. *Why?* These rules evaluate metrics and trigger alerts, allowing proactive monitoring (e.g., notify if a container exceeds 80% CPU).
   
   - **alertmanager/config.yml**: Sets up email notifications via Gmail SMTP, routing alerts to receivers like `abc@gmail.com`. *Why?* This handles alert delivery, ensuring you get emails for critical issues. Replace `auth_password` with your Gmail App Password.
   
   - **loki/config.yaml**: Configures Loki for log storage using filesystem backend and TSDB schema. *Why?* Loki indexes and stores logs efficiently, allowing fast queries in Grafana.
   
   - **promtail/config.yaml**: Sets up Promtail to discover Docker containers via the socket and scrape their JSON logs, relabeling for container names. *Why?* This automates log collection from all containers, forwarding them to Loki without manual setup.
   
   - **grafana/provisioning/datasources/datasources.yaml**: Provisions Prometheus and Loki as data sources in Grafana. *Why?* This auto-configures Grafana for immediate use, saving time on manual setup for metrics and logs visualization.

   Customize these files as needed (e.g., add app-specific scrape jobs or alert rules), then proceed.

3. **Configure Firewall (Optional)** 🔥:
   ```
   #If Firewall Active
   sudo ufw allow 3000  # Grafana
   sudo ufw allow 9090  # Prometheus
   sudo ufw allow 9100  # Node Exporter
   sudo ufw allow 8080  # cAdvisor
   sudo ufw allow 9093  # Alertmanager
   sudo ufw allow 3100  # Loki
   sudo ufw allow 9080  # Promtail
   sudo ufw reload
   ```

## How to Run It ▶️
1. **Start the Stack**:
   ```
   docker compose up -d
   ```

2. **Verify Running Containers**:
   ```
   docker ps -a
   ```
   You should see services like `grafana`, `prometheus`, `loki`, etc.

3. **Access Services**:
   - Grafana: http://<server-ip>:3000 (login: `admin` / `your_secure_password`)
   - Prometheus: http://<server-ip>:9090
   - Alertmanager: http://<server-ip>:9093
   - Loki: http://<server-ip>:3100 (query via Grafana)

4. **View Logs and Metrics in Grafana**:
   - Go to Explore > Loki > Query: `{container_name="frontend"}` (adjust time range to "Last 48 hours" for older logs).
   - For metrics: Explore > Prometheus > Query: `container_cpu_usage_seconds_total{container_name="frontend"}`.

5. **Test Alerts** ⚡:
   - Trigger a high CPU alert by stressing a container (e.g., `docker exec -it frontend stress --cpu 4 --timeout 300`).
   - Check your email for notifications.

6. **Stop the Stack**:
   ```
   docker compose down
   ```

## Troubleshooting 🐞
- **No Logs in Grafana?** 📜: Ensure apps log to stdout/stderr. Check `docker logs <container>` and extend Grafana time range. Reset Promtail positions: `rm /tmp/positions.yaml && docker restart promtail`.
- **Email Alerts Not Sending?** 📧: Verify Gmail App Password and test SMTP connectivity. Check `docker logs alertmanager`.
- **Port Conflicts?** ⚠️: Use `netstat -tuln` to check and reassign in `docker-compose.yml`.
- **Resource Issues?** 💻: Monitor with `docker stats`. Adjust scrape intervals in `prometheus.yml` if high usage.
- **Common Errors**: If Promtail can't read logs, ensure volumes are mounted correctly. For Postgres metrics, add a `postgres-exporter` service.

## Contributing 🤝
Feel free to fork and submit PRs for improvements, like custom dashboards or additional exporters.

## License 📄
MIT License - Free to use and modify.
