<h1 align="center">Writeup — FGTE 2025</h1>

<p align="center">
  <i>Future Generation of Technology Enthusiast — Capture The Flag</i><br>
  Dokumentasi penyelesaian oleh <b>Bagaskara Amukti Palapa</b>
</p>

<p align="center">
  <a href="https://github.com/BagaskaraAP-Dev">
    <img src="https://img.shields.io/badge/author-BagaskaraAP--Dev-1A1B27?style=flat-square&logo=github&logoColor=7AA2F7&labelColor=1A1B27" alt="GitHub">
  </a>
  <img src="https://img.shields.io/badge/event-FGTE%202025-7AA2F7?style=flat-square" alt="Event">
  <img src="https://img.shields.io/badge/challenges-5%20solved-9ECE6A?style=flat-square" alt="Solved">
  <img src="https://img.shields.io/badge/categories-Forensics%20%7C%20Misc%20%7C%20RE%20%7C%20Web-FF7043?style=flat-square" alt="Categories">
</p>

---

## Ringkasan

Repositori ini memuat *writeup* lengkap untuk **lima challenge** yang saya selesaikan di **FGTE 2025**, mencakup kategori **Forensics**, **Misc**, **Reverse Engineering**, dan **Web**. Setiap writeup ditulis langkah demi langkah — dari *reconnaissance*, analisis kerentanan, eksploitasi, hingga mitigasi — disertai bukti (tangkapan layar, berkas artefak, dan skrip yang dapat dijalankan ulang).

Tujuannya bukan sekadar mencatat flag, tapi membuat setiap langkah **dapat direproduksi** dan **dapat dipelajari** oleh siapa pun yang membacanya.

## Daftar Challenge

| # | Challenge | Kategori | Tingkat | Poin | Writeup |
|:-:|---|---|:-:|:-:|:-:|
| 1 | **Astaga !!!** | Forensics | 🟢 Easy | — | [Buka →](Astaga-Forensics-Easy/writeup.md) |
| 2 | **mancing** | Forensics | 🟢 Easy | 40 | [Buka →](Mancing-Forensics-Easy/writeup.md) |
| 3 | **unzip ini!!** | Misc | 🟢 Easy | — | [Buka →](unzip-Misc-Easy/writeup.md) |
| 4 | **activation** | Reverse Engineering | 🟡 Medium | 80 | [Buka →](activation-ReverseEngineering-Medium/writeup.md) |
| 5 | **euphonium** | Web | 🔴 Hard | 160 | [Buka →](euphonium-Web-Hard/writeup.md) |

## Ikhtisar Teknik

<table>
<tr><td width="33%" valign="top">

### 🔍 Astaga !!! — Forensics
Analisis trafik **HTTP** dari `.pcap` dengan Wireshark. Merekonstruksi kronologi penyerang yang menyalahgunakan *edit profile* untuk membajak akun `admin`, lalu mengidentifikasi **username awal** pelaku sebagai flag.

`Wireshark` · `HTTP forensics` · `account takeover`

</td><td width="33%" valign="top">

### 🎣 mancing — Forensics
**Manipulasi dimensi PNG.** Nilai *height* di chunk **IHDR** dikecilkan (800) padahal data IDAT menyimpan 900 baris. Koreksi height + **CRC** memunculkan baris tersembunyi berisi flag.

`PNG internals` · `IHDR` · `CRC` · `zlib`

</td><td width="33%" valign="top">

### 🗜️ unzip ini!! — Misc
Perbaikan **header ZIP** yang dirusak (`AA BB CC…` → `50 4B 03 04 14 00`), *password* dari nama berkas, lalu *decode* **Base64 → PNG** yang ternyata **QR code** berisi flag.

`hex repair` · `ZIP` · `Base64` · `QR`

</td></tr>
<tr><td width="33%" valign="top">

### ⚙️ activation — Reverse Engineering
Binary **.NET** dengan validasi lisensi deterministik. Rekonstruksi lisensi via `HMAC-SHA256(salt, user)`, turunkan kunci **AES-128-CBC**, dan dekripsi *secret* sepenuhnya **offline**.

`.NET` · `HMAC` · `AES-CBC` · `offline solve`

</td><td width="33%" valign="top">

### 🔐 euphonium — Web
**JWT HS256 dengan secret lemah.** Menghindari jebakan *stego* (3 flag palsu), menyusun *wordlist* terarah dari data pribadi target dengan **`cupp`** (OSINT), mem-*crack* secret, lalu **memalsukan token** `role:admin`.

`JWT` · `HMAC crack` · `OSINT` · `cupp`

</td><td width="33%" valign="top">

### 📊 Rekap
**5 challenge · 4 kategori**
Forensics ×2 · Misc ×1 · RE ×1 · Web ×1
Semua flag **terverifikasi**.

</td></tr>
</table>

## Struktur Repositori

```
.
├── Astaga-Forensics-Easy/
│   ├── writeup.md
│   ├── images/              # bukti analisis trafik HTTP
│   └── files/               # Mistakez.pcap
├── Mancing-Forensics-Easy/
│   ├── writeup.md
│   └── writeup-original.pdf # versi dengan tangkapan layar asli
├── unzip-Misc-Easy/
│   ├── writeup.md
│   ├── images/              # perbaikan header, QR asli, bukti
│   └── files/               # arsip rusak + versi perbaikan
├── activation-ReverseEngineering-Medium/
│   ├── writeup.md
│   └── writeup-original.pdf # versi dengan tangkapan layar asli
└── euphonium-Web-Hard/
    ├── writeup.md
    ├── exploit/solve.py     # cupp-gen → crack → forge → fetch (stdlib)
    ├── screenshots/         # bukti langkah demi langkah
    └── artifacts/           # gambar decoy + analisis jebakan
```

## Catatan

- Seluruh flag telah **diverifikasi** benar.
- *Password* dan kredensial mentah di dalam PCAP **disamarkan** (`[REDACTED]`) pada narasi writeup.
- Writeup **mancing** dan **activation** menyertakan PDF asli (`writeup-original.pdf`) yang memuat tangkapan layar bukti.
- Konten ini dibuat untuk **dokumentasi dan pembelajaran** keamanan siber. Seluruh pengujian dilakukan terhadap target resmi masing-masing challenge dalam konteks kompetisi.

---

<p align="center">
  <b>Bagaskara Amukti Palapa</b> · <a href="https://github.com/BagaskaraAP-Dev">@BagaskaraAP-Dev</a><br>
  <sub>Keep learning, keep building. 🔥</sub>
</p>
