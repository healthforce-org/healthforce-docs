# Monitoring Setup for HealthForce

## Overview
Panduan konfigurasi monitoring sistem menggunakan Prometheus dan Grafana.

## Components
- Prometheus: mengumpulkan metrik dari backend dan Odoo
- Grafana: dashboard visualisasi data

## Setup Prometheus
- Scrape endpoint metrics pada backend (`/metrics`)
- Konfigurasi Prometheus untuk monitoring Odoo (custom exporter jika ada)

## Metrics yang Dimonitor
- Request latency & throughput
- Error rate API
- Login SSO success/failure
- Status integrasi sistem eksternal (enabled/disabled)
- Resource usage (CPU, Memory)

## Grafana Dashboard
- Panel login SSO per har
- Error rate integrasi eksternal
- Latency response API
- Alert setup: Slack/Email untuk kegagalan integrasi dan error critical

---

# ops/troubleshooting.md

```markdown
# Troubleshooting Guide for HealthForce

## Common Issues & Solutions

### 1. Backend Service Crash / Pod Restart
- Cek log pod:
```bash
kubectl logs <pod-name> -n healthforce
