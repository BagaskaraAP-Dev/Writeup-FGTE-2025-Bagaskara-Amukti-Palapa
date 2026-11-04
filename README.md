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
  <img src="https://img.shields.io/badge/challenges-3%20solved-9ECE6A?style=flat-square" alt="Solved">
  <img src="https://img.shields.io/badge/categories-Forensics%20%7C%20Misc%20%7C%20Web-FF7043?style=flat-square" alt="Categories">
</p>

---

## Ringkasan

Repositori ini memuat *writeup* lengkap untuk tiga *challenge* yang saya selesaikan di **FGTE 2025**, mencakup kategori **Forensics**, **Misc**, dan **Web**. Setiap writeup ditulis langkah demi langkah — dari *reconnaissance*, analisis kerentanan, eksploitasi, hingga mitigasi — disertai bukti (tangkapan layar, berkas artefak, dan skrip yang dapat dijalankan ulang).

Tujuannya bukan sekadar mencatat flag, tapi membuat setiap langkah **dapat direproduksi** dan **dapat dipelajari** oleh siapa pun yang membacanya.

## Daftar Challenge

| # | Challenge | Kategori | Tingkat | Writeup |
|:-:|---|---|:-:|:-:|
| 1 | **Astaga !!!** | Forensics | 🟢 Easy | [Buka →](Astaga-Forensics-Easy/writeup.md) |
| 2 | **unzip ini!!** | Misc | 🟢 Easy | [Buka →](unzip-Misc-Easy/writeup.md) |
| 3 | **euphonium** | Web | 🔴 Hard | [Buka →](euphonium-Web-Hard/writeup.md) |

## Ikhtisar Teknik

<table>
<tr><td width="33%" valign="top">

### 🔍 Astaga !!! — Forensics
Analisis trafik **HTTP** dari berkas `.pcap` menggunakan Wireshark. Merekonstruksi kronologi penyerang yang menyalahgunakan fitur *edit profile* untuk membajak akun `admin`, lalu mengidentifikasi **username awal** yang didaftarkan pelaku sebagai flag.

`Wireshark` · `HTTP forensics` · `account takeover`

</td><td width="33%" valign="top">

### 🗜️ unzip ini!! — Misc
Perbaikan **header ZIP** yang sengaja dirusak (`AA BB CC…` → `50 4B 03 04 14 00`), menentukan *password* dari nama berkas, lalu men-*decode* konten **Base64 → PNG** yang ternyata sebuah **QR code** berisi flag.

`hex repair` · `ZIP internals` · `Base64` · `QR`

</td><td width="33%" valign="top">

### 🔐 euphonium — Web
Kerentanan **JWT HS256 dengan secret lemah**. Menghindari jebakan *stego* (3 flag palsu), menyusun *wordlist* terarah dari data pribadi target dengan **`cupp`** (OSINT password profiling), mem-*crack* secret, lalu **memalsukan token** `role:admin` untuk mengambil flag.

`JWT` · `HMAC crack` · `OSINT` · `cupp`

</td></tr>
</table>

## Struktur Repositori

```
.
├── Astaga-Forensics-Easy/
│   ├── writeup.md
│   ├── images/              # bukti analisis trafik HTTP
│   └── files/               # Mistakez.pcap
├── unzip-Misc-Easy/
│   ├── writeup.md
│   ├── images/              # perbaikan header, QR asli, bukti
│   └── files/               # arsip rusak + versi perbaikan
└── euphonium-Web-Hard/
    ├── writeup.md
    ├── exploit/solve.py     # cupp-gen → crack → forge → fetch (stdlib)
    ├── screenshots/         # bukti langkah demi langkah
    └── artifacts/           # gambar decoy + analisis jebakan
```

## Catatan

- Seluruh flag telah **diverifikasi** benar.
- *Password* dan kredensial mentah di dalam PCAP **disamarkan** (`[REDACTED]`) pada narasi writeup.
- Konten ini dibuat untuk **dokumentasi dan pembelajaran** keamanan siber. Seluruh pengujian dilakukan terhadap target resmi masing-masing challenge dalam konteks kompetisi.

---

<p align="center">
  <b>Bagaskara Amukti Palapa</b> · <a href="https://github.com/BagaskaraAP-Dev">@BagaskaraAP-Dev</a><br>
  <sub>Keep learning, keep building. 🔥</sub>
</p>
