# mancing — FGTE 2025

| | |
|---|---|
| **Category** | Forensics |
| **Difficulty** | Easy |
| **Points** | 40 |
| **Author** | aria |
| **File** | `files/mancing.png` |

> *Mancing* — ada sesuatu yang tersembunyi di bawah permukaan.

**🚩 Flag:** `FGTE{ukuranku_kurang_besar_bang}`

> Versi dengan tangkapan layar asli tersedia di [`writeup-original.pdf`](writeup-original.pdf).

---

## TL;DR

`mancing.png` tampak normal (1440 × 800), tapi data IDAT-nya menyimpan **900 baris**, bukan 800. Nilai *height* pada chunk **IHDR** sengaja dikecilkan sehingga 100 baris terakhir — tempat flag berada — tidak dirender. Kembalikan height ke 900, perbarui **CRC IHDR**, dan baris tersembunyi muncul.

---

## 1. Analisis Forensik

### 1.1 Decoy di metadata

Field XMP `dc:title` memuat `FGTE{WW5WcllYNGdaR2x6YVc1cA==}`. Terlihat seperti flag, tapi isi dalam kurung adalah **Base64 yang di-encode dua kali**:

```text
Decode 1 : WW5WcllYNGdaR2x6YVc1cA==  →  YnVrYW4gZGlzaW5p
Decode 2 : YnVrYW4gZGlzaW5p          →  bukan disini
```

Artinya *"bukan di sini"* — ini pengalih perhatian, bukan lokasi flag.

### 1.2 Ketidaksesuaian tinggi IHDR

| Atribut | Temuan |
|---|---|
| Dimensi pada IHDR | 1440 × **800** piksel |
| Panjang raw scanline IDAT (setelah `zlib` decompress) | 3.888.900 byte |
| Dimensi nyata dari data | 1440 × **900** piksel |

Untuk PNG RGB 8-bit non-interlaced, setiap baris = 1 byte filter + (1440 × 3) byte piksel. Maka:

```text
3.888.900 ÷ (1 + 1440 × 3) = 900 baris
```

Nilai height di IHDR harus dikoreksi:

```text
IHDR height : 00 00 03 20  (800)  →  00 00 03 84  (900)
```

## 2. Eksploitasi & Validasi

Tidak perlu mengubah data piksel atau *brute-force* steganografi. Cukup baca IDAT untuk menghitung tinggi asli, ubah *height* pada offset `0x14–0x17` (desimal 20–23), lalu hitung ulang **CRC** chunk IHDR.

```python
from pathlib import Path
import struct, zlib

data = bytearray(Path("mancing.png").read_bytes())
width, old_h = struct.unpack_from(">II", data, 16)

pos, idat = 8, []
while pos + 12 <= len(data):
    size = struct.unpack_from(">I", data, pos)[0]
    kind = data[pos+4:pos+8]
    if kind == b"IDAT":
        idat.append(bytes(data[pos+8:pos+8+size]))
    pos += 12 + size
    if kind == b"IEND":
        break

raw = zlib.decompress(b"".join(idat))
assert (data[24], data[25], data[28]) == (8, 2, 0)   # bit depth 8, color type 2 (RGB)
height = len(raw) // (1 + width * 3)

struct.pack_into(">I", data, 20, height)             # koreksi height di IHDR
struct.pack_into(">I", data, 29, zlib.crc32(data[12:29]))  # recompute CRC IHDR
Path("mancing_revealed.png").write_bytes(data)
print(f"{width}x{old_h} -> {width}x{height}")
```

Setelah dipulihkan, baris 801–900 kembali terlihat dan flag muncul sebagai teks biru muda di sudut kanan bawah.

## 3. Flag

```text
FGTE{ukuranku_kurang_besar_bang}
```

## 4. Kesimpulan

Teknik inti: **manipulasi dimensi PNG pada IHDR**. Data gambar tidak pernah hilang — pembaca PNG hanya diberi batas tinggi yang terlalu kecil. Mengoreksi *height* dan CRC menampilkan 100 baris tersembunyi beserta flag.
