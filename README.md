# 🎓 Simple LMS — Django + Docker + PostgreSQL

Proyek Django sederhana sebagai fondasi Learning Management System (LMS), dikonfigurasi menggunakan Docker dan PostgreSQL. Dibuat untuk memenuhi assignment Pertemuan 3 Capstone.

---

## 📁 Project Structure

```
simple-lms/
├── docker-compose.yml      # Orchestrates web + db services
├── Dockerfile              # Builds the Django app image
├── .env.example            # Template environment variables
├── .gitignore
├── requirements.txt        # Python dependencies
├── manage.py               # Django management script
├── config/
│   ├── __init__.py
│   ├── settings.py         # Django settings (uses env vars)
│   ├── urls.py             # Root URL configuration
│   └── wsgi.py             # WSGI entry point
└── README.md
```

---

## ⚙️ Environment Variables

Copy `.env.example` menjadi `.env` dan sesuaikan nilainya:

```bash
cp .env.example .env
```

| Variable       | Keterangan                                      | Contoh Nilai                        |
|----------------|-------------------------------------------------|-------------------------------------|
| `DEBUG`        | Mode debug Django (matikan di production)       | `True`                              |
| `SECRET_KEY`   | Kunci rahasia Django — **JANGAN dibagikan!**    | `your-super-secret-key-...`         |
| `ALLOWED_HOSTS`| Host yang diizinkan mengakses app               | `localhost,127.0.0.1`               |
| `DB_NAME`      | Nama database PostgreSQL                        | `lms_db`                            |
| `DB_USER`      | Username PostgreSQL                             | `lms_user`                          |
| `DB_PASSWORD`  | Password PostgreSQL                             | `lms_password`                      |
| `DB_HOST`      | Host database (nama service di Docker Compose)  | `db`                                |
| `DB_PORT`      | Port PostgreSQL                                 | `5432`                              |

---

## 🚀 Cara Menjalankan Project

### Prasyarat

Pastikan sudah terinstall:
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (includes Docker Compose)
- Git

### Langkah-langkah

**1. Clone repository**

```bash
git clone <URL_REPOSITORY_KAMU>
cd simple-lms
```

**2. Buat file `.env`**

```bash
cp .env.example .env
```

Edit file `.env` sesuai kebutuhan (nilai default sudah siap untuk development).

**3. Build dan jalankan semua service**

```bash
docker compose up --build
```

Perintah ini akan:
- Build image Django dari `Dockerfile`
- Pull image PostgreSQL 16
- Menjalankan migrasi database secara otomatis
- Menjalankan Django development server di port 8000

**4. Akses aplikasi**

Buka browser dan kunjungi:

```
http://localhost:8000
```

Django welcome page akan muncul ✅

**5. Akses Django Admin** *(opsional)*

Buat superuser terlebih dahulu:

```bash
docker compose exec web python manage.py createsuperuser
```

Lalu akses: `http://localhost:8000/admin`

---

## 🛑 Menghentikan Aplikasi

```bash
# Hentikan containers (data tetap tersimpan)
docker compose down

# Hentikan dan hapus semua data (termasuk database)
docker compose down -v
```

---

## 🔍 Perintah Berguna Lainnya

```bash
# Lihat logs secara real-time
docker compose logs -f

# Lihat logs hanya service web
docker compose logs -f web

# Masuk ke shell container web
docker compose exec web bash

# Jalankan Django management commands
docker compose exec web python manage.py <command>

# Membuat migrasi baru (setelah menambah model)
docker compose exec web python manage.py makemigrations
docker compose exec web python manage.py migrate

# Cek status database
docker compose exec db psql -U lms_user -d lms_db -c "\l"
```

---

## 🏗️ Arsitektur Docker

```
┌─────────────────────────────────────────────┐
│              Docker Network                  │
│                                             │
│  ┌────────────────┐    ┌─────────────────┐  │
│  │   web service  │    │   db service    │  │
│  │  (Django 5.0)  │───▶│ (PostgreSQL 16) │  │
│  │  Port: 8000    │    │  Port: 5432     │  │
│  └────────────────┘    └─────────────────┘  │
│          │                     │            │
│          ▼                     ▼            │
│   static_volume         postgres_data       │
│   (named volume)        (named volume)      │
└─────────────────────────────────────────────┘
```

**Services:**

| Service | Image | Port | Keterangan |
|---------|-------|------|------------|
| `web`   | Python 3.12-slim (custom build) | 8000 | Django application server |
| `db`    | postgres:16-alpine | 5432 | PostgreSQL database |

**Volumes:**

| Volume | Keterangan |
|--------|------------|
| `postgres_data` | Persistensi data PostgreSQL |
| `static_volume` | Static files Django |

---

## 🖼️ Screenshot

### Django Welcome Page
> Akses `http://localhost:8000` setelah menjalankan `docker compose up --build`

![Django Welcome Page](docs/django-welcome.png)

*(Screenshot diambil setelah project berhasil dijalankan)*

---

## 📦 Dependencies

| Package | Versi | Keterangan |
|---------|-------|------------|
| Django | 5.0.6 | Web framework utama |
| psycopg2-binary | 2.9.9 | PostgreSQL adapter untuk Python |
| python-decouple | 3.8 | Manajemen environment variables |
| whitenoise | 6.7.0 | Serving static files |
| gunicorn | 22.0.0 | WSGI server untuk production |

---

## 🔐 Catatan Keamanan

- **Jangan commit file `.env`** ke repository Git — sudah dikecualikan di `.gitignore`
- Ganti `SECRET_KEY` dengan nilai yang kuat dan unik untuk production
- Set `DEBUG=False` di production
- Batasi `ALLOWED_HOSTS` hanya ke domain yang kamu gunakan

---

## 📚 Referensi

- [Docker Documentation](https://docs.docker.com/)
- [Django Documentation](https://docs.djangoproject.com/)
- [PostgreSQL Docker Image](https://hub.docker.com/_/postgres)
- [python-decouple](https://github.com/HBNetwork/python-decouple)
