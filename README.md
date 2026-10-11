# Feedback Hub — Flutter User Feedback Collection

An offline-first Flutter starter project for collecting structured feedback, attaching screenshots/videos, and exporting records to CSV after device-owner authentication.

## Features

- Google Sign-In through Firebase Authentication
- BLoC-managed authentication, feedback form, and CSV export flows
- Local SQLite persistence through Drift
- Dependency injection through `get_it`
- User details, issue description, media attachments, and thank-you flow
- Camera/gallery and file picker media selection
- CSV export with owner, user, issue, device, and media paths
- `local_auth` gate before export
- Material 3 UI with subtle transitions and animated feedback

## Requirements

- Flutter stable (Dart 3.4+)
- Android Studio / Xcode as applicable
- A Firebase project with Authentication → Google enabled
- Android application SHA-1/SHA-256 configured in Firebase for Google sign-in
- `flutterfire configure` completed for the target platforms

## Setup

1. Install Flutter and verify with `flutter doctor`.
2. Create/configure a Firebase project and enable Google as a sign-in provider.
3. From this folder run:

   ```bash
   flutter pub get
   dart run build_runner build --delete-conflicting-outputs
   flutterfire configure
   flutter run
   ```

4. Run `flutterfire configure` and then update `lib/main.dart`: import `firebase_options.dart` and initialize with `await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);`. This ensures Firebase uses the generated platform configuration.
5. Configure Android/iOS native settings for Firebase, Google Sign-In, biometrics, and media permissions as required by current platform documentation.

## Storage notes

- Feedback metadata is stored locally in SQLite.
- Media files are copied into the app's documents directory in a `feedback_media` folder. This is reliable and private across Android/iOS.
- CSV is written into the app documents directory as a portable fallback, then offered through the share sheet. Public Downloads/MediaStore publishing is platform-specific; implement and test an Android MediaStore bridge before claiming the file is in public Downloads.
- Media links in CSV are local file paths. They are useful on the collecting device and are not cloud URLs.

## Security notes

- Google authentication establishes the owner identity.
- Biometric/device-credential authentication is required before exporting.
- A local database is not encrypted by default. For production use, consider SQLCipher/encrypted storage, Android Keystore/iOS Keychain key management, a retention policy, and secure deletion.
- This app is a starter implementation. Review Firebase setup, platform permissions, accessibility, error handling, and device-specific behavior before production release.

## Project structure

```text
lib/
  core/          theme, dependency injection
  data/          Drift database, repositories, services
  domain/        feedback model
  features/
    auth/        authentication BLoC and login
    feedback/    collection BLoC and form screens
    export/      biometric-gated CSV export
  main.dart
```

## Tests

Run `flutter test`. Add integration tests for Google Sign-In, media permissions, biometric authentication, and scoped storage on real devices.

## Known limitations / future improvements

- Public Downloads/MediaStore writing needs a native Android implementation and device testing.
- Local media paths are not accessible from other devices; upload to secured cloud storage if cross-device access is required.
- Add automated tests, encrypted SQLite, form validation tests, accessibility audits, and CI.
