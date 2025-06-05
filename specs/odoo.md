# 🧩 Spesifikasi Teknis Odoo - Modul Admin Dashboard HealthForce

Dokumen ini menjelaskan struktur, desain, dan alur teknis implementasi Odoo sebagai admin dashboard dalam sistem HealthForce.

---

## 📌 Tujuan

Odoo digunakan sebagai dashboard untuk kebutuhan operasional SDMK, seperti:
- Validasi data SDMK dari sistem eksternal.
- Monitoring status integrasi.
- Manajemen pengguna internal.

---

## 🏗️ Struktur Modul

Modul dikembangkan dalam direktori `odoo_addons/` dengan struktur sebagai berikut:
```
sdmk_core/
├── models/
│ └── *.py # Model dan logika bisnis
├── views/
│ └── *.xml # UI tampilan
├── data/
│ └── *.xml/csv # Default data (mis. role, status)
├── security/
│ └── ir.model.access.csv
├── init.py
└── manifest.py
```

---

## 📦 Modul Modular

Untuk menjaga skalabilitas dan fleksibilitas, modul Odoo dibagi menjadi beberapa bagian:

- `sdmk_core`: Modul utama yang menyimpan struktur data inti SDMK.
- `sdmk_integration_regnakes`: Untuk integrasi data tenaga kesehatan dari RegNakes.
- `sdmk_integration_hubsdmk`: Untuk integrasi data dari HubSDMK.
- `sdmk_integration_manager`: Untuk kontrol status integrasi sistem eksternal (enable/disable per service).
- `sdmk_admin`: Modul fitur admin non-SDMK seperti pengguna internal, logs, audit, dll.

---

## 🔄 Alur Integrasi dengan Backend Golang

1. Backend menerima data dari sistem eksternal (RegNakes/HubSDMK).
2. Backend mengirim data ke Odoo via REST API endpoint khusus.
3. Odoo melakukan validasi, pencatatan, dan update record.
4. Odoo memberi response (berhasil/gagal) ke backend.

Contoh endpoint di Odoo:
```
POST /api/sdmk/person/sync
Authorization: Bearer <jwt>
Content-Type: application/json
```

---

## 🔐 Hak Akses & Role

Hak akses diatur dalam file `ir.model.access.csv` dengan grup pengguna:
- `Admin Integrasi`: Bisa memantau dan mengelola data integrasi.
- `Verifikator`: Hanya bisa melihat & memverifikasi data SDMK.
- `User Internal`: Akses terbatas pada data non-SDMK.

---

## 💡 Praktik Baik Teknis

- **Isolasi logika** ke dalam service di folder `models/services/`.
- **Gunakan `@api.model` dan `@api.depends`** sesuai kebutuhan.
- **Audit trail** disimpan ke model `integration.log` atau `audit.log`.

---

## 📡 Monitoring & Tracing

Untuk meningkatkan observabilitas sistem secara end-to-end, HealthForce menggunakan **trace ID** yang diturunkan dari sistem eksternal, dikirim melalui API Gateway (Golang), dan diteruskan ke Odoo.

### 🔍 Alur Trace ID:
1. Sistem eksternal mengirim permintaan ke API Gateway (Golang).
2. Golang membuat `trace_id` unik (UUID).
3. `trace_id` disisipkan ke header atau payload API ke Odoo.
4. Odoo menyimpan dan/atau memproses `trace_id` bersama data.
5. `trace_id` digunakan dalam log, audit trail, dan monitoring.

### 🛠 Implementasi di Odoo:
- Model seperti `healthforce.sdmk_person` atau `integration.log` memiliki field `trace_id`.
- Setiap log integrasi mencatat `trace_id`.
- Error & warning log mencantumkan trace ID (bisa dicari di monitoring).
- Logging diarahkan ke `ir.logging` dan bisa dikirim ke Prometheus.

Contoh model:
```python
class IntegrationLog(models.Model):
    _name = 'integration.log'
    _description = 'Log Integrasi Sistem Eksternal'

    trace_id = fields.Char(string="Trace ID")
    status = fields.Selection([('success', 'Success'), ('fail', 'Fail')], default='success')
    message = fields.Text()
