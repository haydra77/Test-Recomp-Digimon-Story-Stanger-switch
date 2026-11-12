# Uji kelayakan (probe) untuk Digimon Story: Time Stranger

Tujuan: mengetahui apakah generator (`tools/gen.py`) bisa mengubah binary utama game menjadi kode C. Ini **bukan**
cara menjalankan game. Hasilnya hanya log teks. Kode C yang dihasilkan mode `--probe` tidak berguna dan tidak boleh
di-commit.

Penting:

- Pakai dump **Switch 1** (bukan versi Switch 2) dan versi yang sama dengan yang nanti mau dipakai (disarankan Ver. 1.2.1).
- Jangan pernah commit file game, `prod.keys`, atau `title.keys`. Jangan tempel `prod.keys` ke chat. File `main`
  juga jangan ditempel atau diunggah ke mana pun selain ke komputer cloud pribadimu sendiri.
- Mode `--probe` belum saya jalankan sendiri (lingkungan saya tidak punya unicorn/capstone/lz4 dan tidak bisa
  memasangnya). Kalau ada error Python yang aneh, tempel log-nya dan kita perbaiki.

## 1. Ambil file `main` dari ExeFS

Kamu butuh file bernama `main` dari ExeFS program utama game (setelah update diterapkan). Cara mendapatkannya
tergantung alat dump yang kamu pakai di Switch atau di komputer; ikuti dokumentasi alat itu (misalnya hactool atau
nstool untuk NSP/XCI, yang membutuhkan `prod.keys` milikmu sendiri dan, untuk NSP bertiket, title key dari `.tik`).
Catat juga **nama dan ukuran semua file** di folder `exefs` (misalnya `main`, `rtld`, `sdk`, `subsdk0`, ...).

## 2. Tempat menjalankan generator

Generator butuh Python dengan paket `unicorn`, `capstone`, dan `lz4`.

**Jalur A (disarankan, murni dari HP): GitHub Codespaces.** Buka repo ini di browser, tekan Code → Codespaces →
Create codespace. Itu memberi Linux di cloud dengan pip yang berfungsi. Ada kuota gratis bulanan; cek di akun GitHub-mu.
Unggah `main` lewat klik kanan di panel Explorer → Upload, ke folder **di luar** repo (misalnya `~/dump/main`).
Kalau kamu butuh `prod.keys` di sana, simpan di `~/.switch/prod.keys`, juga di luar folder repo.

**Jalur B: Termux di HP.** Mungkin berhasil, tapi saya tidak bisa menjamin: `unicorn` dan `capstone` dibangun dari
sumber dan butuh `clang` dan `cmake`. Dokumen `docs/android-ondevice-build.md` mencatat bahwa keduanya memang bisa
dibangun untuk aarch64.

## 3. Jalankan

```bash
cd tools
python3 -m venv ../venv
../venv/bin/pip install unicorn capstone lz4
../venv/bin/python gen.py main ~/dump/main 0x7000000000 /tmp/probe_out \
    --probe --limit 300 --simd c 2>&1 | tee probe_main.log
```

Catatan:

- `--limit 300` hanya menulis 300 fungsi pertama, tapi analisis awal tetap memindai seluruh binary, jadi bisa
  makan waktu lama (puluhan menit tidak aneh) dan memori besar.
- Basis alamat `0x7000000000` mengikuti `Makefile` upstream. Kalau game ini memuat modul dengan susunan lain, itu
  salah satu hal yang kita pelajari dari uji ini.

## 4. Yang ditempel ke chat

Dari `probe_main.log`: baris yang berisi `PROBE: main build ID ...`, baris `boundaries, ... functions, ...`, dan
30 baris terakhir (atau seluruh traceback kalau gagal). Ditambah daftar nama dan ukuran file di `exefs`.

## 5. Cara membaca hasilnya

- **Selesai dengan "wrote N chunks"**: lifter bisa memproses binary-nya. Lanjut ke tahap berikutnya (tabel alamat
  dan patch khusus game).
- **Banyak `Unsupported` atau error instruksi**: lifter perlu diperluas dulu untuk instruksi yang dipakai game ini.
- **Kehabisan memori atau tidak selesai**: batas sumber daya perangkat. Kita bahas apakah ada cara membaginya.
