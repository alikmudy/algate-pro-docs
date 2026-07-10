# Konfigurasi MikroTik API via IP Publik

> **Project:** ALgate Pro - Smart Gateway
> **Tanggal:** Juni 2026

---

## Overview

Panduan ini menjelaskan cara mengaktifkan MikroTik API (port 8728) dan mengizinkan akses dari IP publik VPS ALgate Pro, tanpa menggunakan tunnel VPN.

| Komponen | Nilai | Keterangan |
|---|---|---|
| IP Publik VPS | 103.52.115.28 | Server ALgate Pro |
| Port API MikroTik | 8728 | RouterOS API |
| Protokol | TCP | — |
| Akses diizinkan | 103.52.115.28/32 | Hanya dari VPS |

---

## Langkah 1 — Aktifkan API Service

Buka **Winbox → New Terminal**, jalankan:

```routeros
/ip service set api disabled=no port=8728 address=103.52.115.28/32
```

| Parameter | Nilai | Penjelasan |
|---|---|---|
| disabled | no | API service diaktifkan |
| port | 8728 | Port default RouterOS API |
| address | 103.52.115.28/32 | Hanya IP VPS yang boleh konek |

> ⚠️ Gunakan `/32` bukan `/24`. Dengan `/32` hanya satu IP yang diizinkan — lebih aman dari serangan brute force.

---

## Langkah 2 — Tambahkan Firewall Rule

```routeros
/ip firewall filter add chain=input src-address=103.52.115.28/32 protocol=tcp dst-port=8728 action=accept comment="Allow API from VPS ALgate Pro"
```

> ℹ️ Pastikan rule ini berada di atas rule DROP di daftar firewall. Cek urutan via: `/ip firewall filter print`

---

## Langkah 3 — Verifikasi

### Cek API Service

```routeros
/ip service print
# Pastikan baris api: disabled=no, port=8728, address=103.52.115.28/32
```

### Cek Firewall Rule

```routeros
/ip firewall filter print where comment~"VPS"
```

---

## Ringkasan Perintah

```routeros
# 1. Aktifkan API, batasi ke IP VPS
/ip service set api disabled=no port=8728 address=103.52.115.28/32

# 2. Izinkan di firewall
/ip firewall filter add chain=input src-address=103.52.115.28/32 protocol=tcp dst-port=8728 action=accept comment="Allow API from VPS ALgate Pro"

# 3. Verifikasi
/ip service print
/ip firewall filter print where comment~"VPS"
```

---

## Troubleshooting

| Gejala | Penyebab | Solusi |
|---|---|---|
| Connection refused port 8728 | API service disabled | Jalankan Langkah 1 |
| Connection timeout | Firewall blokir | Cek rule Langkah 2, pastikan posisi di atas DROP |
| Bad credentials | User/pass salah | Cek password admin MikroTik |
| API bisa diakses semua IP | `address` tidak di-set | Set `address=103.52.115.28/32` |
| IP VPS berubah | IP publik VPS dinamis | Update address dan firewall rule |

---

*Panduan ini bagian dari project ALgate Pro - Smart Gateway — Juni 2026.*
