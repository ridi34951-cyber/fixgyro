# GyroLock Android — prototipe stabilisasi gyro

## Fitur
- Kamera dalam aplikasi (CameraX).
- Merekam timestamp sensor gyroscope bersamaan dengan video.
- Setelah rekaman berhenti, menyimpan log CSV dan otomatis menjalankan FFmpeg.
- Merekam data gyroscope XYZ dengan timestamp monotonic (sekitar 100 Hz) dan menyimpan log CSV.
- Mengintegrasikan ketiga sumbu gyro menjadi estimasi lintasan rotasi, menghaluskan lintasan sekitar 0,35 detik, lalu membuat koreksi berinterpolasi terhadap waktu video.
- Koreksi roll memakai filter rotate; gerakan pitch/yaw dipakai untuk pergeseran crop, lalu video diperbesar kembali untuk menyembunyikan tepi kosong.
- GitHub Actions membangun debug APK dan mengunggahnya sebagai artifact.

## Batasan penting
Ini implementasi awal stabilisasi gyro XYZ, bukan jaminan setara Gyroflow atau EIS bawaan pabrik. Konversi pitch/yaw menjadi pergeseran piksel masih memakai perkiraan, belum membaca metadata focal length/lens calibration, belum mengompensasi rolling shutter, dan sinkronisasi awal masih bergantung pada waktu event CameraX. Orientasi sumbu bisa berbeda menurut perangkat, sehingga wajib diuji dengan video nyata. Integrasi gyro juga dapat drift; video dengan gerakan ekstrem mungkin tetap bergoyang atau terpotong. Jika FFmpeg gagal pada video panjang, periksa status aplikasi dan log GitHub/FFmpeg.
Hasil dan video mentah disimpan pada direktori internal aplikasi `Android/data/com.ridiitols.gyrolock/files/GyroLock/` (akses bisa dibatasi oleh Android).

## Build APK
1. Buat repo GitHub lalu upload seluruh isi folder ini (termasuk `.github/workflows/build.yml`).
2. Buka tab **Actions** dan jalankan workflow **Build GyroLock APK**, atau push ke branch `main`/`master`.
3. Setelah selesai, buka run workflow → **Artifacts** → unduh `GyroLock-debug-apk`.
4. Ekstrak ZIP artifact dan pasang `app-debug.apk` di Android.

## Catatan build
Workflow menggunakan Gradle 8.9 dan Java 17. Dependency FFmpegKit menggunakan maintained fork `dev.ffmpegkit-maintained:ffmpeg-kit-full:6.0.3`; jika repository dependency berubah, build log GitHub Actions akan menunjukkan error resolusi.


## Perubahan revisi V4 — gyro XYZ
- Video asli tetap dipertahankan dan stabilisasi dijalankan setelah menekan `STABILKAN VIDEO`.
- Data gyro disimpan dengan timestamp sensor absolut lalu disejajarkan ke awal rekaman.
- Ketiga sumbu diintegrasikan dan dihaluskan; FFmpeg memakai interpolasi waktu serta crop 1/1,12 untuk menyembunyikan tepi kosong.
- Workflow `.github/workflows/build.yml` tetap dipertahankan.
- Ini peningkatan algoritma awal dan perlu diuji pada perangkat; hasil tidak dijamin setara stabilisasi kamera profesional.
