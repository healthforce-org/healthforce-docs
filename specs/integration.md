# Integrasi Sistem Eksternal di HealthForce

Dokumen ini menjelaskan desain dan mekanisme integrasi antara sistem HealthForce dengan sistem eksternal utama, yaitu Konsil Kesehatan Indonesia (KKI), Sistem Informasi Sumber Daya Manusia Kesehatan (SISDMK), dan Single Sign-On (SSO).

---

## 1. Overview Integrasi

HealthForce mengimplementasikan integrasi yang aman, handal, dan scalable untuk mengkonsumsi data serta berinteraksi dengan sistem eksternal. Integrasi ini memungkinkan sinkronisasi data tenaga kesehatan, validasi identitas, dan otentikasi pengguna secara terpusat.

---

## 2. Sistem Eksternal

### 2.1 Konsil Kesehatan Indonesia (KKI)
- **Tujuan Integrasi:**  
  Mendapatkan data validasi dan sertifikasi tenaga kesehatan yang telah diverifikasi oleh KKI.
- **Metode Komunikasi:**  
  REST API dengan autentikasi token API.
- **Data yang Diakses:**  
  Informasi registrasi, status sertifikasi, dan histori pelatihan tenaga kesehatan.
- **Frekuensi Sinkronisasi:**  
  Batch harian dan realtime (webhook) untuk event penting.
- **Penanganan Error:**  
  Retry dengan exponential backoff, fallback ke data cache lokal jika API KKI down.

### 2.2 Sistem Informasi Sumber Daya Manusia Kesehatan (SISDMK)
- **Tujuan Integrasi:**  
  Sinkronisasi data administrasi dan profil tenaga kesehatan di seluruh fasilitas kesehatan.
- **Metode Komunikasi:**  
  REST API dan Webhook untuk event-driven updates.
- **Data yang Diakses:**  
  Data personal, data kerja, riwayat pendidikan, dan sertifikasi.
- **Frekuensi Sinkronisasi:**  
  Sinkronisasi periodik (harian) dan real-time event subscription.
- **Penanganan Error:**  
  Queue retry dan alert jika terjadi kegagalan sinkronisasi.

### 2.3 Single Sign-On (SSO)
- **Tujuan Integrasi:**  
  Menyediakan otentikasi terpusat bagi pengguna HealthForce dan sistem terkait.
- **Metode Komunikasi:**  
  OAuth 2.0 / OpenID Connect untuk autentikasi dan otorisasi.
- **Fungsi:**  
  Menghasilkan dan memvalidasi token JWT yang digunakan untuk otorisasi API.
- **Keamanan:**  
  Penggunaan TLS/SSL, validasi token, dan scope-based access control.

---

## 3. Arsitektur Integrasi

- Backend HealthForce sebagai API Gateway yang meneruskan permintaan ke microservices terkait.
- Modul `sdmk_integration_manager` mengatur lifecycle integrasi: enable/disable koneksi, monitoring status, dan log.
- Penyimpanan sementara data dari sistem eksternal pada PostgreSQL dengan mekanisme enkripsi.
- Penggunaan message queue (RabbitMQ / Kafka) untuk menangani sinkronisasi asinkron dan retry mekanisme.

---

## 4. Mekanisme Data Flow

1. **Data Request:**  
   Backend HealthForce mengirimkan request API ke sistem eksternal dengan header autentikasi.
2. **Response Processing:**  
   Data diterima dan divalidasi, lalu disimpan ke database internal.
3. **Event Handling:**  
   Jika ada webhook event dari sistem eksternal, diterima dan diproses melalui queue.
4. **Error Handling & Retry:**  
   Jika gagal, log error dan retry otomatis dengan interval yang bertambah.
5. **Tracing & Monitoring:**  
   Setiap request dilengkapi ID tracing untuk logging dan monitoring menggunakan Prometheus dan Grafana.

---

## 5. Keamanan dan Compliance

- Semua komunikasi menggunakan HTTPS/TLS.
- Data sensitif dienkripsi saat penyimpanan dan transfer.
- Audit log setiap transaksi data dengan waktu dan user terkait.
- Sesuai dengan kebijakan keamanan dan regulasi kesehatan nasional.

---

## 6. Rencana Pengujian Integrasi

- Unit testing untuk tiap modul integrasi.
- End-to-end testing dengan simulasi API eksternal.
- Load testing untuk melihat performa sinkronisasi batch.
- Monitoring real-time error dan alert di lingkungan staging dan production.

---

## 7. Roadmap Pengembangan

- Fase 1: Integrasi dasar REST API untuk KKI dan SISDMK.
- Fase 2: Implementasi webhook dan event-driven sync.
- Fase 3: Integrasi SSO dengan OAuth 2.0.
- Fase 4: Penguatan mekanisme monitoring, tracing, dan fallback.

---

Dokumen ini akan diperbarui sesuai dengan perkembangan implementasi dan kebutuhan operasional sistem.

