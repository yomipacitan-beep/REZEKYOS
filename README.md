# REZEKI OS — Proyek Android (APK)

Aplikasi Android ini membungkus REZEKI OS (file `app/src/main/assets/index.html`) dalam WebView.
Semua data tetap tersimpan **lokal di HP** (IndexedDB), aplikasi bisa dipakai **tanpa internet**,
dan ekspor JSON / CSV / PDF tersimpan di folder **Download/RezekiOS**.

## Cara 1 — Android Studio (paling mudah di laptop)
1. Pasang Android Studio (gratis): https://developer.android.com/studio
2. Ekstrak zip ini, lalu **File → Open** → pilih folder `rezeki-os-android`.
3. Tunggu proses *Gradle Sync* selesai (pertama kali butuh internet untuk mengunduh Android SDK & plugin).
4. Menu **Build → Build App Bundle(s) / APK(s) → Build APK(s)**.
5. Klik **locate** pada notifikasi. File ada di `app/build/outputs/apk/debug/app-debug.apk`.
6. Kirim APK ke HP (WhatsApp/Drive/kabel), buka, izinkan "Instal dari sumber tidak dikenal".

## Cara 2 — GitHub Actions (tanpa memasang apa pun)
1. Buat repositori baru di GitHub, unggah seluruh isi folder ini (termasuk folder `.github`).
2. Buka tab **Actions** → workflow **Build APK** berjalan otomatis (atau klik *Run workflow*).
3. Setelah selesai (±5 menit), unduh **RezekiOS-debug-apk** di bagian *Artifacts*.

## Memperbarui isi aplikasi
Ganti `app/src/main/assets/index.html` dengan versi terbaru REZEKI OS, naikkan `versionCode`
dan `versionName` di `app/build.gradle`, lalu build ulang. Data pengguna di HP tidak hilang saat update
(selama `applicationId` dan kunci tanda tangan sama).

## APK rilis (untuk Play Store / distribusi luas)
1. Buat keystore sekali saja:
   `keytool -genkeypair -v -keystore rezeki-release.jks -keyalg RSA -keysize 2048 -validity 10000 -alias rezeki`
2. Buat file `keystore.properties` di folder proyek:
   ```
   storeFile=../rezeki-release.jks
   storePassword=PASSWORD_ANDA
   keyAlias=rezeki
   keyPassword=PASSWORD_ANDA
   ```
3. Jalankan `./gradlew assembleRelease` (APK) atau `./gradlew bundleRelease` (AAB untuk Play Store).
   **Simpan keystore baik-baik** — tanpa itu Anda tidak bisa memperbarui aplikasi yang sudah terpasang.

## Catatan teknis
- Paket: `com.rezekios.app` · minSdk 24 (Android 7.0) · targetSdk 35 · AGP 8.7.3 · Gradle 8.11.1 · Java 17.
- Halaman dimuat dari `https://appassets.androidplatform.net/assets/` (WebViewAssetLoader) agar berjalan
  dalam *secure context*: IndexedDB permanen, clipboard berfungsi, tanpa izin internet.
- Tombol Kembali HP menelusuri riwayat halaman aplikasi.
- Impor cadangan JSON memakai pemilih file Android.
- AI Coach memakai Coach Lokal (offline). Mode AI hanya tersedia bila Anda mengisi endpoint proxy sendiri.
- Data aplikasi web di browser/claude.ai **tidak otomatis pindah** ke APK: ekspor JSON di sana, lalu impor di APK.
