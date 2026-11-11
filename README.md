# Abdullah Ubaid - PABWE 2026 Praktikum 8 (Laravel 13, Inertia.js & React) — TodoList Pro

Aplikasi Todolist modern dengan arsitektur **Clean Architecture & Feature-First Approach** sesuai standar industri, dibangun menggunakan **Laravel 13**, **Inertia.js**, **React**, basis data **PostgreSQL (`pabwe_p8_todolist`)**, upload/export spreadsheet via **PhpOffice/PhpSpreadsheet**, integrasi kecerdasan buatan (**OpenAI AI Content Generation**), rate limiting (1000 per detik), proteksi kepemilikan middleware, force HTTPS, dan **100% Test Coverage**.

---

## 🚀 Fitur Utama

1. **Clean Architecture & Feature-First Approach**:
   - Modul diorganisir per feature/domain (`App\Features\Auth`, `App\Features\Todo`, `App\Features\Admin`, `App\Features\Ai`, `App\Features\Profile`).
   - Menerapkan Dependency Inversion Principle (DIP) dengan repository interface (`TodoRepositoryInterface`, `TodoGroupRepositoryInterface`, `AiClientInterface`).
   - Pemisahan lapisan yang jelas antara Actions, Repositories, Services, DTOs, Policies, Controllers, dan React Components (Inertia.js).

2. **Kelompok Todo (`TodoGroup`) & Template Publik**:
   - **Manajemen Kelompok**: Pengguna dapat membuat kelompok todo (misal: "Proyek Skripsi", "Belajar Frontend", "Rutinitas Harian") dengan deskripsi dan status publik.
   - **Template Publik (`is_public`)**: Kelompok todo dapat dijadikan template publik yang dapat dibagikan kepada umum.
   - **Akses Publik Tanpa Login**: Pengunjung umum (guest) dapat menjelajahi galeri template publik di `/templates` dan melihat detail template beserta daftar todo di dalamnya di `/templates/{todoGroup}`.
   - **Kloning Template Publik**: Pengguna yang telah login dapat mengkloning template publik menjadi kelompok todo dan daftar tugas pribadi miliknya hanya dengan satu klik (`POST /templates/{todoGroup}/clone`).

3. **UI Modern (List View, Vertical Dots Menu & Stat Summary)**:
   - **List View**: Daftar todolist disajikan dalam bentuk daftar list padat dan rapi (bukan format kartu/card besar).
   - **Ringkas & Bersih**: Baris daftar utama **tidak menampilkan deskripsi** panjang sehingga daftar tugas tetap bersih dan mudah dipindai secara visual. Deskripsi lengkap dapat dilihat dengan membuka modal detail.
   - **Menu Tindakan Kebab (*Vertical Dots `⋮`*)**: Menggantikan tombol berjejer dengan satu tombol vertical dots yang membuka dropdown menu interaktif:
     - 👁️ **Detail**: Warna primary (biru), menampilkan detail lengkap modal tugas.
     - ✏️ **Ubah Edit**: Warna primary (biru), membuka modal edit todo.
     - 🗑️ **Hapus**: Warna merah (*rose*), memunculkan dialog konfirmasi hapus.
   - **Kartu Statistik (*Todo Stats Summary*)**: Menampilkan rekapitulasi data tugas secara real-time:
     - **Total Todo**: Jumlah seluruh tugas pengguna.
     - **Total Selesai**: Jumlah tugas berstatus `completed`.
     - **Belum Selesai**: Jumlah tugas aktif yang belum diselesaikan (`pending`/`in_progress`).
   - **Modal Header & Footer Berwarna**: Setiap modal dirancang dengan latar header berwarna kontras (aksen Indigo) dan footer berwarna terpisah (aksen Slate/Gray) agar navigasi aksi lebih intuitif.

4. **Autentikasi & Autorisasi Role-Based**:
   - **Login Fleksibel**: Pengguna dapat masuk menggunakan **Username** atau **Email**.
   - **ID Form Login Standar**: Form login menggunakan `id="login-username"`, `id="login-password"`, dan `id="login-submit"`.
   - **Role `admin`**:
     - Mengakses halaman Manajemen Pengguna (`/admin/users`).
     - Melihat daftar seluruh pengguna beserta username, email, dan jumlah todolist mereka.
     - Melihat detail todolist pengguna lain dalam mode **Read-Only** (`/admin/users/{user}/todos`).
     - Admin **tidak dapat** memodifikasi/menghapus todolist pengguna lain.
   - **Role `member`**:
     - User yang mendaftar melalui form registrasi otomatis memiliki role `member`.
     - Mengelola (CRUD), mengelompokkan, mengekspor/mengimpor, serta generate AI untuk todolist miliknya sendiri (`/todos`).
   - **Middleware & Policy**:
     - `EnsureRole`: Membatasi rute khusus admin.
     - `EnsureTodoOwnership`: Memastikan member tidak dapat mengakses atau memodifikasi todolist milik orang lain.
     - `TodoPolicy` & `TodoGroupPolicy`: Memvalidasi otorisasi kepemilikan di tingkat model.

5. **Spreadsheet Excel Import & Export**:
   - Menggunakan library aman dari kerentanan: **`phpoffice/phpspreadsheet`**.
   - **Export**: Menghasilkan file `.xlsx` dengan styling profesional (header berwarna, border, text wrap, auto size).
   - **Import**: Mengunggah file `.xlsx`, `.xls`, `.csv` untuk melakukan bulk insert tugas secara aman dengan validasi format data, status, dan tanggal jatuh tempo.

6. **AI Todo Content Generator (OpenAI)**:
   - Dikonfigurasi secara dinamis melalui `.env`:
     - `OPENAI_ENDPOINT` (mendukung custom proxy atau Azure/Ollama kompatibel)
     - `OPENAI_KEY`
     - `OPENAI_MODEL`
   - Kemampuan generate multiple todolist items secara otomatis berdasarkan prompt/permintaan pengguna (misal: "Buatkan rencana belajar Laravel selama 3 hari").
   - Output diformat dan divalidasi ke dalam DTO `GeneratedTodoItem`.
   - Pengguna dapat melihat preview hasil generate AI, memilih item yang diinginkan, dan menyimpannya langsung ke basis data.

7. **Rate Limiting & Keamanan**:
   - Skrip pembatas akses: **1000 request per detik** via `RateLimiter::for('1000-per-second', ...)`.
   - **Force HTTPS**: Dikonfigurasi melalui `FORCE_HTTPS=true` di `.env` atau otomatis aktif pada environment `production`.
   - Bebas dari kerentanan keamanan (`composer audit` clean 0 advisories).

8. **100% Test Coverage**:
   - Seluruh classes, methods, dan baris kode pada direktori `app/` memiliki **100% coverage**.
   - Unit test dan feature test komprehensif menguji seluruh alur autentikasi, model, repository, policy, middleware, service AI (mocked), template publik & kloning, serta upload/download spreadsheet.

---

## 📁 Struktur Direktori Feature-First

```
app/
├── Features/
│   ├── Admin/
│   │   ├── Controllers/
│   │   │   └── AdminUserController.php
│   │   └── Services/
│   │       └── AdminUserService.php
│   ├── Ai/
│   │   ├── Contracts/
│   │   │   └── AiClientInterface.php
│   │   ├── DTO/
│   │   │   └── GeneratedTodoItem.php
│   │   └── Services/
│   │       └── OpenAiTodoGeneratorService.php
│   ├── Auth/
│   │   ├── Actions/
│   │   │   ├── LoginUserAction.php
│   │   │   └── RegisterUserAction.php
│   │   └── Controllers/
│   │       └── AuthController.php
│   ├── Profile/
│   │   ├── Controllers/
│   │   │   └── ProfileController.php
│   │   └── Services/
│   │       └── ProfileService.php
│   └── Todo/
│       ├── Contracts/
│       │   ├── TodoGroupRepositoryInterface.php
│       │   └── TodoRepositoryInterface.php
│       ├── Controllers/
│       │   ├── PublicTemplateController.php
│       │   ├── TodoController.php
│       │   └── TodoGroupController.php
│       ├── Enums/
│       │   └── TodoStatus.php
│       ├── Models/
│       │   ├── Todo.php
│       │   └── TodoGroup.php
│       ├── Policies/
│       │   ├── TodoGroupPolicy.php
│       │   └── TodoPolicy.php
│       ├── Repositories/
│       │   ├── TodoGroupRepository.php
│       │   └── TodoRepository.php
│       └── Services/
│           ├── TodoExcelService.php
│           ├── TodoGroupService.php
│           └── TodoService.php
├── Http/
│   └── Middleware/
│       ├── EnsureRole.php
│       └── EnsureTodoOwnership.php
├── Models/
│   └── User.php
└── Providers/
    └── AppServiceProvider.php

resources/js/
├── Components/
├── Layouts/
│   └── AppLayout.jsx
└── Pages/
    ├── Admin/
    │   ├── UserList.jsx
    │   └── UserTodoList.jsx
    ├── Auth/
    │   ├── Login.jsx
    │   └── Register.jsx
    ├── Profile/
    │   └── Edit.jsx
    ├── Template/
    │   ├── Index.jsx
    │   └── Show.jsx
    └── Todo/
        ├── Index.jsx
        └── Show.jsx
```

---

## ⚙️ Konfigurasi Environment (`.env`)

```ini
APP_NAME="TodoList Pro"
APP_ENV=local
APP_KEY=base64:...
APP_DEBUG=true
APP_URL=http://localhost:8000

FORCE_HTTPS=false

# Basis Data PostgreSQL
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=pabwe_p8_todolist
DB_USERNAME=postgres
DB_PASSWORD=postgres

# Konfigurasi AI (OpenAI API)
OPENAI_ENDPOINT=https://api.openai.com/v1
OPENAI_KEY=your-openai-api-key-here
OPENAI_MODEL=gpt-4o-mini
```

---

## 🛠️ Instalasi & Menjalankan Proyek

1. **Masuk ke Direktori Proyek**:
   ```bash
   cd abdullah_ubaid-pabwe2026-latihan-p8-laravel-inertia
   ```

2. **Instalasi Dependencies**:
   ```bash
   composer install
   npm install
   ```

3. **Salin `.env` & Generate Key**:
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Migrasi & Seeding Data Awal**:
   Pastikan PostgreSQL sudah berjalan dan database `pabwe_p8_todolist` telah dibuat, kemudian jalankan:
   ```bash
   php artisan migrate:fresh --seed
   ```

   **Akun Bawaan Seeder**:
   - **Admin**:
     - Username: `admin` atau Email: `admin@example.com`
     - Password: `admin`
   - **Member**:
     - Username: `member` atau Email: `member@example.com`
     - Password: `member`

5. **Build Frontend & Jalankan Aplikasi**:
   ```bash
   npm run build
   php artisan serve
   ```
   Akses aplikasi di browser melalui `http://localhost:8000`.

### Catatan Direktori Storage

Pastikan direktori berikut ada sebelum menjalankan perintah `artisan` (terutama pada clone baru atau pipeline CI): `storage/framework/views`, `storage/framework/cache/data`, `storage/framework/sessions`, `storage/framework/testing`, dan `storage/logs`.

Direktori `storage/framework/views` wajib ada karena Laravel menghitung path compile view dengan `realpath(storage_path('framework/views'))`. Jika direktori tersebut hilang, `php artisan view:clear` akan gagal dengan `RuntimeException: View path not found`.

Jika ada direktori yang belum ada, buat dengan:

```bash
mkdir -p storage/framework/views storage/framework/cache/data storage/framework/sessions storage/framework/testing storage/logs
```

Direktori ini dipertahankan di Git melalui file `.gitignore` di dalam masing-masing folder.

---

## 🧪 Pengujian & Verifikasi 100% Test Coverage

Cukup jalankan perintah berikut untuk menjalankan seluruh pengujian dan melihat kalkulasi coverage 100%:

```bash
composer test
```

Hasil verifikasi:
```text
Code Coverage Report:
  Summary:
   Classes: 100.00% (26/26)
   Methods: 100.00%
   Lines:   100.00%

Tests: 135, Assertions: 562, Status: PASSED (100%)
```

Audit keamanan dependencies:
```bash
composer audit
# Output: No security vulnerability advisories found.
```
