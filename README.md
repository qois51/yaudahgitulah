## 1. Tabel Prefix & Subnet Mask (Wajib Hafal)
Fokus pada **Oktet Terakhir**. Hafalkan **Block Size** (Ukuran Blok) karena ini kunci menghitung kelipatan Network ID.

| Prefix | Subnet Mask (Akhir) | Block Size | Wildcard Mask | Host Valid | Kegunaan Umum |
| :---: | :---: | :---: | :---: | :---: | :--- |
| **/24** | `.0` | **256** | `0.0.0.255` | 254 | LAN Standar |
| **/25** | `.128` | **128** | `0.0.0.127` | 126 | Membagi 2 Network |
| **/26** | `.192` | **64** | `0.0.0.63` | 62 | LAN Sedang (50-60 host) |
| **/27** | `.224` | **32** | `0.0.0.31` | 30 | LAN Kecil (20-30 host) |
| **/28** | `.240` | **16** | `0.0.0.15` | 14 | Lab Kecil |
| **/30** | `.252` | **4** | `0.0.0.3` | **2** | **Link Antar Router** |

---

## 2. Aturan Emas VLSM (Variable Length Subnet Mask)

### Langkah Pengerjaan
1.  **Urutkan:** Selalu kerjakan dari kebutuhan Host **TERBANYAK** ke **TERKECIL**.
2.  **Rapat:** Network berikutnya dimulai persis **1 angka setelah Broadcast** network sebelumnya.

### ⚠️ Hukum "Kelipatan Blok" (Network ID)
Network Address **HARUS** kelipatan dari Block Size-nya.
* ❌ **Salah:** Network /26 (Block 64) ditaruh di `.130` (Bukan kelipatan 64).
* ✅ **Benar:** Network /26 harus ditaruh di `.0`, `.64`, `.128`, atau `.192`.

### 🚫 IP Terlarang (Prohibited)
Jangan pasang IP ini di PC atau Gateway Router:
1.  **Network ID:** Angka pertama di blok (misal: `.0`, `.64`).
2.  **Broadcast ID:** Angka terakhir di blok (misal: `.63`, `.127`).

---

## 3. Rumus Cepat Wildcard Mask
Digunakan untuk konfigurasi OSPF.

**Rumus:**
`Block Size - 1`

**Contoh:**
* Prefix **/26** (Mask .192) -> Block Size **64** -> Wildcard **63** (`0.0.0.63`)
* Prefix **/30** (Mask .252) -> Block Size **4** -> Wildcard **3** (`0.0.0.3`)

---

## 4. Konfigurasi OSPF (Cisco IOS)

Lakukan langkah ini di **SETIAP** Router.

```bash
! 1. Masuk Mode OSPF
Router(config)# router ospf 1

! 2. Daftarkan SEMUA Network (Milik Sendiri)
! Format: network [Network_ID] [Wildcard] area 0
Router(config-router)# network 200.31.16.0 0.0.0.255 area 0
Router(config-router)# network 200.31.17.128 0.0.0.63 area 0

! 3. Pasang Passive Interface (Ke arah PC/Switch)
! Agar tidak kirim paket Hello ke PC (Security & Efficiency)
Router(config-router)# passive-interface FastEthernet0/0
