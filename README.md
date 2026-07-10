# ALgate Pro - Smart Gateway 📡

> Dokumentasi teknis resmi untuk **ALgate Pro - Smart Gateway** — Solusi manajemen MikroTik multi-cabang dengan Tagihan Pembayaran Online berbasis WEB dan Android dengan Notifikasi WA, Telegram dan Push Android.

---

## 🗂️ Daftar Panduan

🌐 Website: https://lintas-algate.my.id

📱 Aplikasi Android:
ALgate Pro - Smart Gateway  https://play.google.com/store/apps/details?id=com.alya.algatefull

| No | Panduan | Deskripsi |
|---|---|---|
| 1 | [Konfigurasi MikroTik OpenVPN Client](docs/mikrotik-openvpn-client.md) | Menghubungkan MikroTik cabang ke VPS via OpenVPN + setup PPPoE Isolir |
| 2 | [Konfigurasi API MikroTik via IP Publik](docs/mikrotik-api-ip-publik.md) | Mengaktifkan RouterOS API (port 8728) dengan akses IP Publik |
| 3 | [Setup Telegram Bot & Chat ID](docs/telegram-bot-setup.md) | Membuat bot Telegram, mendapatkan ID bot dan Chat ID grup untuk notifikasi |
| 4 | [PPPoE Profile Isolir](docs/pppoe-isolir.md) | Membuat profile isolir 10k/10k (untuk fungsi tombol isolir manual/otomatis) dan cara assign/lepas via API MikroTik |
| 5 | [Setup Notifikasi WA](docs/whatsapp-id.md) | MMendapatkan ID WA pribadi dan Chat ID grup untuk notifikasi WA |

---

## 🏗️ Topologi Jaringan

```
[MikroTik Cabang 1] ──┐
[MikroTik Cabang 2] ──┼──(Internet)──► Server OpenVPN ALgate Pro (10.8.0.1)
[MikroTik Cabang N] ──┘                    │
                                    ALgate Pro Web App
                                    Notifikasi WA, Telegram, dan Push Android
```

| Node | IP Tunnel | Keterangan |
|---|---|---|
| VPS (OpenVPN Server) | 10.8.0.1 | Server pusat ALgate Pro |
| MikroTik Cabang 1 | 10.8.0.2 | client cabang1 |
| MikroTik Cabang 2 | 10.8.0.3 | client cabang2 |
| MikroTik Cabang N | 10.8.0.N | client cabangN |

---

## ⚡ Quick Start

### Untuk Admin / Server
1. Generate sertifikat per cabang

### Untuk Teknisi Cabang
1. Terima file sertifikat dari admin (ca.crt, cabangX.crt, cabangX.key)
2. [Konfigurasi MikroTik Client](docs/mikrotik-openvpn-client.md)
3. Verifikasi koneksi VPN

### Untuk Notifikasi Telegram
1. [Buat bot via @BotFather](docs/telegram-bot-setup.md#bagian-a--membuat-bot-telegram-via-botfather)
2. [Dapatkan ID Bot](docs/telegram-bot-setup.md#bagian-b--mendapatkan-id-bot-field-chat-id-personal)
3. [Setup grup & dapatkan Chat ID Grup](docs/telegram-bot-setup.md#bagian-c--mendapatkan-chat-id-grup--jadikan-bot-admin)

---

## 🛠️ Stack Teknologi

| Komponen | Teknologi |
|---|---|
| Server VPN | OpenVPN 2.4 + Ubuntu 20.04 |
| Router Cabang | MikroTik RouterOS 6.x |
| Notifikasi | WA, Telegram dan Push Notifikasi Android|
| Manajemen Bandwidth | PPPoE Profile + RouterOS API |

---

## 📋 Persyaratan

- VPS Ubuntu 20.04 dengan IP publik statis
- MikroTik RouterOS versi 6.x
---

## 📞 Kontak & Support

Butuh bantuan teknis? Hubungi kami:

**📱 WhatsApp / Telepon: 081249007936**
**Channel Tekegram : https://t.me/algatepro**
**Github : https://github.com/alikmudy/algate-pro-docs**

---

## 📄 Lisensi

Dokumentasi ini milik **ALgate Pro - Smart Gateway**.
Dilarang menyebarluaskan tanpa izin tertulis.

---

*Dokumentasi ini diperbarui: Juni 2026*
