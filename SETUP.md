# Panduan Setup — Sistem Pelaporan Dialog PPD × SISC+

Sistem tiga bahagian: **frontend** (`index.html` di GitHub Pages) · **backend** (`Code.gs` di Google Apps Script) · **pangkalan data** (Google Sheets).

Sekarang `index.html` berjalan dalam **mod demo** — boleh terus buka & tunjuk bos tanpa setup apa-apa. Langkah di bawah untuk go-live dengan data sebenar.

---

## 1. Pasang backend & bina pangkalan data secara automatik

1. Buat satu Google Sheet baharu.
2. **Extensions → Apps Script**, padam kod contoh, tampal keseluruhan `Code.gs`.
3. Di bar atas editor, pilih fungsi **`setupSistem`** → tekan **Run**.
   (Kali pertama Google minta kebenaran — luluskan.)
4. Fungsi ini mencipta **5 tab** lengkap dengan header dan **akaun permulaan** secara automatik:

   | Tab | Kegunaan |
   |---|---|
   | `Pengguna` | Semua akaun (Username, PIN, Nama, Peranan, Sektor, PPD, Panel, Aktif) |
   | `Laporan` | Kepala setiap laporan |
   | `Tindakan` | Satu baris satu intervensi (dikemas oleh Meja Sektor) |
   | `Sektor` | 14 sektor JPN Kedah (auto-diisi) |
   | `Config` | `PROGRAM_START` = 2026-05-11 (auto-diisi) |

   > **Selamat dijalankan berulang** — tab atau data yang sudah wujud tidak akan ditimpa.

**Akaun permulaan yang dibuat** (satu bagi setiap peranan untuk uji terus 4 paparan):

| Peranan | Username | PIN |
|---|---|---|
| Admin | `admin` | `admin` |
| Pengurusan | `pengarah` | `1234` |
| Meja Sektor | `meja1` | `1234` |
| Meja Sektor | `meja2` | `1234` |
| SISC+ | `nurul` | `1234` |

> Tambah pegawai SISC+ & Meja Sektor sebenar melalui tab `Pengguna` atau panel **Admin** dalam sistem. Peranan sah: `admin`, `pengurusan`, `meja`, `sisc`. Lajur `Sektor` untuk **meja** sahaja; `PPD` + `Panel` untuk **sisc** sahaja.

---

## 2. Deploy backend sebagai Web App

1. Dalam editor Apps Script: **Deploy → New deployment → Web app.**
   - Execute as: **Me**
   - Who has access: **Anyone**
2. Salin **Web app URL**.

---

## 3. Wayar frontend ke backend

Dalam `index.html`, cari blok `CONFIG` di bahagian atas `<script>`:

```js
const CONFIG = { DEMO_MODE:true, GAS_URL:'', PROGRAM_START:'2026-05-11' };
```

Tukar kepada:

```js
const CONFIG = { DEMO_MODE:false, GAS_URL:'URL_WEB_APP_ANDA', PROGRAM_START:'2026-05-11' };
```

### Sambungan live sudah TERBINA

Tak perlu edit kod lagi — cukup tukar `DEMO_MODE:false` dan isi `GAS_URL`. Semua operasi ini automatik memanggil `Code.gs` (guna `fetch` POST `text/plain`, tiada isu CORS):

| Operasi | Action GAS |
|---|---|
| Log masuk | `login` |
| Muat semua data selepas login | `bootstrap` |
| SISC+ hantar laporan | `submitLaporan` |
| Meja Sektor kemas status/catatan | `updateTindakan` |
| Admin tambah pengguna | `addUser` |

Bila `DEMO_MODE:false`, skrin login menyembunyikan butang akaun demo dan footer menunjukkan "Mod Live".

---

## 4. Hos frontend di GitHub Pages

1. Cipta repo, muat naik `index.html`.
2. **Settings → Pages → Deploy from branch → main / root.**
3. Sistem sedia di `https://<username>.github.io/<repo>/`.

---

## Akaun demo (mod demo)

| Peranan | Username | Kata laluan |
|---|---|---|
| Admin | `admin` | `admin` |
| Pengurusan Tertinggi | `pengarah` | `1234` |
| Meja Sektor | `meja1` | `1234` |
| Pegawai SISC+ | `nurul` | `1234` |

Skrin login ada butang pintas untuk keempat-empat akaun ini.

---

## Nota keselamatan

PIN disimpan sebagai teks biasa dalam tab `Pengguna` (selari dengan sistem dalaman JPN sedia ada). Sesuai untuk kegunaan dalaman. Jika perlu lebih ketat kemudian, boleh naik taraf ke pengesahan Google (senarai e-mel dibenarkan).
