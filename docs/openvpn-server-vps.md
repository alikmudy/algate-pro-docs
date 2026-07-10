# Panduan Setup OpenVPN Server di VPS Ubuntu + MikroTik Client

> **Project:** ALgate Pro - Smart Gateway
> **Tanggal:** Juni 2026
> **Status:** ✅ Berhasil Diimplementasikan

---

## Topologi Jaringan

```
[MikroTik Cabang 1] ──┐
[MikroTik Cabang 2] ──┼──(Internet)──► VPS ALgate Pro (10.8.0.1)
[MikroTik Cabang N] ──┘                    │
                                   ALgate Pro Web App
                                   akses via IP tunnel VPN
```

| Node | IP Tunnel | Keterangan |
|---|---|---|
| VPS (OpenVPN Server) | 10.8.0.1 | Server pusat |
| MikroTik Cabang 1 | 10.8.0.2 | client cabang1 |
| MikroTik Cabang 2 | 10.8.0.3 | client cabang2 |

---

## Bagian 0 — Bersihkan VPS Lama (Fresh Install)

> ⚠️ Jalankan ini **sebelum** setup baru jika VPS sudah pernah diinstall OpenVPN sebelumnya.

```bash
# Stop dan disable service
sudo systemctl stop openvpn@server
sudo systemctl disable openvpn@server

# Hapus semua konfigurasi OpenVPN
sudo rm -rf /etc/openvpn/*

# Hapus direktori CA/PKI
rm -rf ~/openvpn-ca

# Uninstall paket
sudo apt remove --purge openvpn easy-rsa -y
```

Setelah selesai, lanjut ke Bagian 1 untuk install ulang dari awal.

---

## Bagian 1 — Setup OpenVPN Server di VPS Ubuntu

### 1.1 Install OpenVPN & EasyRSA

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install openvpn easy-rsa -y
make-cadir ~/openvpn-ca
cd ~/openvpn-ca
```

### 1.2 Konfigurasi vars

```bash
nano vars
```

Edit bagian berikut (pastikan semua field < 64 karakter):

```bash
set_var EASYRSA_REQ_COUNTRY   "ID"
set_var EASYRSA_REQ_PROVINCE  "East Java"
set_var EASYRSA_REQ_CITY      "Surabaya"
set_var EASYRSA_REQ_ORG       "ALgate Pro"
set_var EASYRSA_REQ_EMAIL     "admin@algate.local"
set_var EASYRSA_REQ_OU        "Network"
set_var EASYRSA_KEY_SIZE      2048
set_var EASYRSA_CA_EXPIRE     3650
set_var EASYRSA_CERT_EXPIRE   1080
```

> ⚠️ Jika muncul warning "string is too long", itu hanya warning dan tidak mempengaruhi proses.

### 1.3 Buat CA

```bash
./easyrsa init-pki
./easyrsa build-ca nopass
# Saat ditanya Common Name, ketik: algate
```

### 1.4 Buat Sertifikat Server

```bash
./easyrsa gen-req server nopass
# Saat ditanya Common Name, ketik: server
./easyrsa sign-req server server
# Ketik "yes" untuk konfirmasi
```

### 1.5 Buat DH Parameters

```bash
./easyrsa gen-dh
# Proses ini membutuhkan 1-3 menit
```

### 1.6 Buat Sertifikat Client (per cabang)

```bash
# Cabang 1
./easyrsa gen-req cabang1 nopass
./easyrsa sign-req client cabang1

# Cabang 2
./easyrsa gen-req cabang2 nopass
./easyrsa sign-req client cabang2
```

### 1.7 Salin File ke Direktori OpenVPN

```bash
sudo cp ~/openvpn-ca/pki/ca.crt /etc/openvpn/
sudo cp ~/openvpn-ca/pki/issued/server.crt /etc/openvpn/
sudo cp ~/openvpn-ca/pki/private/server.key /etc/openvpn/
sudo cp ~/openvpn-ca/pki/dh.pem /etc/openvpn/
```

### 1.8 Buat server.conf

```bash
sudo nano /etc/openvpn/server.conf
```

```conf
port 1194
proto tcp
dev tap

ca   /etc/openvpn/ca.crt
cert /etc/openvpn/server.crt
key  /etc/openvpn/server.key
dh   /etc/openvpn/dh.pem

server 10.8.0.0 255.255.255.0
topology subnet

client-config-dir /etc/openvpn/ccd
ifconfig-pool-persist /var/log/openvpn/ipp.txt

keepalive 10 120
cipher AES-256-CBC
auth SHA1

persist-key
persist-tun

status /var/log/openvpn/openvpn-status.log
log-append /var/log/openvpn/openvpn.log
verb 3

client-to-client
```

> **Catatan:** Gunakan `dev tap` (bukan `tun`), `proto tcp`, dan jangan tambahkan `comp-lzo` / `compress` / `plugin`.

### 1.9 Buat Direktori CCD (IP Tetap per Client)

```bash
sudo mkdir -p /etc/openvpn/ccd
sudo mkdir -p /var/log/openvpn

echo "ifconfig-push 10.8.0.2 255.255.255.0" | sudo tee /etc/openvpn/ccd/cabang1
echo "ifconfig-push 10.8.0.3 255.255.255.0" | sudo tee /etc/openvpn/ccd/cabang2
```

> ⚠️ Nama file di `ccd/` harus sama persis dengan Common Name sertifikat client.

### 1.10 Aktifkan IP Forwarding & NAT

```bash
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Ganti eth0 sesuai nama interface publik VPS
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE
sudo iptables -I INPUT -p tcp --dport 1194 -j ACCEPT
sudo iptables -I INPUT -i tun0 -j ACCEPT
sudo iptables -I FORWARD -i tun0 -j ACCEPT
sudo iptables -I FORWARD -o tun0 -j ACCEPT

sudo apt install iptables-persistent -y
sudo netfilter-persistent save
```

### 1.11 Jalankan OpenVPN Server

```bash
sudo systemctl enable openvpn@server
sudo systemctl start openvpn@server
sudo systemctl status openvpn@server

ip a show tun0
# Harus menampilkan IP 10.8.0.1
```

---

## Bagian 2 — Konfigurasi MikroTik Client

Lihat panduan lengkap: [mikrotik-openvpn-client.md](mikrotik-openvpn-client.md)

---

## Bagian 3 — Verifikasi End-to-End

### 3.1 Cek di VPS

```bash
sudo cat /var/log/openvpn/openvpn-status.log
ping 10.8.0.2 -c 4
```

### 3.2 Cek di MikroTik

```routeros
/interface ovpn-client print
/ping 10.8.0.1 count=4
```

---

## Bagian 4 — Menambah Cabang Baru

```bash
cd ~/openvpn-ca
./easyrsa gen-req cabangN nopass
./easyrsa sign-req client cabangN
echo "ifconfig-push 10.8.0.N 255.255.255.0" | sudo tee /etc/openvpn/ccd/cabangN
```

Kemudian download sertifikat, upload ke MikroTik, import, dan buat OVPN Client — sama seperti [Bagian 2](mikrotik-openvpn-client.md).

---

## Troubleshooting

| Gejala | Penyebab | Solusi |
|---|---|---|
| Interface tidak flag `R` | Firewall blokir port 1194 | `iptables -I INPUT -p tcp --dport 1194 -j ACCEPT` |
| `Bad compression stub` | Mismatch compression | Hapus semua baris `comp-lzo` di server.conf |
| `IP packet with unknown IP version=0` | Mode `ip` di MikroTik | Ganti ke mode `ethernet` |
| Ping timeout meski Running | FORWARD rules kosong | Tambahkan rule FORWARD untuk tun0 |
| `string is too long` saat gen CA | Field vars > 64 karakter | Persingkat nilai di file vars |
| OpenVPN gagal start | Ada baris `plugin` di server.conf | Hapus baris `plugin` |

---

## Ringkasan Konfigurasi Final

**VPS Ubuntu 20.04 (OpenVPN 2.4)**

| Parameter | Value |
|---|---|
| proto | tcp |
| dev | tap |
| cipher | AES-256-CBC |
| auth | SHA1 |
| topology | subnet |
| compress | TIDAK ADA |

---

*Panduan ini bagian dari project ALgate Pro - Smart Gateway — Juni 2026.*
