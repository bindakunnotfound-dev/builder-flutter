# Flutter Cloud Builder (build.yml)

Aplikasi Flutter Android untuk mengirim ZIP proyek Flutter ke repository GitHub, menjalankan GitHub Actions melalui workflow `.github/workflows/build.yml`, memantau build, lalu mengunduh APK atau ZIP diagnostik.

## Konfigurasi

File `assets/github_config.json` sudah diset ke repository yang diberikan:

```json
{
  "github_owner": "bindakunnotfound-dev",
  "github_repo": "builder-flutter",
  "github_branch": "main",
  "github_token": ""
}
```

Build APK Cloud Builder dengan token tetap kosong. Setelah APK selesai dibangun, jika tetap memilih metode MT Manager, edit `assets/flutter_assets/assets/github_config.json` di dalam APK, isi token, simpan, lalu sign ulang APK. Token di dalam APK dapat diekstrak; jangan bagikan APK tersebut dan gunakan token dengan izin minimum. Cabut token jika pernah terunggah ke repository atau terbagikan.

Fine-grained PAT untuk cara ini perlu akses ke repository `builder-flutter`: **Contents: Read and write** serta **Actions: Read and write**. Workflow di repository perlu diizinkan pada tab Actions.

## Cara kerja

1. Pilih ZIP source Flutter.
2. Aplikasi menyimpan ZIP sementara ke `build-inputs/` melalui GitHub Contents API (bukan `git push` lokal).
3. Jika `.github/workflows/build.yml` belum ada, aplikasi memasangnya melalui Contents API.
4. Aplikasi memanggil `workflow_dispatch` pada `build.yml`, lalu memantau run.
5. Workflow mencari otomatis folder yang memiliki `pubspec.yaml` dan `lib/main.dart`, baik di root ZIP maupun di dalam folder bertingkat.
6. Workflow menjalankan `flutter pub get` dan `flutter build apk --release --split-per-abi`.
7. APK diunggah sebagai artifact `flutter-release-apks`. Jika gagal, workflow mencoba mengunggah `flutter-build-diagnostics`.

## ZIP yang didukung

```text
project.zip
├── pubspec.yaml
├── lib/main.dart
└── android/...
```

atau:

```text
project.zip
└── nama-folder/
    ├── pubspec.yaml
    ├── lib/main.dart
    └── android/...
```

atau proyek di folder yang lebih dalam. ZIP harus berisi source Flutter, bukan hanya APK.

## Build aplikasi Cloud Builder

Jalankan `flutter pub get`, lalu `flutter build apk --release`. Build workflow ini sendiri berlangsung di GitHub Actions. ZIP ini adalah source project, bukan APK yang sudah dikompilasi dan diverifikasi di perangkat.
