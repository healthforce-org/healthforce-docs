# 🧩 Use Case: User Self-Registration untuk Koreksi Data Eksternal

## 🆔 ID Use Case
UC-SELFREG-001

## 🎯 Tujuan
Memungkinkan pengguna (tenaga kesehatan) melakukan pendaftaran mandiri untuk memperbaiki atau melengkapi data mereka yang berasal dari sistem eksternal seperti KKI atau SISDMK.

## 👥 Aktor Terkait
- Tenaga kesehatan (user eksternal)
- Backend Golang (API Gateway)
- Modul Odoo Admin Dashboard (`sdmk_core`)

## 🗂️ Modul Terkait
- Backend Service: `user-service`
- Odoo Module: `sdmk_core`

## 📚 Preconditions
- Data dasar pengguna sudah tersedia dari sistem eksternal tapi perlu validasi atau koreksi.
- Pengguna belum memiliki akun aktif di HealthForce.

## 🔁 Alur Normal (Main Flow)
1. Pengguna mengakses halaman pendaftaran self-registration.
2. Mengisi formulir dengan data yang diperlukan (misal NIK, nomor registrasi KKI).
3. Sistem memverifikasi data awal dengan data eksternal.
4. Pengguna mengirimkan permintaan koreksi atau update data.
5. Backend menyimpan permintaan dan menunggu validasi admin.
6. Admin Odoo memvalidasi dan menyetujui koreksi data.
7. Data pengguna diperbarui dan akun aktif dibuat jika valid.

## 🔄 Alur Alternatif (Exception Flow)
- Data tidak ditemukan di sistem eksternal → Tampilkan pesan error.
- Data sudah terdaftar → Tampilkan opsi login atau kontak admin.
- Koreksi data ditolak oleh admin → Notifikasi ke pengguna.

## ✅ Postconditions
- Data pengguna diperbaiki sesuai validasi.
- Akun HealthForce dibuat dan dapat digunakan untuk login.

## 🔐 Keamanan & Logging
- Verifikasi input wajib dengan CAPTCHA.
- Semua permintaan koreksi tercatat di audit log.

## 📈 Monitoring & Observabilitas
- Jumlah permintaan koreksi per hari.
- Waktu proses validasi admin.

## 🧩 Referensi Terkait
- [Integrasi SISDMK dan KKI](../specs/integration.md)
- [Modul Odoo sdmk_core](../specs/odoo.md)
