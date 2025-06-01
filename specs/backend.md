# 📦 Backend Specifications — HealthForce

Dokumen ini menjelaskan arsitektur, komponen utama, standar pengembangan, dan praktik terbaik dalam implementasi layanan backend HealthForce.

---

## 1. 🔧 Teknologi Utama

- **Bahasa:** Golang (Go 1.22+)
- **Framework:** Gin (untuk REST API)
- **Arsitektur:** Microservices
- **Database:** PostgreSQL
- **Caching & Pub/Sub:** Redis
- **Komunikasi antar layanan:** HTTP (RESTful API), gRPC (opsional)
- **Containerisasi:** Docker
- **Orkestrasi:** Kubernetes (GKE)
- **Monitoring:** Prometheus + Grafana

---

## 2. 🧱 Struktur Arsitektur Layanan

- **API Gateway**  
  Titik masuk utama untuk request dari client atau dashboard admin. Mengatur routing dan forwarding ke layanan internal.

- **Auth Service**  
  Menangani login, SSO, validasi JWT, dan manajemen otorisasi.

- **User Service**  
  Mengelola informasi pengguna, termasuk SDMK, Nakes, dan admin dashboard.

- **Sync Service**  
  Bertugas menarik data eksternal secara berkala (pull), menerima webhook, dan menyinkronkan data dengan standar internal.

- **Analytics Service**  
  Mengagregasi data dan menghasilkan laporan atau summary yang dapat digunakan untuk keperluan kebijakan.

---

## 3. 📂 Struktur Folder Layanan (Per-Service)

```bash
cmd/
  main.go            # Entry point aplikasi
internal/
  handler/           # Handler HTTP/gRPC
  service/           # Business logic utama
  repository/        # Akses database (PostgreSQL)
  model/             # Struktur data dan DTO
  middleware/        # Middleware umum (logging, auth, dll)
pkg/
  utils/             # Fungsi utilitas
  config/            # Konfigurasi environment
