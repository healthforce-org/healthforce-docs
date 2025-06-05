# 🧩 Use Case: User Management dan Role Assignment di HealthForce

## 🆔 ID Use Case  
UC-USER-001

## 🎯 Tujuan  
Memungkinkan admin sistem HealthForce untuk mengelola profil pengguna internal SDMK serta mengatur hak akses dan peran (role) sesuai kebutuhan operasional.

## 👥 Aktor Terkait  
- Admin SDMK (superuser di sistem)  
- Backend Golang (API Gateway)  
- Modul Odoo Admin Dashboard  
- Database PostgreSQL  

## 🗂️ Modul Terkait  
- Backend Service: `user-service`  
- Odoo Module: `sdmk_core`  

## 📚 Preconditions  
- Admin telah terautentikasi dan memiliki hak akses yang cukup untuk manajemen user.  
- Sistem User Management sudah berjalan dan terhubung dengan database.  

## 🔁 Alur Normal (Main Flow)  
1. Admin membuka dashboard user management di Odoo.  
2. Admin melakukan pencarian atau memilih pengguna yang ingin diubah/profil dibuat baru.  
3. Admin mengisi/memperbarui data profil seperti nama, email, NIP, unit kerja.  
4. Admin memilih role pengguna dari daftar role yang tersedia (misal: admin, operator, verifikator).  
5. Admin menyimpan perubahan.  
6. Backend memvalidasi data dan menyimpan ke database.  
7. Sistem mengirim notifikasi sukses ke admin.  

## 🔄 Alur Alternatif (Exception Flow)  
- **Kondisi**: Data input tidak valid (misal email tidak sesuai format)  
  - Sistem menolak perubahan dengan pesan error validasi.  
- **Kondisi**: Admin tidak memiliki hak akses yang cukup  
  - Sistem menolak permintaan dengan pesan "Unauthorized".  
- **Kondisi**: Konflik data (misal NIP sudah terdaftar)  
  - Sistem menampilkan error "Data duplikat".  

## ✅ Postconditions  
- Data profil pengguna berhasil tersimpan dengan role yang sesuai.  
- Perubahan data tercatat dalam audit log.  

## 🧪 Validasi & Logika  
- Email harus valid dan domain sesuai kebijakan.  
- Role yang diberikan harus ada di daftar role yang sudah didefinisikan.  
- Tidak boleh ada duplikasi NIP atau username.  

## 🔐 Keamanan & Logging  
- Akses ke fitur manajemen user hanya untuk role admin.  
- Semua perubahan dicatat di audit trail dengan user pembuat, waktu, dan perubahan data.  

## 📈 Monitoring & Observabilitas  
- Aktivitas manajemen user dicatat dan dipantau lewat Prometheus/Grafana.  
- Alert untuk percobaan akses tidak sah.  

## 🧩 Referensi Terkait  
- [Modul Odoo user management](../odoo.md)  
- [Panduan API user management](../api-guidelines.md)  
- [Audit logging use case](./data_audit_logging.md)  
