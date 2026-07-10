# Panduan MikroTik OpenVPN Client

> **Project:** ALgate Pro - Smart Gateway
> **Ditujukan untuk:** Teknisi Cabang / IT MikroTik
> **Tanggal:** Juni 2026

---

## Overview

Panduan ini menjelaskan konfigurasi MikroTik RouterOS sebagai OpenVPN Client yang terhubung ke VPS server ALgate Pro. Setelah terhubung, MikroTik akan mendapatkan IP tunnel dan dapat dikelola dari server pusat.

| Item | Nilai |
|---|---|
| IP Server VPN | 103.52.115.28 |
| Port OpenVPN | 1194 (TCP) |
| IP Tunnel MikroTik | 10.8.0.x (assigned dari server) |
| Mode | ethernet (TAP) |
| Cipher | AES-256-CBC |
| Auth | SHA1 |
| RouterOS versi | 6.x |

> ⚠️ Pastikan sertifikat (ca.crt, cabangX.crt, cabangX.key) sudah disiapkan dari VPS oleh administrator sebelum memulai.

---

## Langkah 1 — Upload Sertifikat ke MikroTik

Buka **Winbox → Files → Upload**, lalu upload tiga file berikut:

| File | Keterangan |
|---|---|
| `ca.crt` | Sertifikat CA dari server |
| `cabangX.crt` | Sertifikat client cabang ini |
| `cabangX.key` | Private key client cabang ini |

> ℹ️ Ganti `cabangX` sesuai nama file yang diberikan administrator (contoh: cabang1, cabang2, dst).

---

## Langkah 2 — Import Sertifikat di MikroTik Terminal

Buka **Winbox → New Terminal**, jalankan perintah berikut:

```routeros
/certificate import file-name=ca.crt passphrase=""
/certificate import file-name=cabangX.crt passphrase=""
/certificate import file-name=cabangX.key passphrase=""
```

Verifikasi hasil import:

```routeros
/certificate print
# Output yang diharapkan:
# 0  AT  ca       algate
# 1  KT  cabangX  cabangX
```

| Flag | Arti |
|---|---|
| A | Authority — sertifikat CA |
| T | Trusted — dipercaya oleh sistem |
| K | Private Key tersedia |

> ⚠️ Jika flag `K` tidak muncul pada cabangX, berarti file `.key` belum terimport. Ulangi import file `.key`.

---

## Langkah 3 — Buat OVPN Client Interface

Buka **Winbox → PPP → Interface → klik tombol + → pilih OVPN Client**

| Field | Nilai | Keterangan |
|---|---|---|
| Name | `ovpn-algate` | Nama interface, bebas |
| Connect To | `103.52.115.28` | IP publik server VPN |
| Port | `1194` | Port OpenVPN |
| **Mode** | **`ethernet`** | **WAJIB untuk ROS 6.x (TAP mode)** |
| Protocol | `tcp` | Harus TCP |
| User | `cabangX` | Nama cabang sesuai sertifikat |
| Password | *(kosong)* | Tidak perlu password |
| Certificate | `cabangX` | Pilih sertifikat client |
| CA Certificate | `ca` | Pilih sertifikat CA |
| Auth | `sha1` | Harus SHA1 |
| Cipher | `aes256` | Harus AES-256 |
| Add Default Route | ☐ tidak dicentang | Jangan centang |

> ⚠️ Mode WAJIB diset ke `ethernet` bukan `ip`. Jika salah, akan muncul error: `IP packet with unknown IP version=0`.

---

## Langkah 4 — Konfigurasi PPP Profile

Pastikan compression dinonaktifkan:

```routeros
/ppp profile set default use-compression=no
```

---

## Langkah 5 — Izinkan API dari Subnet VPN

```routeros
/ip service set api disabled=no port=8728 address=10.8.0.0/24
/ip firewall filter add chain=input src-address=10.8.0.0/24 protocol=tcp dst-port=8728 action=accept comment="Allow API from VPN ALgate Pro"
```

---

## Langkah 6 — Buat Profile PPPoE Isolir

Jalankan sekali saja untuk membuat profile isolir pelanggan menunggak:

```routeros
/ppp profile add name=isolir rate-limit=10k/10k only-one=yes

# Verifikasi
/ppp profile print
```

### Assign isolir ke pelanggan

```routeros
# Aktifkan isolir
/ppp secret set [find name=username_pelanggan] profile=isolir

# Putus sesi aktif agar langsung berlaku
/ppp active remove [find name=username_pelanggan]
```

### Lepas isolir (pulihkan ke normal)

```routeros
# Kembalikan ke profile normal
/ppp secret set [find name=username_pelanggan] profile=default

# Putus sesi agar profile baru berlaku
/ppp active remove [find name=username_pelanggan]
```

> ℹ️ `/ppp active remove` memutus sesi PPPoE aktif. Pelanggan akan reconnect otomatis dengan profile baru. Jika pelanggan sedang offline, perintah ini tidak perlu dijalankan.

---

## Langkah 7 — Verifikasi Koneksi

### Cek status interface

```routeros
/interface ovpn-client print
```

| Flag | Arti |
|---|---|
| R (Running) | Koneksi VPN berhasil, tunnel aktif |
| Tidak ada flag | Koneksi gagal, cek langkah sebelumnya |

### Ping ke server VPN

```routeros
/ping 10.8.0.1 count=4
# 10.8.0.1 adalah IP tunnel VPS server
```

✅ Jika ping berhasil (packet loss = 0), MikroTik sudah terhubung ke server ALgate Pro.

---

## Troubleshooting

| Gejala | Penyebab | Solusi |
|---|---|---|
| Interface tidak flag `R` | Firewall VPS blokir port 1194 | Hubungi administrator VPS |
| `Bad compression stub` | Compression aktif | Langkah 4: `use-compression=no` |
| `IP packet unknown version=0` | Mode `ip` bukan `ethernet` | Langkah 3: ganti Mode ke `ethernet` |
| Certificate not found | Import sertifikat gagal | Ulangi Langkah 2, cek flag `K` |
| Ping timeout ke 10.8.0.1 | FORWARD rules VPS kosong | Hubungi administrator VPS |
| `Bad credentials` | Nama user salah | Pastikan User = nama cabang sertifikat |

---

## Ringkasan Konfigurasi

| Parameter | Nilai |
|---|---|
| Server VPN | 103.52.115.28 : 1194 |
| Protocol | TCP |
| Mode | ethernet (TAP) |
| Cipher | AES-256-CBC |
| Auth | SHA1 |
| use-compression | no |
| IP Tunnel | 10.8.0.x |
| API MikroTik | port 8728, address 10.8.0.0/24 |
| Profile Isolir | rate-limit=10k/10k, only-one=yes |

---

*Panduan ini bagian dari project ALgate Pro - Smart Gateway — Juni 2026.*
