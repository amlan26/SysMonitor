# 🐳 Loki Docker Logging Driver Setup Guide

This guide helps you set up the **Loki Docker logging driver** to ship container logs directly to a Loki server.

---

## 📦 Prerequisites

- Docker installed and running (v19.03+ recommended)
- Access to a Loki server (running at a specific IP/hostname)
- Root or sudo privileges

---

## 🧰 Step-by-Step Setup

### 1. 🔌 Install Loki Docker Logging Plugin

Use the following command to install the Loki Docker plugin:

```bash
docker plugin install grafana/loki-docker-driver:2.9.1 --alias loki --grant-all-permissions
```

✅ This will allow Docker to send logs directly to Loki.

To verify the plugin is installed:

```bash
docker plugin ls
```

---

### 2. ⚙️ Configure Docker Daemon

Edit the Docker daemon configuration file:

```bash
sudo nano /etc/docker/daemon.json
```

Add the following content:

```json
{
  "debug": true,
  "log-driver": "loki",
  "log-opts": {
    "loki-url": "http://loki-server-ip:3100/loki/api/v1/push",
    "loki-batch-size": "400",
    "loki-retries": "3",
    "loki-max-backoff": "1s",
    "loki-timeout": "1s",
    "keep-file": "true",
    "no-file": "false",
    "max-size": "100m",
    "max-file": "2"
  }
}
```

> 📝 **Note:** Replace `loki-server-ip` with your actual Loki server's IP address or domain.

---

### 3. 🔄 Restart Docker

For the changes to take effect, restart the Docker service:

```bash
sudo systemctl restart docker
```

---

## 🚀 Test the Configuration

Try running a container and check your Loki instance to verify logs are being shipped correctly.

```bash
docker run --rm alpine echo "Hello from Loki"
```

You should see this log entry appear in Grafana's Loki Explore UI.

---

## 📚 Resources

- [Grafana Loki Documentation](https://grafana.com/docs/loki/latest/)
- [Docker Logging Plugins](https://docs.docker.com/config/containers/logging/plugins/)

---

## ✅ Done

Your Docker host is now configured to send logs directly to Loki using the Loki Docker plugin. Enjoy better observability! 🚀