# 🧩 Use Case: Manajemen Data SDMK

## 🆔 ID Use Case
UC-SDMK-002

## 🎯 Tujuan
Menyediakan fitur pengelolaan data SDMK (Sumber Daya Manusia Kesehatan) melalui dashboard admin HealthForce, termasuk pencarian, pemutakhiran, dan verifikasi data tenaga kesehatan.

## 👥 Aktor Terkait
- Admin SDMK pusat
- Operator daerah
- Backend Golang (API Service)
- Modul Odoo Admin Dashboard
- Database SDMK Terpadu

## 🗂️ Modul Terkait
- Backend Service: `sdmk-service`
- Odoo Module: `sdmk_core`, `sdmk_integration_regnakes`, `sdmk_integration_hubsdmk`, `sdmk_integration_manager`,
`sdmk_admin`

## 📚 Preconditions
- Pengguna telah login melalui SSO dan memiliki peran yang sesuai.
- Database SDMK telah terisi minimal sebagian dari integrasi RegNakes atau HubSDMK.
- Hak akses pengguna sesuai dengan wilayah kerja atau level administratifnya.

## 🔁 Alur Normal (Main Flow)
1. Pengguna membuka halaman manajemen data SDMK di dashboard Odoo.
2. Sistem menampilkan daftar data SDMK berdasarkan filter default (wilayah, status, profesi).
3. Pengguna melakukan pencarian atau filter tambahan (mis: berdasarkan STR, nama, fasilitas kesehatan).
4. Pengguna memilih salah satu entri untuk melihat detail.
5. Jika dibutuhkan, pengguna dapat melakukan update data seperti status aktif, perubahan fasilitas, atau unggah dokumen.
6. Semua perubahan dikirim ke backend `sdmk-service` untuk disimpan ke database pusat.
7. Trace ID dibawa dari frontend sampai backend untuk keperluan logging dan observabilitas.

## 🔄 Alur Alternatif (Exception Flow)
- **Kondisi**: Data tidak ditemukan
  - Sistem menampilkan pesan "Data SDMK tidak ditemukan"
  - Catatan query disimpan ke log
- **Kondisi**: Pengguna mencoba mengakses data di luar wilayah kewenangannya
  - Sistem menolak akses dan mencatat audit log
- **Kondisi**: Update data gagal karena validasi
  - Sistem menampilkan pesan error spesifik (mis: "STR tidak valid")

## ✅ Postconditions
- Perubahan data SDMK berhasil disimpan dan tercermin di dashboard.
- Audit log tercatat untuk setiap perubahan data.
- ID perubahan/log dilacak melalui `trace_id`.

## 🧪 Validasi & Logika
- STR harus valid dan masih berlaku (jika data berasal dari RegNakes).
- Fasilitas kesehatan harus terdaftar resmi.
- Status kepegawaian tidak boleh kosong.
- Validasi dilakukan di level frontend dan backend.

## 🔐 Keamanan & Logging
- Hanya pengguna dengan peran `admin_sdmk` atau `operator_daerah` yang dapat mengubah data.
- Audit log mencatat: user, timestamp, field yang diubah, nilai sebelum/sesudah.
- Semua akses disertai `trace_id`.

## 📈 Monitoring & Observabilitas
- Aktivitas perubahan data dimonitor melalui Prometheus (jumlah perubahan, status success/fail).
- `trace_id` dikirim dari Odoo → API Gateway → Backend → DB log.
- Panel Grafana:
  - Jumlah perubahan per wilayah
  - Rasio sukses/gagal update
  - Durasi rata-rata pengambilan dan penyimpanan data

## 🧩 Referensi Terkait
- [Modul Odoo terkait SDMK](../odoo.md)
- [Integrasi RegNakes & HubSDMK](../integration.md)
- [Panduan API](../api-guidelines.md)
- [Diagram konteks SDMK](../diagrams/architecture/c4/container-diagram.puml)
