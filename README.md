# E-Voting System

Sistem E-Voting berbasis web yang dibangun menggunakan Laravel 12. Aplikasi ini memungkinkan pengelolaan pemilihan suara secara digital dengan fitur manajemen kelas, kandidat, dan pengguna.

## 📋 Daftar Isi

- [Fitur](#-fitur)
- [Persyaratan Sistem](#-persyaratan-sistem)
- [Instalasi](#-instalasi)
    - [1. Clone Repository](#1-clone-repository)
    - [2. Install Dependencies](#2-install-dependencies)
    - [3. Konfigurasi Environment](#3-konfigurasi-environment)
    - [4. Setup Database](#4-setup-database)
    - [5. Menjalankan Aplikasi](#5-menjalankan-aplikasi)
- [Struktur Database](#-struktur-database)
- [Dokumentasi API](#-dokumentasi-api)
- [Lisensi](#-lisensi)

## ✨ Fitur

- 🗳️ Sistem voting digital
- 👥 Manajemen kelas/kelompok
- 🎯 Manajemen kandidat
- 👤 Manajemen pengguna dengan pembagian kelas
- 📊 Rekap hasil voting

## 💻 Persyaratan Sistem

- PHP >= 8.2
- Composer
- Node.js >= 18.x
- MySQL / MariaDB
- Git

## 🚀 Instalasi

### 1. Clone Repository

```bash
git clone https://github.com/username/e-voting.git
cd e-voting
```

### 2. Install Dependencies

Install PHP dependencies menggunakan Composer:

```bash
composer install
```

Install JavaScript dependencies:

```bash
npm install
```

### 3. Konfigurasi Environment

Salin file `.env.example` menjadi `.env`:

```bash
cp .env.example .env
```

Generate application key:

```bash
php artisan key:generate
```

Edit file `.env` dan sesuaikan konfigurasi database:

```env
APP_NAME="E-Voting"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=e_voting
DB_USERNAME=root
DB_PASSWORD=your_password
```

### 4. Setup Database

Buat database baru di MySQL:

```sql
CREATE DATABASE e_voting;
```

Jalankan migrasi database:

```bash
php artisan migrate
```

(Opsional) Jalankan seeder untuk data dummy:

```bash
php artisan db:seed
```

### 5. Menjalankan Aplikasi

**Development Mode (Recommended):**

```bash
composer dev
```

Perintah ini akan menjalankan:

- Laravel development server
- Queue listener
- Laravel Pail (log viewer)
- Vite development server

**Atau jalankan secara manual:**

```bash
# Terminal 1 - Laravel server
php artisan serve

# Terminal 2 - Vite (untuk assets)
npm run dev
```

Akses aplikasi di: `http://localhost:8000`

## 🗄️ Struktur Database

### Tabel `classes`

Menyimpan data kelas/kelompok pemilih.

| Kolom        | Tipe         | Keterangan                  |
| ------------ | ------------ | --------------------------- |
| `id`         | bigint       | Primary key, auto increment |
| `name`       | varchar(255) | Nama kelas                  |
| `created_at` | timestamp    | Waktu pembuatan             |
| `updated_at` | timestamp    | Waktu update terakhir       |

### Tabel `candidates`

Menyimpan data kandidat yang dapat dipilih.

| Kolom        | Tipe         | Keterangan                  |
| ------------ | ------------ | --------------------------- |
| `id`         | bigint       | Primary key, auto increment |
| `name`       | varchar(255) | Nama kandidat               |
| `created_at` | timestamp    | Waktu pembuatan             |
| `updated_at` | timestamp    | Waktu update terakhir       |

### Tabel `users`

Menyimpan data pengguna/pemilih.

| Kolom               | Tipe         | Keterangan                      |
| ------------------- | ------------ | ------------------------------- |
| `id`                | bigint       | Primary key, auto increment     |
| `name`              | varchar(255) | Nama pengguna                   |
| `email`             | varchar(255) | Email pengguna (unique)         |
| `email_verified_at` | timestamp    | Waktu verifikasi email          |
| `password`          | varchar(255) | Password (hashed)               |
| `class_id`          | bigint       | Foreign key ke tabel `classes`  |
| `remember_token`    | varchar(100) | Token untuk fitur "remember me" |
| `created_at`        | timestamp    | Waktu pembuatan                 |
| `updated_at`        | timestamp    | Waktu update terakhir           |

### Tabel `votes`

Menyimpan data suara/pilihan pengguna.

| Kolom          | Tipe      | Keterangan                        |
| -------------- | --------- | --------------------------------- |
| `id`           | bigint    | Primary key, auto increment       |
| `candidate_id` | bigint    | Foreign key ke tabel `candidates` |
| `user_id`      | bigint    | Foreign key ke tabel `users`      |
| `created_at`   | timestamp | Waktu pembuatan                   |
| `updated_at`   | timestamp | Waktu update terakhir             |

### Diagram ERD

```
┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│   classes   │       │    users    │       │    votes    │
├─────────────┤       ├─────────────┤       ├─────────────┤
│ id (PK)     │◄──────│ id (PK)     │◄──────│ id (PK)     │
│ name        │       │ name        │       │ candidate_id│───┐
└─────────────┘       │ email       │       │ user_id (FK)│   │
                      │ password    │       └─────────────┘   │
                      │ class_id(FK)│                         │
                      └─────────────┘                         │
                                                              │
                      ┌─────────────┐                         │
                      │ candidates  │                         │
                      ├─────────────┤                         │
                      │ id (PK)     │◄────────────────────────┘
                      │ name        │
                      └─────────────┘
```

## 📚 Dokumentasi API

### Base URL

```
http://localhost:8000/api
```

### Authentication

Semua endpoint API (kecuali login dan register) memerlukan token Bearer:

```
Authorization: Bearer {token}
```

---

### 🔐 Authentication

#### Register

```http
POST /api/register
```

**Request Body:**

```json
{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123",
    "password_confirmation": "password123",
    "class_id": 1
}
```

**Response (201 Created):**

```json
{
    "success": true,
    "message": "Registrasi berhasil",
    "data": {
        "user": {
            "id": 1,
            "name": "John Doe",
            "email": "john@example.com",
            "class_id": 1,
            "created_at": "2026-02-02T10:00:00.000000Z"
        },
        "token": "1|abc123..."
    }
}
```

#### Login

```http
POST /api/login
```

**Request Body:**

```json
{
    "email": "john@example.com",
    "password": "password123"
}
```

**Response (200 OK):**

```json
{
    "success": true,
    "message": "Login berhasil",
    "data": {
        "user": {
            "id": 1,
            "name": "John Doe",
            "email": "john@example.com",
            "class_id": 1
        },
        "token": "2|def456..."
    }
}
```

#### Logout

```http
POST /api/logout
```

**Headers:**

```
Authorization: Bearer {token}
```

**Response (200 OK):**

```json
{
    "success": true,
    "message": "Logout berhasil"
}
```

---

### 🏫 Classes

#### Get All Classes

```http
GET /api/classes
```

**Response (200 OK):**

```json
{
    "success": true,
    "data": [
        {
            "id": 1,
            "name": "XII RPL 1",
            "created_at": "2026-02-02T10:00:00.000000Z",
            "updated_at": "2026-02-02T10:00:00.000000Z"
        },
        {
            "id": 2,
            "name": "XII RPL 2",
            "created_at": "2026-02-02T10:00:00.000000Z",
            "updated_at": "2026-02-02T10:00:00.000000Z"
        }
    ]
}
```

#### Get Class by ID

```http
GET /api/classes/{id}
```

**Response (200 OK):**

```json
{
    "success": true,
    "data": {
        "id": 1,
        "name": "XII RPL 1",
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T10:00:00.000000Z"
    }
}
```

#### Create Class

```http
POST /api/classes
```

**Request Body:**

```json
{
    "name": "XII RPL 3"
}
```

**Response (201 Created):**

```json
{
    "success": true,
    "message": "Kelas berhasil ditambahkan",
    "data": {
        "id": 3,
        "name": "XII RPL 3",
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T10:00:00.000000Z"
    }
}
```

#### Update Class

```http
PUT /api/classes/{id}
```

**Request Body:**

```json
{
    "name": "XII PPLG 1"
}
```

**Response (200 OK):**

```json
{
    "success": true,
    "message": "Kelas berhasil diperbarui",
    "data": {
        "id": 1,
        "name": "XII PPLG 1",
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T11:00:00.000000Z"
    }
}
```

#### Delete Class

```http
DELETE /api/classes/{id}
```

**Response (200 OK):**

```json
{
    "success": true,
    "message": "Kelas berhasil dihapus"
}
```

---

### 🎯 Candidates

#### Get All Candidates

```http
GET /api/candidates
```

**Response (200 OK):**

```json
{
    "success": true,
    "data": [
        {
            "id": 1,
            "name": "Kandidat A",
            "created_at": "2026-02-02T10:00:00.000000Z",
            "updated_at": "2026-02-02T10:00:00.000000Z"
        },
        {
            "id": 2,
            "name": "Kandidat B",
            "created_at": "2026-02-02T10:00:00.000000Z",
            "updated_at": "2026-02-02T10:00:00.000000Z"
        }
    ]
}
```

#### Get Candidate by ID

```http
GET /api/candidates/{id}
```

**Response (200 OK):**

```json
{
    "success": true,
    "data": {
        "id": 1,
        "name": "Kandidat A",
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T10:00:00.000000Z"
    }
}
```

#### Create Candidate

```http
POST /api/candidates
```

**Request Body:**

```json
{
    "name": "Kandidat C"
}
```

**Response (201 Created):**

```json
{
    "success": true,
    "message": "Kandidat berhasil ditambahkan",
    "data": {
        "id": 3,
        "name": "Kandidat C",
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T10:00:00.000000Z"
    }
}
```

#### Update Candidate

```http
PUT /api/candidates/{id}
```

**Request Body:**

```json
{
    "name": "Kandidat A (Updated)"
}
```

**Response (200 OK):**

```json
{
    "success": true,
    "message": "Kandidat berhasil diperbarui",
    "data": {
        "id": 1,
        "name": "Kandidat A (Updated)",
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T11:00:00.000000Z"
    }
}
```

#### Delete Candidate

```http
DELETE /api/candidates/{id}
```

**Response (200 OK):**

```json
{
    "success": true,
    "message": "Kandidat berhasil dihapus"
}
```

---

### 🗳️ Votes

#### Submit Vote

```http
POST /api/votes
```

**Headers:**

```
Authorization: Bearer {token}
```

**Request Body:**

```json
{
    "candidate_id": 1
}
```

**Response (201 Created):**

```json
{
    "success": true,
    "message": "Vote berhasil disimpan",
    "data": {
        "id": 1,
        "candidate_id": 1,
        "user_id": 1,
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T10:00:00.000000Z"
    }
}
```

**Response (400 Bad Request - Already Voted):**

```json
{
    "success": false,
    "message": "Anda sudah melakukan voting"
}
```

#### Get Vote Results

```http
GET /api/votes/results
```

**Response (200 OK):**

```json
{
    "success": true,
    "data": {
        "total_votes": 100,
        "results": [
            {
                "candidate_id": 1,
                "candidate_name": "Kandidat A",
                "vote_count": 60,
                "percentage": 60.0
            },
            {
                "candidate_id": 2,
                "candidate_name": "Kandidat B",
                "vote_count": 40,
                "percentage": 40.0
            }
        ]
    }
}
```

#### Check User Vote Status

```http
GET /api/votes/status
```

**Headers:**

```
Authorization: Bearer {token}
```

**Response (200 OK - Has Voted):**

```json
{
    "success": true,
    "data": {
        "has_voted": true,
        "vote": {
            "id": 1,
            "candidate_id": 1,
            "candidate_name": "Kandidat A",
            "voted_at": "2026-02-02T10:00:00.000000Z"
        }
    }
}
```

**Response (200 OK - Has Not Voted):**

```json
{
    "success": true,
    "data": {
        "has_voted": false,
        "vote": null
    }
}
```

---

### 👤 Users

#### Get Current User

```http
GET /api/user
```

**Headers:**

```
Authorization: Bearer {token}
```

**Response (200 OK):**

```json
{
    "success": true,
    "data": {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com",
        "class_id": 1,
        "class": {
            "id": 1,
            "name": "XII RPL 1"
        },
        "created_at": "2026-02-02T10:00:00.000000Z",
        "updated_at": "2026-02-02T10:00:00.000000Z"
    }
}
```

#### Get All Users (Admin)

```http
GET /api/users
```

**Headers:**

```
Authorization: Bearer {token}
```

**Response (200 OK):**

```json
{
    "success": true,
    "data": [
        {
            "id": 1,
            "name": "John Doe",
            "email": "john@example.com",
            "class_id": 1,
            "class": {
                "id": 1,
                "name": "XII RPL 1"
            },
            "has_voted": true,
            "created_at": "2026-02-02T10:00:00.000000Z"
        }
    ]
}
```

---

### Error Responses

#### 401 Unauthorized

```json
{
    "success": false,
    "message": "Unauthenticated"
}
```

#### 404 Not Found

```json
{
    "success": false,
    "message": "Data tidak ditemukan"
}
```

#### 422 Validation Error

```json
{
    "success": false,
    "message": "Validasi gagal",
    "errors": {
        "email": ["Email sudah terdaftar"],
        "password": ["Password minimal 8 karakter"]
    }
}
```

#### 500 Server Error

```json
{
    "success": false,
    "message": "Terjadi kesalahan pada server"
}
```

---

## 🛠️ Development

### Menjalankan Tests

```bash
composer test
```

### Code Style

Format kode menggunakan Laravel Pint:

```bash
./vendor/bin/pint
```

## 📄 Lisensi

Project ini dilisensikan di bawah [MIT License](https://opensource.org/licenses/MIT).
