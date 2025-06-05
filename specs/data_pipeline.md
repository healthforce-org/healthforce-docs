# Desain Data Pipeline dan Enkripsi di HealthForce

Dokumen ini menjelaskan arsitektur dan mekanisme pipeline data yang digunakan untuk pemrosesan, transformasi, dan enkripsi data dalam sistem HealthForce, khususnya data Sumber Daya Manusia Kesehatan (SDMK).

---

## 1. Tujuan Data Pipeline

- Mengumpulkan data dari berbagai sumber internal dan eksternal secara terstruktur.
- Melakukan transformasi data agar siap digunakan dalam analitik dan layanan backend.
- Memastikan keamanan data melalui enkripsi dan proteksi selama proses.
- Menyediakan pipeline yang scalable dan fault-tolerant.

---

## 2. Arsitektur Pipeline

- **Data Sources:**  
  Sistem eksternal seperti RegNakes, HubSDMK, SSO, dan internal services.
- **Ingestion Layer:**  
  Komponen yang menerima data mentah via API, webhook, atau batch upload.
- **Processing Layer:**  
  Melakukan validasi, pembersihan, transformasi, dan normalisasi data.
- **Storage Layer:**  
  Menyimpan data hasil proses di PostgreSQL dan/atau data warehouse.
- **Encryption Layer:**  
  Melakukan enkripsi data sensitif sebelum disimpan dan saat ditransfer.
- **Monitoring & Logging:**  
  Memantau performa pipeline, error, dan throughput data.

---

## 3. Komponen Utama

### 3.1 Data Ingestion

- Menggunakan REST API endpoint dan webhook listener untuk menerima data.
- Batch ingestion untuk data besar dengan jadwal periodik.
- Queue system (misal: RabbitMQ) untuk antrean data yang masuk agar asinkron dan handal.

### 3.2 Data Processing & Transformation

- Pembersihan data (data cleansing) seperti penghapusan duplikasi, format ulang, dan validasi.
- Transformasi data mengikuti skema yang disepakati untuk konsistensi.
- Enrichment data jika diperlukan, seperti penambahan metadata.

### 3.3 Enkripsi Data

- Data sensitif dienkripsi menggunakan AES-256 saat disimpan di database.
- Transfer data antar layanan menggunakan TLS/SSL.
- Penggunaan key management system (KMS) untuk penyimpanan dan rotasi kunci enkripsi.

### 3.4 Data Storage

- Penyimpanan data hasil proses di PostgreSQL sebagai database utama.
- Backup rutin dan replikasi untuk memastikan ketersediaan data.

### 3.5 Monitoring & Alerting

- Integrasi dengan Prometheus/Grafana untuk monitoring pipeline health dan performa.
- Alert otomatis pada kegagalan ingestion atau processing.
- Logging lengkap untuk audit trail dan troubleshooting.

---

## 4. Data Flow Overview

1. Sistem eksternal mengirimkan data melalui API atau webhook.
2. Data masuk ke ingestion layer dan dikelola dalam queue.
3. Data diproses dan divalidasi pada processing layer.
4. Data sensitif dienkripsi sebelum disimpan.
5. Data hasil transformasi disimpan di storage layer.
6. Sistem monitoring memantau seluruh pipeline.

---

## 5. Keamanan dan Kepatuhan

- Implementasi kontrol akses ketat pada pipeline dan storage.
- Audit trail untuk setiap akses dan perubahan data.
- Compliance dengan regulasi perlindungan data kesehatan nasional.

---

## 6. Rencana Pengujian dan Deployment

- Unit test untuk komponen ingestion, processing, dan enkripsi.
- Integrasi test dengan sistem eksternal dan backend.
- Load test untuk volume data besar dan peak load.
- Deployment bertahap dengan monitoring ketat pada production.

---

Dokumen ini akan diperbarui secara berkala seiring pengembangan dan pengoperasian pipeline data HealthForce.
