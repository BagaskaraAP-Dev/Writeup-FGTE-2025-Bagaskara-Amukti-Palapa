# activation — FGTE 2025

| | |
|---|---|
| **Category** | Reverse Engineering |
| **Difficulty** | Medium |
| **Points** | 80 |
| **Author** | aria |
| **File** | `files/activator.exe` |

> Aplikasi meminta *username* dan sebuah lisensi yang harus cocok dengan identitas tersebut. Telusuri validasi aktivasi untuk mendapatkan *secret* yang disimpan dalam binary.

**🚩 Flag:** `FGTE{L!c3n$3_4cTiv@t10n_7xY9_b2Q8R5s}`

**Hasil akhir:** Username `RANI` · License `0905E413DDF6D48B`

> Versi dengan tangkapan layar asli tersedia di [`writeup-original.pdf`](writeup-original.pdf).

---

## TL;DR

Binary `.NET` dengan validasi lisensi lokal yang **deterministik**. Lisensi sah dihitung dari `HMAC-SHA256(salt, username)` (8 byte pertama, hex uppercase). Lisensi itu lalu menjadi material kunci **AES-128-CBC** untuk mendekripsi *secret* (flag) yang tertanam di binary. Semua bisa direkonstruksi **offline**, tanpa menjalankan `.exe`.

---

## 1. Identifikasi executable

```bash
file activator.exe          # PE32 Windows, .NET assembly
strings -a -el activator.exe   # string UTF-16: pesan validasi + data Base64
```

Program adalah PE32 Windows berisi assembly **.NET**. String UTF-16 memperlihatkan pesan validasi dan data Base64 yang relevan.

## 2. Menemukan data tersembunyi

Dari metadata .NET dan CIL ditemukan fungsi: `GetEmbeddedUsername`, `GetSalt`, `ExpectedLicense`, `DeriveKeyFromLicense`, dan `DecryptBlob`.

```python
import base64

user = base64.b64decode("UkFOSQ==").decode()                 # RANI
salt = base64.b64decode("c2FsdF9wYXJ0XzFfZnJvbV9jcmU=")      # salt_part_1_from_cre
```

- **Username** tersembunyi sebagai Base64 `UkFOSQ==` → `RANI`.
- **Salt** dibangun dari potongan Base64 → `salt_part_1_from_cre`.
- Temuan tambahan: data *mask* 8 byte di `.sdata` di-XOR dengan `0x33` → menghasilkan `fgte_ctf`.

## 3. Menghasilkan lisensi yang benar

`ExpectedLicense` menghitung `HMAC-SHA256(salt, username)`, mengambil **8 byte pertama**, lalu mengonversi ke hex uppercase.

```python
import hmac, hashlib

license = hmac.new(salt, user.encode(), hashlib.sha256).digest()[:8].hex().upper()
print(license)   # 0905E413DDF6D48B
```

## 4. Dekripsi secret dengan AES

`DeriveKeyFromLicense` menghitung `SHA256(license + Base64(salt))`, mengambil **16 byte pertama** untuk AES-128.

```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes

key = hashlib.sha256((license + base64.b64encode(salt).decode()).encode()).digest()[:16]
# ab5ca2c49a6e6f4425eb73d97f603242

# encryptedBlob = 64 byte dari FieldRVA kedua:
#   16 byte pertama = IV, 48 byte sisanya = ciphertext AES-CBC (PKCS7)
iv, ciphertext = encrypted_blob[:16], encrypted_blob[16:]
dec = Cipher(algorithms.AES(key), modes.CBC(iv)).decryptor()
padded = dec.update(ciphertext) + dec.finalize()
# lanjutkan dengan PKCS7 unpadding → flag
```

## 5. Verifikasi lewat program (Windows)

```powershell
PS C:\CTF> .\activator.exe RANI 0905E413DDF6D48B
Activator v1.0
Activation OK. Here is your secret:
FGTE{L!c3n$3_4cTiv@t10n_7xY9_b2Q8R5s}
```

> Dependensi solver: `pip install cryptography`, lalu `python solve_activation.py activator.exe`.

## 6. Flag

```text
FGTE{L!c3n$3_4cTiv@t10n_7xY9_b2Q8R5s}
```

## 7. Kesimpulan

Program memakai validasi lisensi lokal yang deterministik dengan *secret* terenkripsi. Dengan membaca Base64 dan metadata .NET, kita merekonstruksi lisensi yang sah, menurunkan kunci AES, lalu mendekripsi flag sepenuhnya secara offline — tanpa perlu menjalankan binary-nya.
