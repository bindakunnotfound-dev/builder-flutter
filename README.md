# Excellent Mirror — Remote Pterodactyl build

USB/ADB transport has been removed. The Flutter/Android build workflow itself is unchanged, including `flutter build apk --release --split-per-abi`.

The APK:
1. asks for Android screen-capture permission;
2. fetches `server.json` from the specified GitHub repository;
3. connects directly to the configured host over WebSocket;
4. sends the existing H.264 packets to the Node.js server;
5. if the connection times out/disconnects, refreshes `server.json` and retries.

The `server/` folder is a Node.js service intended to run in a Pterodactyl Node.js server. It also includes a browser WebCodecs viewer so the old Chromebook-local viewer is no longer required.

The current GitHub configuration is:
https://raw.githubusercontent.com/vinszzr-lang/Project-Reskin-Gue/main/server.json


## Build APK (split per ABI)

GitHub Actions workflow: `.github/workflows/build.yml`. It builds separate APKs using `flutter build apk --release --split-per-abi`. The Android app module pins NDK `28.2.13676358`. Keep `MainActivity.java` and `MirrorService.java` together under `android/app/src/main/java/com/nullx/evo/`; `MainActivity` references `MirrorService`.


## GitHub configuration inside APK

Configuration asset: `assets/github_config.json` (bundled as `assets/flutter_assets/assets/github_config.json`).

```json
{
  "github_owner": "vinszzr-lang",
  "github_repo": "Project-Reskin-Gue",
  "github_branch": "main",
  "config_path": "server.json",
  "github_token": ""
}
```

For a public repository, keep `github_token` empty. To change settings after building, edit the JSON inside the APK with an APK editor, rebuild/save and sign the APK again. A token embedded in an APK is extractable; prefer a public config file and no token. If a private repo is used, the optional token is read as a GitHub Contents API credential, but embedding it is not secure. The `server.json` file itself should contain `{ "server": "wss://your-host.example:PORT" }` (or the actual HTTP(S)/WS(S) endpoint supported by the server).
