
# Grafana OSS Installation on Ubuntu/Debian

This guide walks you through installing **Grafana OSS** on a Linux system using APT repositories.

---

## 🔧 Prerequisites

- Ubuntu/Debian-based system
- `sudo` privileges

---

## 📦 Install Required Packages

```bash
sudo apt-get install -y apt-transport-https software-properties-common wget
```

---

## 🔐 Import Grafana GPG Key

```bash
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
```

---

## ➕ Add Grafana APT Repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

---

## 🔄 Update Package List

```bash
sudo apt-get update
```

---

## 📥 Install Grafana OSS

```bash
sudo apt-get install grafana
```

---

## 🚀 Start and Enable Grafana Server

```bash
sudo systemctl start grafana-server
sudo systemctl status grafana-server
sudo systemctl enable grafana-server
```

---

## 🌐 Access Grafana

Once started, access Grafana by visiting:

```
http://<YOUR-HOST>:3000
```

- **Default Username:** `admin`
- **Default Password:** `admin` (you will be prompted to change this on first login)

---

## ✅ Done!

Grafana is now installed and running. You can now add data sources like **Loki**, **Prometheus**, and start visualizing your logs and metrics.