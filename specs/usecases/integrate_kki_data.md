# 🧩 Use Case: Integrasi Data Tenaga Kesehatan dari KKI

## 🆔 ID Use Case
UC-INT-KKI-003

## 🎯 Tujuan
Mengambil dan menyinkronkan data tenaga kesehatan dari sistem KKI (Konsil Kedokteran Indonesia) ke dalam sistem HealthForce secara berkala untuk memperkaya dan memverifikasi informasi SDMK.

## 👥 Aktor Terkait
- Sistem KKI (eksternal)
- Backend HealthForce (ETL Service)
- Database SDMK
- Odoo Admin Dashboard
- Admin atau Operator SDMK (sebagai pemantau)

## 🗂️ Modul Terkait
- Backend Service: `etl-kki-service`, `sdmk-service`
- Odoo Module: `sdmk_integration_kki`, `sdmk_core`
- Integration Manager: `sdmk_integration_manager`

## 📚 Preconditions
- Endpoint API KKI dapat diakses dan tersedia.
- Token otorisasi KKI masih valid atau dapat diperbarui.
- Modul integrasi KKI telah diaktifkan melalui `sdmk_integration_manager`.

## 🔁 Alur Normal (Main Flow)
1. Scheduler backend menjalankan proses fetch data dari KKI (interval bisa diatur, mis: harian).
2. ETL Service mengirim request ke endpoint API KKI dengan parameter tertentu (tanggal, jenis profesi, dsb).
3. Data diterima dalam format JSON lalu diproses (transformasi: mapping field, sanitasi data, validasi awal).
4. Data hasil transformasi dikirim ke `sdmk-service` untuk disimpan di database SDMK HealthForce.
5. Odoo menerima notifikasi sinkronisasi untuk memperbarui view dashboard SDMK.
6. Proses integrasi disertai `trace_id` untuk setiap batch yang diproses.
7. Monitoring mencatat hasil proses (jumlah data, error, waktu proses) di Prometheus dan tampil di Grafana.

## 🔄 Alur Alternatif (Exception Flow)
- **Kondisi**: Endpoint KKI tidak dapat dijangkau
  - Retry otomatis dilakukan (maksimal 3x)
  - Jika tetap gagal → alarm dikirim via Grafana/Slack
- **Kondisi**: Format data dari KKI tidak sesuai
  - Log error dengan deskripsi lengkap dikirim ke log pipeline
  - Baris data yang gagal tetap dicatat tanpa menghentikan proses
- **Kondisi**: Token expired
  - Sistem mencoba refresh token, jika gagal → hentikan proses dan beri alert

## ✅ Postconditions
- Data tenaga kesehatan dari KKI berhasil disimpan atau diperbarui di database SDMK HealthForce.
- Proses integrasi tercatat dalam log audit integrasi.
- Jumlah dan status sinkronisasi ditampilkan di halaman admin dashboard.

## 🧪 Validasi & Logika
- Nomor STR harus valid dan aktif.
- Data yang sudah pernah disinkron akan di-*update*, bukan dibuat ulang.
- Nama, NIK, tempat lahir digunakan sebagai fallback matching jika STR tidak unik.

## 🔐 Keamanan & Logging
- Token KKI disimpan di Vault / Secret Manager dan hanya digunakan oleh `etl-kki-service`.
- Semua proses disertai trace ID: `X-Request-ID` untuk pelacakan end-to-end.
- Audit log per batch integrasi mencatat: timestamp, jumlah data, success/error count.

## 📈 Monitoring & Observabilitas
- Panel Grafana untuk:
  - Status integrasi harian
  - Jumlah data yang berhasil/gagal
  - Durasi integrasi
- Trace ID digunakan untuk menelusuri error sampai ke request API eksternal.
- Alert jika data gagal melebihi threshold 20% dalam 1 batch.

## 🧩 Referensi Terkait
- [Integrasi eksternal](../integration.md)
- [Modul Odoo terkait SDMK](../odoo.md)
- [Panduan pipeline](../data_pipeline.md)
- [Panduan keamanan](../security.md)
- [Diagram konteks integrasi KKI](../diagrams/architecture/c4/context-diagram.puml)
