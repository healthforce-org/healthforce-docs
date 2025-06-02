# 🧩 Use Case: Audit Logging untuk Data SDMK

## 🆔 ID Use Case
UC-DATA-AUDIT-001

## 🎯 Tujuan
Mencatat seluruh perubahan data penting SDMK (seperti data tenaga kesehatan) untuk keperluan audit, keamanan, dan penelusuran.

## 👥 Aktor Terkait
- Admin SDMK
- Backend HealthForce
- Modul Odoo `sdmk_core`
- Tim audit dan keamanan

## 🗂️ Modul Terkait
- Backend Service: `sdmk-core-service`
- Odoo Module: `sdmk_core`

## 📚 Preconditions
- User memiliki hak akses untuk melakukan perubahan data.
- Sistem audit logging telah diaktifkan dan berjalan.
- Database mendukung penyimpanan audit trail.

## 🔁 Alur Normal (Main Flow)
1. User melakukan perubahan data SDMK melalui dashboard Odoo atau API backend.
2. Sistem backend menerima request dan memvalidasi perubahan.
3. Setelah berhasil diubah, sistem mencatat log audit berisi:
   - Identitas user (user_id)
   - Waktu perubahan (timestamp)
   - Jenis perubahan (create, update, delete)
   - Data sebelum dan sesudah perubahan (old vs new)
4. Log audit disimpan di tabel khusus audit trail di database.
5. Admin atau tim audit dapat mengakses laporan audit melalui dashboard.

## 🔄 Alur Alternatif (Exception Flow)
- **Kondisi**: Penyimpanan log audit gagal
  - Sistem mencatat error pada log server
  - Operasi data tetap dilanjutkan (tapi dengan peringatan)
- **Kondisi**: User tanpa hak akses mengakses audit log
  - Sistem menolak akses dan menampilkan pesan error

## ✅ Postconditions
- Semua perubahan data penting tercatat dengan lengkap.
- Audit trail dapat ditelusuri dan diverifikasi.
- Data audit terlindungi dari manipulasi tidak sah.

## 🧪 Validasi & Logika
- Log audit hanya mencatat data yang dianggap kritikal.
- Perubahan yang bersifat read-only tidak dicatat.
- Audit log bersifat immutable (tidak bisa diubah).

## 🔐 Keamanan & Logging
- Akses audit log hanya untuk role admin dan auditor.
- Data log dienkripsi saat disimpan.
- Semua akses ke audit log dicatat juga dalam sistem logging.

## 📈 Monitoring & Observabilitas
- Monitoring health sistem audit logging di Datadog.
- Alert jika terjadi kegagalan penyimpanan audit log lebih dari 5 menit.
- Statistik jumlah perubahan data per hari ditampilkan di dashboard.

## 🧩 Referensi Terkait
- [Spesifikasi Backend](../backend.md)
- [Spesifikasi Odoo](../odoo.md)
- [Spesifikasi Security](../security.md)
- [Panduan API](../api-guidelines.md)
