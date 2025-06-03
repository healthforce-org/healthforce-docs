# 🧩 Use Case: Integrasi Data SDMK dari SISDMK

## 🆔 ID Use Case
UC-INT-SISDMK-004

## 🎯 Tujuan
Mengambil dan menyinkronkan data SDMK (Sumber Daya Manusia Kesehatan) dari sistem SISDMK ke dalam sistem HealthForce secara berkala untuk pemutakhiran data tenaga kesehatan nasional.

## 👥 Aktor Terkait
- Sistem SISDMK (eksternal)
- Backend HealthForce (ETL Service)
- Database SDMK
- Odoo Admin Dashboard
- Admin atau Operator SDMK (sebagai pemantau)

## 🗂️ Modul Terkait
- Backend Service: `etl-sisdmk-service`, `sdmk-service`
- Odoo Module: `sdmk_integration_sisdmk`, `sdmk_core`
- Integration Manager: `sdmk_integration_manager`

## 📚 Preconditions
- Endpoint API SISDMK aktif dan dapat diakses.
- Akses token/credentials SISDMK valid dan tersimpan dengan aman.
- Modul integrasi SISDMK telah diaktifkan di `sdmk_integration_manager`.

## 🔁 Alur Normal (Main Flow)
1. Scheduler backend memicu proses ETL SISDMK (misal setiap 6 jam).
2. `etl-sisdmk-service` mengirim permintaan ke API SISDMK berdasarkan parameter tertentu (misal wilayah kerja, update sejak waktu tertentu).
3. Data diterima dalam format standar, lalu dilakukan validasi awal dan transformasi (mapping ke struktur internal).
4. Data dikirim ke `sdmk-service` untuk disimpan di database HealthForce.
5. Dashboard Odoo otomatis menyegarkan data visualisasi SDMK.
6. Trace ID diterapkan sejak permintaan awal sampai penyimpanan akhir.
7. Seluruh proses dimonitor melalui Prometheus dan panel Grafana.

## 🔄 Alur Alternatif (Exception Flow)
- **Kondisi**: API SISDMK tidak responsif
  - Sistem melakukan retry (maks 3x), lalu kirim alert jika tetap gagal.
- **Kondisi**: Format atau struktur data berubah
  - Data mentah disimpan sementara di blob/log
  - Admin diberi notifikasi via dashboard/alert
- **Kondisi**: Gagal autentikasi token
  - Sistem hentikan proses dan kirim alert
  - Tidak ada data yang diproses

## ✅ Postconditions
- Data SDMK terbaru dari SISDMK tersimpan dan tersinkron dengan baik.
- Proses dicatat dalam audit log integrasi.
- Ringkasan status sinkronisasi tersedia di dashboard Odoo.

## 🧪 Validasi & Logika
- Validasi wajib: NIK, nama lengkap, status aktif.
- Penyesuaian data lokasi (mapping kode wilayah dari SISDMK ke internal).
- Data lama akan di-*update* jika terdapat perubahan (berdasarkan `last_updated` timestamp).

## 🔐 Keamanan & Logging
- Token/credential SISDMK disimpan di Secret Manager dan hanya diakses oleh service terkait.
- Semua request diberi header `X-Request-ID` untuk tracing end-to-end.
- Audit log integrasi mencatat: waktu, jumlah baris, ID batch, status proses.

## 📈 Monitoring & Observabilitas
- Prometheus metrics:
  - Jumlah data sinkron per batch
  - Durasi proses sinkronisasi
  - Error rate per endpoint
- Grafana alert jika:
  - Proses gagal berulang dalam 3 batch
  - Durasi > threshold (misal 5 menit)
- Trace ID digunakan untuk debug lintas sistem.

## 🧩 Referensi Terkait
- [Spesifikasi integrasi](../specs/integration.md)
- [Spesifikasi data pipeline](../specs/data_pipeline.md)
- [Spesifikasi keamanan](../specs/security.md)
- [Modul Odoo](../specs/odoo.md)
- [Diagram konteks SISDMK](../diagrams/architecture/c4/context-diagram.puml)
