# Flutter Cloud Builder (build.yml)

Aplikasi Flutter Android untuk mengunggah ZIP proyek ke GitHub, memicu GitHub Actions, memantau run, dan mengambil APK/artifact diagnostik.

## Workflow

Workflow canonical: `.github/workflows/build.yml` (folder `workflows` wajib plural).

Pemicu:
- `workflow_dispatch`: dipanggil aplikasi dengan `source_path` dan `build_id`.
- `push` ke branch `main` jika ZIP baru masuk ke `build-inputs/*.zip`; workflow akan memilih ZIP terbaru jika input tidak diberikan.

## Konfigurasi

Edit `assets/github_config.json` sebelum build APK untuk owner/repository/branch. Token tidak diisi di repository. APK membaca token dari aset JSON saat runtime, jadi jika mengedit APK melalui MT Manager setelah build, sign ulang APK sebelum instalasi. Token di APK dapat diekstrak; gunakan token berumur pendek dengan izin repository minimum.

Token untuk API perlu izin Contents: Read and write serta Actions: Read and write (dan Workflows: write jika mengubah file workflow melalui API).

## ZIP input

Workflow mencari `pubspec.yaml` dan `lib/main.dart` otomatis di root atau folder bertingkat. ZIP harus berisi source Flutter, bukan APK saja.

## Build

`flutter pub get` lalu `flutter build apk --release --split-per-abi`.
