# PPPoE Profile Isolir — 10 Kbps / 10 Kbps

> **Project:** ALgate Pro - Smart Gateway
> **Tanggal:** Juni 2026

---

## Overview

Profile isolir digunakan untuk membatasi bandwidth pelanggan yang menunggak tagihan menjadi **10 Kbps / 10 Kbps** (upload/download). Perintah dapat dijalankan langsung dari **Winbox Terminal** maupun dieksekusi otomatis dari sistem ALgate Pro via RouterOS API.

---

## Langkah 1 — Buat Profile Isolir

Jalankan **sekali saja** via **Winbox → New Terminal**:

```routeros
/ppp profile add name=isolir rate-limit=10k/10k only-one=yes
```

Verifikasi:

```routeros
/ppp profile print
# Harus ada entry: name=isolir rate-limit=10k/10k
```

---

## Langkah 2 — Assign Profile Isolir ke Pelanggan

### Via Winbox Terminal

```routeros
# Aktifkan isolir
/ppp secret set [find name=username_pelanggan] profile=isolir

# Putus sesi aktif agar langsung berlaku
/ppp active remove [find name=username_pelanggan]
```

### Via ALgate Pro (API MikroTik — otomatis)

ALgate Pro mengeksekusi perintah ini secara otomatis saat pelanggan terdeteksi menunggak.

---

## Langkah 3 — Lepas Isolir (Pulihkan ke Normal)

### Via Winbox Terminal

```routeros
# Kembalikan ke profile normal
/ppp secret set [find name=username_pelanggan] profile=default

# Putus sesi agar profile baru berlaku
/ppp active remove [find name=username_pelanggan]
```

> ℹ️ `/ppp active remove` memutus sesi PPPoE aktif sehingga pelanggan reconnect otomatis dengan profile baru. Jika pelanggan sedang **offline**, perintah ini tidak perlu dijalankan.

---

## Catatan Penting

| Kondisi | Yang Dilakukan |
|---|---|
| Pelanggan sedang online | Jalankan `secret set` + `active remove` |
| Pelanggan sedang offline | Cukup jalankan `secret set` saja |
| Profile belum ada | Buat profile dulu (Langkah 1) |

---

## Troubleshooting

| Gejala | Penyebab | Solusi |
|---|---|---|
| Profile tidak ditemukan saat assign | Profile `isolir` belum dibuat | Jalankan Langkah 1 terlebih dahulu |
| Pelanggan masih bisa internet normal setelah isolir | Sesi lama belum terputus | Jalankan `/ppp active remove` |
| Bandwidth masih penuh setelah isolir | Pelanggan reconnect sebelum profile tersimpan | Tunggu beberapa detik, lalu cek lagi |
| `[find name=...]` tidak menemukan user | Username salah | Cek via `/ppp secret print` |

---

## Referensi Perintah Cepat

```routeros
# Lihat semua PPPoE secret
/ppp secret print

# Lihat sesi aktif
/ppp active print

# Cek profile pelanggan tertentu
/ppp secret print where name=username_pelanggan

# Lihat semua profile yang ada
/ppp profile print
```

---

*Panduan ini bagian dari project ALgate Pro - Smart Gateway — Juni 2026.*
