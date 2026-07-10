# Setup Telegram Bot & Chat ID untuk Notifikasi ALgate Pro

> **Project:** ALgate Pro - Smart Gateway
> **Tanggal:** Juni 2026

---

## Overview

Panduan ini menjelaskan cara membuat bot Telegram, mendapatkan ID bot, dan mendapatkan Chat ID grup untuk sistem notifikasi ALgate Pro.

| Jenis | Format | Contoh | Field di ALgate Pro |
|---|---|---|---|
| ID Bot | Angka positif | `5010257781` | Chat ID Personal |
| Chat ID Grup | Angka negatif | `-5584843527` | Chat ID Grup |

---

## Bagian A — Membuat Bot Telegram via @BotFather

### Langkah 1 — Buka @BotFather

Di aplikasi Telegram, cari dan buka:

```
@BotFather
```

> ℹ️ Pastikan memilih akun dengan centang biru (verified). BotFather adalah bot resmi Telegram untuk membuat dan mengelola bot.

### Langkah 2 — Buat bot baru

Kirim perintah:

```
/newbot
```

BotFather akan meminta dua informasi:

| Urutan | Informasi | Contoh | Catatan |
|---|---|---|---|
| 1 | Nama bot (display name) | `ALgate Pro Notifikasi` | Nama yang tampil di Telegram |
| 2 | Username bot | `algatepro_bot` | Harus diakhiri `_bot` atau `Bot`, unik |

> ⚠️ Username bot bersifat permanen dan unik — tidak bisa dipakai jika sudah digunakan orang lain.

### Langkah 3 — Simpan Token Bot

BotFather akan mengirim token API:

```
Done! Congratulations on your new bot.
Use this token to access the HTTP API:

123456789:ABCdefGHIjklMNOpqrSTUvwxYZ

Keep your token secure and store it safely.
```

> ⚠️ **Jangan bagikan token ke siapapun.** Token = akses penuh ke bot Anda.

### Langkah 4 — Masukkan Token ke ALgate Pro

Buka **Pengaturan Notifikasi → Telegram** di ALgate Pro, masukkan token pada field yang tersedia.

### Perintah Tambahan @BotFather (Opsional)

| Perintah | Fungsi |
|---|---|
| `/mybots` | Lihat daftar bot yang sudah dibuat |
| `/setdescription` | Ubah deskripsi bot |
| `/setuserpic` | Upload foto profil bot |
| `/deletebot` | Hapus bot (permanen) |
| `/token` | Generate ulang token jika token lama bocor |

---

## Bagian B — Mendapatkan ID Bot (Field: Chat ID Personal)

Field **Chat ID Personal** di ALgate Pro diisi dengan **ID dari bot yang Anda buat** — bukan ID akun pribadi.

### Langkah 1 — Akses endpoint getMe

Buka browser, akses URL berikut menggunakan token dari Bagian A:

```
https://api.telegram.org/bot<TOKEN>/getMe
```

Contoh:
```
https://api.telegram.org/bot123456789:ABCdef.../getMe
```

### Langkah 2 — Ambil ID Bot dari JSON

```json
{
  "ok": true,
  "result": {
    "id": 5010257781,
    "is_bot": true,
    "first_name": "ALgate Pro",
    "username": "algatepro_bot"
  }
}
```

✅ Angka pada `"id"` adalah **ID Bot** Anda.

### Langkah 3 — Masukkan ke field Chat ID Personal

```
Chat ID Personal: 5010257781
```

---

## Bagian C — Mendapatkan Chat ID Grup & Jadikan Bot Admin

Bot dimasukkan ke grup dan dijadikan **Admin** agar bisa mengirim notifikasi ke grup.

### Langkah 1 — Tambahkan bot ke grup & jadikan Admin

1. Tap nama grup di bagian atas
2. Pilih **Edit / Add Members**
3. Cari username bot (contoh: `@algatepro_bot`) → Tambahkan
4. Kembali ke info grup → tap nama bot → pilih **Promote to Admin**
5. Aktifkan izin: **Send Messages** → simpan

> ⚠️ Bot **WAJIB** dijadikan Admin. Tanpa hak admin, notifikasi tidak akan terkirim ke grup.

### Langkah 2 — Kirim pesan sembarang di grup

Kirim pesan apa saja di grup (contoh: `test`) agar aktivitas grup terekam di getUpdates.

### Langkah 3 — Buka getUpdates via browser

```
https://api.telegram.org/bot<TOKEN>/getUpdates
```

### Langkah 4 — Temukan Chat ID Grup di JSON

Cari pesan dari grup (`type: group` atau `supergroup`). Ambil nilai `chat → id`:

```json
{
  "message": {
    "chat": {
      "id": -5584843527,
      "title": "Nama Grup",
      "type": "group"
    }
  }
}
```

✅ Angka **negatif** pada `chat.id` adalah **Chat ID Grup** Anda.

### Langkah 5 — Masukkan ke field Chat ID Grup

```
Chat ID Grup: -5584843527
```

> ⚠️ Jangan hilangkan tanda minus (`-`) di depan angka. ID grup selalu bertanda negatif.

---

## Troubleshooting

| Gejala | Penyebab | Solusi |
|---|---|---|
| Bot tidak merespons `/start` | Bot belum pernah di-start | Buka chat bot, kirim `/start` dulu |
| `getUpdates` kosong `[]` | Belum ada pesan setelah bot ditambah | Kirim pesan dulu, lalu refresh URL |
| Chat ID tidak muncul | Token bot salah | Cek token dari @BotFather |
| Notifikasi tidak terkirim ke grup | Bot bukan admin grup | Jadikan bot Admin di grup |
| Chat ID Personal = angka negatif | Salah ambil — itu ID grup | Gunakan `getMe` untuk ID bot |

---

## Ringkasan

| Jenis ID | Cara Mendapatkan | Format | Field ALgate Pro |
|---|---|---|---|
| ID Bot | `getMe` via browser | Angka positif (contoh: `5010257781`) | Chat ID Personal |
| Chat ID Grup | Bot masuk grup → kirim pesan → `getUpdates` | Angka negatif (contoh: `-5584843527`) | Chat ID Grup |

---

*Panduan ini bagian dari project ALgate Pro - Smart Gateway — Juni 2026.*
