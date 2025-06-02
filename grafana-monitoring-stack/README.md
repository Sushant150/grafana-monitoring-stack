
# GrafanaFlow Monitoring Stack

Comprehensive logging and monitoring solution using the Grafana ecosystem with real-time alerting, dashboard visualization, and multi-source data integration.

## 🎯 Overview

GrafanaFlow is a production-ready monitoring stack that provides:
- **Real-time log aggregation** with Loki
- **Custom dashboard creation** with Grafana
- **Alert management system** with Alertmanager
- **Multi-source data integration** from AWS CloudWatch, EC2, and applications

## 🏗️ Architecture

```
Applications/Services
    ↓
Promtail (Log Collection)
    ↓
Loki (Log Storage & Querying)
    ↓
Grafana (Visualization & Alerting)
    ↓
Alertmanager (Alert Routing)
    ↓
Slack/Email/PagerDuty
```

## ✨ Features

- **Centralized Logging**: Collect logs from multiple sources
- **Real-time Monitoring**: Live dashboards with auto-refresh
- **Smart Alerting**: Rule-based alerts with multiple notification channels
- **Cost-effective**: Loki's label-based indexing reduces storage costs
- **Scalable**: Horizontal scaling support for high-volume environments
- **Multi-tenant**: Support for multiple teams and environments

## 🛠️ Tech Stack

- **Grafana**: Visualization and dashboarding
- **Loki**: Log aggregation and storage
- **Promtail**: Log collection agent
- **Prometheus**: Metrics collection and storage
- **AWS CloudWatch**: AWS service monitoring
- **EC2**: Host-level metrics
- **Docker**: Containerized deployment

## 📁 Project Structure

```
grafana-monitoring-stack/
├── docker-compose/
│   ├── docker-compose.yml
│   ├── grafana/
│   │   ├── provisioning/
│   │   └── dashboards/
│   ├── loki/
│   │   └── loki-config.yml
│   ├── promtail/
│   │   └── promtail-config.yml
│   └── prometheus/
│       └── prometheus.yml
├── kubernetes/
│   ├── grafana/
│   ├── loki/
│   ├── promtail/
│   └── prometheus/
├── aws-integration/
│   ├── cloudwatch-exporter/
│   └── iam-policies/
├── dashboards/
│   ├── infrastructure.json
│   ├── application.json
│   └── business-metrics.json
├── alerts/
│   ├── infrastructure.yml
│   └── application.yml
└── README.md
```

## 🔧 Components Configuration

### Loki Configuration
```yaml
auth_enabled: false
server:
  http_listen_port: 3100
  grpc_listen_port: 9096

ingester:
  wal:
    enabled: true
    dir: /loki/wal
  lifecycler:
    address: 127.0.0.1
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1

schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h
```

### Promtail Configuration
```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: containers
    static_configs:
      - targets:
          - localhost
        labels:
          job: containerlogs
          __path__: /var/log/containers/*.log
```

## 🚀 Quick Start

### Docker Compose Deployment

```bash
# Clone the repository
git clone https://github.com/buildwithsushant/grafana-monitoring-stack.git
cd grafana-monitoring-stack

# Start the stack
docker-compose up -d

# Access Grafana
# URL: http://localhost:3000
# Username: admin
# Password: admin
```

### Kubernetes Deployment

```bash
# Apply Kubernetes manifests
kubectl apply -f kubernetes/

# Forward Grafana port
kubectl port-forward svc/grafana 3000:3000
```

### AWS Integration Setup

```bash
# Install CloudWatch exporter
cd aws-integration/cloudwatch-exporter
./install.sh

# Configure IAM permissions
aws iam create-policy --policy-name GrafanaCloudWatchReadOnly \
  --policy-document file://iam-policies/cloudwatch-read-policy.json
```

## 📊 Pre-built Dashboards

### Infrastructure Dashboard
- **System Metrics**: CPU, Memory, Disk usage
- **Network Monitoring**: Bandwidth, connections
- **Container Metrics**: Docker container performance
- **AWS Resources**: EC2, RDS, Load Balancers

### Application Dashboard
- **Response Times**: API endpoint performance
- **Error Rates**: 4xx/5xx error tracking
- **Throughput**: Requests per second
- **Database Performance**: Query times, connections

### Business Metrics Dashboard
- **User Analytics**: Active users, sessions
- **Revenue Tracking**: Sales, conversions
- **Feature Usage**: Feature adoption rates
- **SLA Monitoring**: Uptime, availability

## 🚨 Alert Rules

### Infrastructure Alerts
```yaml
groups:
  - name: infrastructure
    rules:
      - alert: HighCPUUsage
        expr: cpu_usage_percent > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage detected"
          description: "CPU usage is above 80% for more than 5 minutes"
```

### Application Alerts
```yaml
groups:
  - name: application
    rules:
      - alert: HighErrorRate
        expr: error_rate > 5
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
          description: "Error rate is above 5% for more than 2 minutes"
```

## 🔗 Integrations

### Notification Channels
- **Slack**: Real-time alerts to team channels
- **Email**: Detailed alert reports
- **PagerDuty**: Critical incident management
- **Webhook**: Custom integrations

### Data Sources
- **Prometheus**: Metrics collection
- **Loki**: Log queries
- **CloudWatch**: AWS service metrics
- **MySQL/PostgreSQL**: Database metrics
- **Custom APIs**: Business metrics

## 📈 Performance Tuning

### Loki Optimization
- Proper label design for efficient querying
- Retention policies for cost management
- Chunk caching for query performance

### Grafana Optimization
- Dashboard variable usage
- Query optimization techniques
- Panel refresh interval tuning

## 🔐 Security Best Practices

- Authentication via OAuth/LDAP
- Role-based access control (RBAC)
- HTTPS encryption
- Secure credential management
- Network security groups

## 🌐 Live Demo

🔗 **Live Monitoring**: [https://monitoring.buildwithsushant.com](https://monitoring.buildwithsushant.com)

## 📚 Documentation

- [Installation Guide](docs/installation.md)
- [Configuration Reference](docs/configuration.md)
- [Dashboard Creation](docs/dashboards.md)
- [Alert Setup](docs/alerts.md)
- [Troubleshooting](docs/troubleshooting.md)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

---

**Built with ❤️ by Sushant Kumar**
