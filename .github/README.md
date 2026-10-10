# Flutter Cloud Builder


### Workflow fix
`/.github/workflows/build.yml` runs on pushes to `main`. It accepts a ZIP in `build-inputs/` when present; if the app uploads Flutter source files directly into the repository, it detects `pubspec.yaml` and `lib/main.dart` from the checked-out repository instead.
