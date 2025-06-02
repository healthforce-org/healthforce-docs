# Deployment Guide for HealthForce Project

## Overview
Dokumen ini menjelaskan prosedur deployment aplikasi HealthForce, meliputi backend Golang services dan modul Odoo pada lingkungan production dan staging.

## Prasyarat
- Akses ke cluster Kubernetes (k8s) yang sudah disiapkan.
- Konfigurasi kubeconfig dengan izin deploy.
- Docker image terbaru sudah tersedia di registry.
- Secrets dan configmaps sudah ter-update.

## Langkah Deployment

### 1. Build dan Push Docker Image
```bash
# Backend service
docker build -t registry.healthforce.id/backend:latest ./backend
docker push registry.healthforce.id/backend:latest

# Odoo service
docker build -t registry.healthforce.id/odoo:18.0 ./odoo
docker push registry.healthforce.id/odoo:18.0
