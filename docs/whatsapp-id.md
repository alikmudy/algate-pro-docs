# Panduan Mendapatkan ID WhatsApp untuk Notifikasi ALgate Pro

> **Project:** ALgate Pro - Smart Gateway
> **Tanggal:** Juni 2026

---

## Overview

ALgate Pro membutuhkan dua jenis ID WhatsApp untuk mengirim notifikasi:

| Jenis | Format | Contoh | Field di ALgate Pro |
|---|---|---|---|
| Nomor WA Personal | Kode negara + nomor (tanpa + dan 0) | `6281249007936` | Nomor WA Personal |
| ID Grup WA | Angka panjang diakhiri `@g.us` | `628119xxxxxx-1234567890@g.us` | Nomor WA Grup |

---

## Bagian A — Nomor WA Personal

Nomor WA Personal adalah nomor WhatsApp tujuan notifikasi pribadi. Formatnya menggunakan **kode negara 62** (Indonesia) tanpa tanda `+` dan tanpa angka `0` di depan.

### Format Penulisan

| Nomor HP | Format Salah | Format Benar |
|---|---|---|
| 081249007936 | +6281249007936 | `6281249007936` |
| 08123456789 | 08123456789 | `628123456789` |
| 081234567890 | +62-812-3456-7890 | `6281234567890` |

### Cara Mengubah Nomor ke Format yang Benar

1. Hilangkan angka **0** di depan nomor
2. Tambahkan **62** di depan
3. Tidak perlu tanda `+`, spasi, atau tanda hubung

**Contoh:**
```
081249007936
→ hilangkan 0 → 81249007936
→ tambah 62   → 6281249007936
```

### Masukkan ke Form ALgate Pro

```
Nomor WA Personal: 6281249007936
```

---

## Bagian B — ID Grup WhatsApp via WhatsApp Web

Cara paling mudah dan akurat mendapatkan ID grup WA adalah melalui **WhatsApp Web** — langsung terlihat di URL browser tanpa tools tambahan.

### Langkah 1 — Buka WhatsApp Web

Buka browser, akses:

```
https://web.whatsapp.com
```

Scan QR code menggunakan aplikasi WhatsApp di HP (nomor WA yang sudah tergabung di grup).

### Langkah 2 — Buka Grup yang Dituju

Di panel kiri, cari dan klik nama grup yang ingin menerima notifikasi.

### Langkah 3 — Lihat ID Grup di URL Browser

Setelah grup terbuka, perhatikan **address bar** browser. URL akan berubah menjadi:

```
https://web.whatsapp.com/accept?code=...
```

atau format langsung:

```
https://web.whatsapp.com/#628119xxxxxx-1234567890@g.us
```

Bagian setelah `#` adalah **ID Grup** lengkap.

> ℹ️ Jika URL tidak langsung menampilkan ID, coba klik info grup atau kirim pesan di grup — URL akan terupdate otomatis.

### Langkah 4 — Salin ID Grup

Salin bagian ID grup dari URL, contoh:

```
628119xxxxxx-1234567890@g.us
```

### Langkah 5 — Masukkan ke Form ALgate Pro

```
Nomor WA Grup: 628119xxxxxx-1234567890@g.us
```

> ⚠️ Salin ID grup **lengkap** termasuk bagian `@g.us` di belakangnya. Tanpa `@g.us` notifikasi tidak akan terkirim ke grup.

---

## Troubleshooting

| Gejala | Penyebab | Solusi |
|---|---|---|
| Notifikasi tidak terkirim ke nomor personal | Format nomor salah | Pastikan diawali `62`, tanpa `+` dan `0` |
| URL WA Web tidak menampilkan ID grup | Grup belum terbuka penuh | Klik info grup atau kirim pesan di grup |
| ID grup tidak valid | `@g.us` tidak disertakan | Salin ID grup lengkap dari URL |
| Pesan terkirim tapi tidak masuk grup | Nomor WA bukan anggota grup | Tambahkan nomor WA ke grup terlebih dahulu |
| QR Code WA Web expired | Terlalu lama tidak di-scan | Refresh halaman, scan ulang QR |

---

## Ringkasan

| Field | Format | Cara Mendapatkan |
|---|---|---|
| Nomor WA Personal | `62xxxxxxxxxxxx` | Ubah nomor HP: ganti `0` depan dengan `62` |
| Nomor WA Grup | `xxxx@g.us` | WhatsApp Web → buka grup → salin dari URL browser |

---

*Panduan ini bagian dari project ALgate Pro - Smart Gateway — Juni 2026.*
