
# Loki Installation as a Systemd Service

This guide provides step-by-step instructions to install and run Grafana Loki as a service on a Linux system.

---

## 🔧 Prerequisites

- A Linux server with `curl`, `unzip`, and `systemd` support
- Root or sudo access

---

## 📥 Download and Install Loki

```bash
curl -O -L https://github.com/grafana/loki/releases/download/v2.9.3/loki-linux-amd64.zip
unzip "loki-linux-amd64.zip"
chmod a+x "loki-linux-amd64"
sudo cp loki-linux-amd64 /usr/local/bin/loki
loki --version
```

---

## 👤 Create Loki User and Directory

```bash
sudo useradd --system loki
sudo mkdir -p /etc/loki /etc/loki/logs
```

---

## 📄 Download Configuration File

```bash
sudo curl -o /etc/loki/loki-local-config.yaml -L https://gist.github.com/psujit775/ceaf475fc369e25a2d04501f8a7c0a59/raw
sudo chown -R loki: /etc/loki
```

---

## ⚙️ Create systemd Service File

Create and edit `/etc/systemd/system/loki.service`:

```ini
[Unit]
Description=Loki service
After=network.target

[Service]
Type=simple
User=loki
ExecStart=/usr/local/bin/loki -config.file /etc/loki/loki-local-config.yaml
Restart=on-failure
RestartSec=20
StandardOutput=append:/etc/loki/logs/loki.log
StandardError=append:/etc/loki/logs/loki.log

[Install]
WantedBy=multi-user.target
```

---

## 🚀 Start and Enable Loki Service

```bash
sudo systemctl daemon-reload
sudo systemctl start loki
sudo systemctl status loki
sudo systemctl enable loki.service
```

---

## 🌐 Verify Loki

Visit Loki metrics endpoint:

```
http://<YOUR-HOST>:3100/metrics
```

Replace `<YOUR-HOST>` with your actual server IP or domain.

---

## ✅ Done!

Loki is now running as a service and will automatically start on boot.