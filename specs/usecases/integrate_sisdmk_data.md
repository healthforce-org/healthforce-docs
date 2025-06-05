# 🧩 Use Case: Integrasi Data SDMK dari HubSDMK

## 🆔 ID Use Case
UC-INT-HubSDMK-004

## 🎯 Tujuan
Mengambil dan menyinkronkan data SDMK (Sumber Daya Manusia Kesehatan) dari sistem HubSDMK ke dalam sistem HealthForce secara berkala untuk pemutakhiran data tenaga kesehatan nasional.

## 👥 Aktor Terkait
- Sistem HubSDMK (eksternal)
- Backend HealthForce (ETL Service)
- Database SDMK
- Odoo Admin Dashboard
- Admin atau Operator SDMK (sebagai pemantau)

## 🗂️ Modul Terkait
- Backend Service: `etl-hubsdmk-service`, `sdmk-service`
- Odoo Module: `sdmk_integration_hubsdmk`, `sdmk_core`
- Integration Manager: `sdmk_integration_manager`

## 📚 Preconditions
- Endpoint API HubSDMK aktif dan dapat diakses.
- Akses token/credentials HubSDMK valid dan tersimpan dengan aman.
- Modul integrasi HubSDMK telah diaktifkan di `sdmk_integration_manager`.

## 🔁 Alur Normal (Main Flow)
1. Scheduler backend memicu proses ETL HubSDMK (misal setiap 6 jam).
2. `etl-hubsdmk-service` mengirim permintaan ke API HubSDMK berdasarkan parameter tertentu (misal wilayah kerja, update sejak waktu tertentu).
3. Data diterima dalam format standar, lalu dilakukan validasi awal dan transformasi (mapping ke struktur internal).
4. Data dikirim ke `sdmk-service` untuk disimpan di database HealthForce.
5. Dashboard Odoo otomatis menyegarkan data visualisasi SDMK.
6. Trace ID diterapkan sejak permintaan awal sampai penyimpanan akhir.
7. Seluruh proses dimonitor melalui Prometheus dan panel Grafana.

## 🔄 Alur Alternatif (Exception Flow)
- **Kondisi**: API HubSDMK tidak responsif
  - Sistem melakukan retry (maks 3x), lalu kirim alert jika tetap gagal.
- **Kondisi**: Format atau struktur data berubah
  - Data mentah disimpan sementara di blob/log
  - Admin diberi notifikasi via dashboard/alert
- **Kondisi**: Gagal autentikasi token
  - Sistem hentikan proses dan kirim alert
  - Tidak ada data yang diproses

## ✅ Postconditions
- Data SDMK terbaru dari HubSDMK tersimpan dan tersinkron dengan baik.
- Proses dicatat dalam audit log integrasi.
- Ringkasan status sinkronisasi tersedia di dashboard Odoo.

## 🧪 Validasi & Logika
- Validasi wajib: NIK, nama lengkap, status aktif.
- Penyesuaian data lokasi (mapping kode wilayah dari HubSDMK ke internal).
- Data lama akan di-*update* jika terdapat perubahan (berdasarkan `last_updated` timestamp).

## 🔐 Keamanan & Logging
- Token/credential HubSDMK disimpan di Secret Manager dan hanya diakses oleh service terkait.
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
- [Spesifikasi integrasi](../integration.md)
- [Spesifikasi data pipeline](../data_pipeline.md)
- [Spesifikasi keamanan](../security.md)
- [Modul Odoo](../odoo.md)
- [Diagram konteks HubSDMK](../diagrams/architecture/c4/context-diagram.puml)
