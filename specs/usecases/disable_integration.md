# 🧩 Use Case: Menonaktifkan Sementara Integrasi Sistem Eksternal

## 🆔 ID Use Case
UC-INT-MGMT-005

## 🎯 Tujuan
Memberikan kemampuan kepada admin untuk menonaktifkan sementara integrasi dengan sistem eksternal (seperti RegNakes atau HubSDMK) saat terjadi kendala operasional, pemeliharaan, atau sistem eksternal tidak tersedia.

## 👥 Aktor Terkait
- Admin SDMK (pengelola integrasi)
- Backend HealthForce
- Modul Odoo Admin Dashboard
- Sistem eksternal (RegNakes, HubSDMK, dll)

## 🗂️ Modul Terkait
- Backend Service: `integration-manager-service`, service integrasi terkait (`etl-regnakes-service`, `etl-hubsdmk-service`)
- Odoo Module: `sdmk_integration_manager`

## 📚 Preconditions
- Admin memiliki hak akses untuk melakukan konfigurasi integrasi.
- Modul `sdmk_integration_manager` aktif dan berjalan normal.
- Setiap integrasi memiliki status aktif/non-aktif yang bisa dikontrol.

## 🔁 Alur Normal (Main Flow)
1. Admin membuka halaman "Pengaturan Integrasi" di Odoo.
2. Admin memilih sistem eksternal yang ingin dinonaktifkan (misalnya RegNakes).
3. Admin menekan tombol "Nonaktifkan".
4. Backend menyimpan status integrasi menjadi `inactive` di database internal.
5. Semua service integrasi terkait akan membaca status ini sebelum melakukan proses sinkronisasi.
6. Jika integrasi dalam kondisi non-aktif, proses otomatis (scheduler) akan dilewati.
7. Notifikasi status integrasi akan ditampilkan pada dashboard.

## 🔄 Alur Alternatif (Exception Flow)
- **Kondisi**: Admin tidak memiliki hak akses
  - Sistem menolak perubahan dan menampilkan pesan error
- **Kondisi**: Backend gagal menyimpan status
  - Ditampilkan pesan kesalahan dan perubahan dibatalkan
  - Log error dikirim ke monitoring system

## ✅ Postconditions
- Status integrasi berubah menjadi non-aktif.
- Proses ETL yang dijadwalkan untuk integrasi tersebut dihentikan sementara.
- Status terbaru dapat dilihat di dashboard integrasi.

## 🧪 Validasi & Logika
- Perubahan hanya dapat dilakukan oleh role `admin` atau `superuser`.
- Setiap integrasi memiliki flag status aktif/non-aktif di database.
- Log perubahan status tercatat dengan timestamp dan user_id.

## 🔐 Keamanan & Logging
- Semua perubahan status direkam dalam audit log (user, waktu, sistem).
- Perubahan hanya bisa dilakukan melalui UI atau API resmi dengan token otentikasi.
- Status integrasi tidak bisa dimanipulasi langsung di level service tanpa otorisasi.

## 📈 Monitoring & Observabilitas
- Panel Grafana menampilkan status aktif/non-aktif masing-masing integrasi.
- Alert dapat dikirim jika integrasi dinonaktifkan dalam waktu lama (> 3 hari).
- Semua proses sinkronisasi mencatat status "skipped" saat integrasi nonaktif.

## 🧩 Referensi Terkait
- [Spesifikasi Odoo](../odoo.md)
- [Spesifikasi integrasi](../integration.md)
- [Spesifikasi keamanan](../security.md)
- [Panduan API](../api-guidelines.md)
- [Diagram konteks integrasi](../diagrams/architecture/c4/context-diagram.puml)
