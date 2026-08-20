# Recap Scanner

Aplikasi Android: bubble mengambang di atas app lain → sekali tap → screenshot layar →
OCR otomatis (ML Kit, offline) → data (No Resi, Nama, RT/RW, COD, Status Bayar) tersimpan
ke rekap harian lokal → bisa di-export ke CSV.

## Cara dapat file APK — TANPA install apa-apa di laptop (paling gampang)

Project ini sudah dilengkapi `github action` (`.github/workflows/build.yml`) yang otomatis
membuat APK di server GitHub. Kakak cuma butuh akun GitHub (gratis).

1. Buat akun di [github.com](https://github.com) kalau belum punya.
2. Buat repository baru (New repository), kasih nama bebas misal `recap-scanner`,
   set ke **Public** atau **Private** sama saja, lalu klik **Create repository**.
3. Di halaman repo yang baru dibuat, klik **"uploading an existing file"**.
4. Extract file zip yang aku kasih, lalu **drag & drop semua isi folder `RecapScanner`**
   (bukan folder-nya, tapi isi di dalamnya) ke halaman upload itu. Scroll bawah, klik
   **Commit changes**.
5. Klik tab **Actions** di bagian atas repo → akan ada proses "Build APK" jalan otomatis
   (ada titik kuning berputar). Tunggu sekitar 3-6 menit sampai jadi centang hijau ✅.
6. Kalau sudah hijau, klik proses itu → scroll ke bawah ke bagian **Artifacts** →
   klik **RecapScanner-debug-apk** untuk download. Isinya file `.apk` siap install.
7. Pindahkan file APK itu ke HP (lewat Google Drive/WhatsApp/kabel data), lalu tap
   untuk install. Kalau muncul peringatan "sumber tidak dikenal", izinkan saja
   (ini normal untuk APK di luar Play Store).

Kalau tab Actions tidak otomatis jalan setelah upload, buka tab **Actions** →
pilih workflow **"Build APK"** di kiri → klik **"Run workflow"** → **Run workflow** (hijau).

## Cara build manual pakai Android Studio (alternatif)

1. Install **Android Studio** (versi terbaru, minimal Koala/2024.x).
2. Buka project ini: `File > Open` → pilih folder `RecapScanner`.
3. Biarkan Gradle sync selesai (otomatis download dependency, butuh internet).
4. Sambungkan HP via USB (aktifkan USB debugging) atau pakai emulator.
5. Klik tombol **Run ▶** — atau `Build > Build Bundle(s)/APK(s) > Build APK(s)`
   untuk dapat file .apk tanpa perlu HP tersambung.

## Cara pakai di HP

1. Buka app → tap **"Aktifkan Bubble Scanner"**.
2. Akan muncul 3 dialog izin berturut-turut:
   - **Izin overlay** (tampil di atas app lain) → aktifkan.
   - **Izin notifikasi** (Android 13+) → izinkan.
   - **Izin "mulai merekam atau melakukan siaran layar"** → pilih **Start now/Mulai**.
     (Ini WAJIB, tanpa ini bubble tidak bisa screenshot.)
3. Bubble merah bulat akan muncul mengambang di layar.
4. Buka app apa pun (misal app kurir kamu), pas layar menampilkan data resi →
   **tap bubble** (jangan digeser) → otomatis screenshot + baca teks + masuk rekap.
   Akan muncul toast "Tersimpan: <no resi> - <nama>".
5. Bubble bisa digeser-geser ke posisi mana saja (tahan & drag).
6. Buka app Recap Scanner lagi → tap **"Lihat Rekap"** untuk lihat semua data harian,
   atau tap **"Export CSV"** untuk simpan ke folder Download/RecapScanner.

## PENTING: soal parsing OCR

Parsing (`OcrParser.kt`) dibuat mengikuti pola dari contoh screenshot yang dikasih
(resi format 2-huruf+angka, ada label "COD", "RT xx RW xx", "Belum Bayar"). Kalau app
kurir yang dipakai beda tampilan atau parsing meleset, tinggal:

- Buka `OcrParser.kt`
- Sesuaikan regex `resiRegex`, `rtRwRegex`, `codNominalRegex`
- Sesuaikan logika `extractNama()` dan `extractCodAmount()` sesuai posisi teks
  di app yang dipakai

Setiap data yang tersimpan juga menyimpan **rawText** (teks OCR mentah), jadi kalau
suatu saat ada data yang parsingnya salah, teks aslinya tetap ada dan bisa dicek manual
lewat database (atau tambahkan tampilan rawText di RecapAdapter kalau perlu).

## Batasan teknis

- Izin screen capture (MediaProjection) dari Android akan **hilang setiap kali app
  di-force close / HP restart** — harus buka app & tap "Aktifkan Bubble Scanner" lagi.
- Beberapa HP (Xiaomi/MIUI, Oppo/ColorOS, dll) butuh izin tambahan manual di
  pengaturan "Autostart" / "Battery optimization" supaya service tidak dimatikan
  sistem di background.
- OCR pakai ML Kit bawaan (gratis, jalan offline setelah model ke-download pertama kali).
