+++
title = "Complete Linux System Monitoring with Grafana, InfluxDB & Telegraf (Munin Alternative)"
date = "2016-08-18"
lang = "en"
description = "Step-by-step guide to set up modern Linux system monitoring with Grafana, InfluxDB, and Telegraf. Perfect replacement for Munin with real-time dashboards and alerting."
keywords = ["Grafana", "InfluxDB", "Telegraf", "Linux monitoring", "system monitoring", "server monitoring", "Munin alternative", "DevOps"]
tags = ["Grafana", "InfluxDB", "Telegraf", "Monitoring", "Linux", "DevOps", "Tutorial"]
categories = ["DevOps", "Monitoring", "Linux"]
+++

This comprehensive tutorial will guide you through setting up a modern Linux monitoring stack using **Grafana**, **InfluxDB**, and **Telegraf**. This powerful combination provides real-time system monitoring, beautiful dashboards, and historical data storage - making it an excellent replacement for traditional tools like Munin.

## Why This Stack?

**Benefits over traditional monitoring:**
- **Real-time monitoring** with sub-second granularity
- **Beautiful, customizable dashboards** with Grafana
- **Scalable time-series database** with InfluxDB
- **Easy metric collection** with Telegraf
- **Alerting capabilities** for proactive monitoring
- **Modern web interface** accessible from anywhere

## Architecture Overview

```
Linux System → Telegraf → InfluxDB → Grafana → Dashboard
```

1. **Telegraf** collects system metrics (CPU, memory, disk, network)
2. **InfluxDB** stores time-series data efficiently
3. **Grafana** visualizes data with interactive dashboards

---

## Step 1: Installing InfluxDB

### For Ubuntu/Debian:

```bash
# Add InfluxDB repository
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list

# Install InfluxDB
sudo apt-get update && sudo apt-get install influxdb
sudo systemctl start influxdb
sudo systemctl enable influxdb
```

### For RHEL/CentOS:

```bash
# Add repository
cat <<EOF | sudo tee /etc/yum.repos.d/influxdb.repo
[influxdb]
name = InfluxDB Repository - RHEL
baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
EOF

# Install
sudo yum install influxdb
sudo systemctl start influxdb
sudo systemctl enable influxdb
```

### Secure InfluxDB Configuration

Edit the configuration file for better security:

```bash
sudo vim /etc/influxdb/influxdb.conf
```

**Key security settings:**
```toml
# Enable authentication
[http]
  auth-enabled = true
  
# Enable HTTPS (recommended for production)
  https-enabled = true
  https-certificate = "/etc/ssl/certs/influxdb.pem"
```

**Create admin user:**
```bash
# Connect to InfluxDB
influx

# Create admin user (replace with secure password)
CREATE USER admin WITH PASSWORD 'secure_admin_password' WITH ALL PRIVILEGES
exit
```

**Restart InfluxDB:**
```bash
sudo systemctl restart influxdb
```

---

## Step 2: Installing Grafana

### Download and Install Latest Grafana:

```bash
# Get latest version (check https://grafana.com/grafana/download)
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_10.2.0_amd64.deb
sudo apt-get install -y adduser libfontconfig1
sudo dpkg -i grafana-enterprise_10.2.0_amd64.deb

# Start and enable Grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

### Configure Grafana

Edit Grafana configuration:
```bash
sudo vim /etc/grafana/grafana.ini
```

**Key settings:**
```ini
[security]
admin_user = admin
admin_password = secure_grafana_password

[server]
http_port = 3000
domain = your-server-domain.com

[smtp]
enabled = true
host = localhost:587
# Configure for email alerts
```

**Restart Grafana:**
```bash
sudo systemctl restart grafana-server
```

**Access Grafana:**
- URL: `http://your-server:3000`
- Default: admin/admin (change immediately!)

---

## Step 3: Setting Up Telegraf

### Install Telegraf:

```bash
# For Ubuntu/Debian
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.28.5_amd64.deb
sudo dpkg -i telegraf_1.28.5_amd64.deb

# For RHEL/CentOS
wget https://dl.influxdata.com/telegraf/releases/telegraf-1.28.5.x86_64.rpm
sudo yum localinstall telegraf-1.28.5.x86_64.rpm
```

### Generate Telegraf Configuration:

```bash
# Generate config for common system metrics
telegraf config \
  --input-filter cpu:mem:disk:diskio:net:netstat:swap:system \
  --output-filter influxdb > /etc/telegraf/telegraf.conf
```

### Configure Telegraf:

Edit the configuration:
```bash
sudo vim /etc/telegraf/telegraf.conf
```

**Key InfluxDB output settings:**
```toml
[[outputs.influxdb]]
  urls = ["http://localhost:8086"]
  database = "telegraf"
  username = "telegraf_user"
  password = "telegraf_password"

# System monitoring inputs
[[inputs.cpu]]
  percpu = true
  totalcpu = true

[[inputs.disk]]
  ignore_fs = ["tmpfs", "devtmpfs", "devfs"]

[[inputs.mem]]

[[inputs.net]]

[[inputs.system]]
```

**Create Telegraf database user in InfluxDB:**
```bash
influx -username admin -password secure_admin_password
CREATE DATABASE telegraf
CREATE USER telegraf_user WITH PASSWORD 'telegraf_password'
GRANT ALL ON telegraf TO telegraf_user
exit
```

**Start Telegraf:**
```bash
sudo systemctl start telegraf
sudo systemctl enable telegraf
```

---

## Step 4: Configure Grafana Data Source

1. **Login to Grafana** (`http://your-server:3000`)
2. **Go to Configuration → Data Sources**
3. **Add InfluxDB data source:**
   - **URL:** `http://localhost:8086`
   - **Database:** `telegraf`
   - **User:** `telegraf_user`
   - **Password:** `telegraf_password`
4. **Test connection** and save

---

## Step 5: Create Monitoring Dashboards

### Import Pre-built Dashboard:

1. **Go to Dashboards → Import**
2. **Use dashboard ID:** `928` (Telegraph System Monitoring)
3. **Configure data source** and import

### Custom Dashboard Panels:

**CPU Usage Panel:**
```sql
SELECT mean("usage_idle") FROM "cpu" 
WHERE "host" = 'your-hostname' AND $timeFilter 
GROUP BY time($__interval), "cpu"
```

**Memory Usage Panel:**
```sql
SELECT mean("used_percent") FROM "mem" 
WHERE "host" = 'your-hostname' AND $timeFilter 
GROUP BY time($__interval)
```

**Disk Usage Panel:**
```sql
SELECT mean("used_percent") FROM "disk" 
WHERE "host" = 'your-hostname' AND $timeFilter 
GROUP BY time($__interval), "path"
```

---

## Step 6: Set Up Alerting

### Configure Alert Channels:

1. **Go to Alerting → Notification channels**
2. **Add Email/Slack/Discord notifications**
3. **Test notifications**

### Create Alerts:

**High CPU Alert:**
- **Condition:** `avg() OF query(A, 5m, now) IS ABOVE 80`
- **Evaluation:** Every `10s` for `30s`

**Low Disk Space Alert:**
- **Condition:** `avg() OF query(A, 5m, now) IS ABOVE 90`
- **Evaluation:** Every `1m` for `2m`

---

## Troubleshooting Common Issues

### InfluxDB Connection Issues:
```bash
# Check InfluxDB status
sudo systemctl status influxdb

# Check logs
sudo journalctl -u influxdb -f

# Test connection
influx -host localhost -port 8086
```

### Telegraf Not Collecting Data:
```bash
# Test Telegraf configuration
telegraf --config /etc/telegraf/telegraf.conf --test

# Check Telegraf logs
sudo journalctl -u telegraf -f

# Verify metrics in InfluxDB
influx -database telegraf
SHOW MEASUREMENTS
```

### Grafana Dashboard Issues:
```bash
# Check Grafana logs
sudo journalctl -u grafana-server -f

# Verify data source connection
# Go to Data Sources → Test
```

---

## Performance Optimization

### InfluxDB Tuning:
```toml
# /etc/influxdb/influxdb.conf
[data]
  cache-max-memory-size = "1g"
  cache-snapshot-memory-size = "25m"

[http]
  max-connection-limit = 0
```

### Telegraf Collection Intervals:
```toml
# Adjust collection frequency
[agent]
  interval = "10s"
  flush_interval = "10s"
```

---

## Advanced Features

### Custom Metrics:
```bash
# Add custom script monitoring
[[inputs.exec]]
  commands = ["/path/to/your/script.sh"]
  data_format = "influx"
```

### Log Monitoring:
```toml
# Monitor log files
[[inputs.tail]]
  files = ["/var/log/nginx/access.log"]
  from_beginning = false
  data_format = "grok"
```

---

## Conclusion

You now have a powerful, modern monitoring solution that provides:

- **Real-time system visibility**
- **Historical trend analysis**
- **Proactive alerting**
- **Scalable architecture**
- **Beautiful, customizable dashboards**

This setup scales from single servers to enterprise environments and provides much more flexibility than traditional tools like Munin.

**Next steps:**
- Explore Grafana's advanced visualization options
- Set up monitoring for applications and services
- Implement log aggregation with the ELK stack
- Consider using Grafana Cloud for managed hosting

---

*Questions or need help with advanced configurations? Connect with me on [GitHub](https://github.com/llazzaro) or [LinkedIn](https://www.linkedin.com/in/llazzaro/).*