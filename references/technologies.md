# 📌 Teknologi yang Digunakan di HealthForce

Berikut adalah daftar teknologi yang digunakan dalam pengembangan dan operasional sistem HealthForce.

## ⚙️ Backend
- **Golang (Go)** — Bahasa utama untuk layanan backend dan API Gateway.
- **Gin** — Web framework ringan untuk membuat RESTful API di Go.
- **gRPC** — Komunikasi antar microservices (opsional, tergantung desain akhir).
- **PostgreSQL** — Sistem basis data utama untuk penyimpanan data terstruktur.
- **Redis** — Digunakan untuk caching dan pub/sub system (optional).
- **Google Cloud Platform (GCP)** — Infrastruktur cloud utama (Compute Engine, Cloud SQL, IAM, dll).
- **Docker** — Containerisasi semua komponen layanan.
- **Kubernetes (GKE)** — Orkestrasi layanan dalam skala besar di GCP.
- **Prometheus/Grafana** — Monitoring dan observabilitas sistem.

## 📊 Admin Dashboard
- **Odoo 18.0 Community Edition** — Digunakan untuk modul admin dashboard.
- **QWeb & XML** — Untuk tampilan UI di Odoo.
- **PostgreSQL** — DB default dari Odoo.

## 🔐 Keamanan & Identitas
- **Custom SSO** — Integrasi otentikasi antar sistem internal SDMK.
- **JWT** — JSON Web Token untuk otorisasi API.
- **TLS/SSL** — Enkripsi trafik data.
- **Custome Enkripsi** - Custome Enkripsi saat pertukaran data sistem eksternal


## 🔄 Integrasi Sistem Eksternal
- **Hub Sumber Daya Manusia Kesehatan (HubSDMK)** — Konsumsi dan sinkronisasi data FIKTIF tenaga kesehatan.
- **Registrasi Nakes(RegNakes)** — Sistem FIKTIF lain yang terhubung via API.
- **Webhook** — Mekanisme komunikasi event-driven.

## 🛠️ DevOps & Tooling
- **GitHub** — SCM dan kolaborasi codebase.
- **GitHub Actions** — CI/CD pipeline otomatis.
- **Makefile** — Task runner untuk setup dan pengujian lokal.
- **PlantUML** — Diagram arsitektur dan flow dokumentasi teknis.
