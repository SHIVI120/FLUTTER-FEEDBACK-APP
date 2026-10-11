# Feedback Hub — Design and Implementation Notes

## Design choices

The interface uses Material 3, generous spacing, rounded surfaces, restrained indigo accents, clear step titles, and a single primary action per screen. This keeps the form focused and makes the workflow easy to understand on mobile devices. Small entrance transitions and progress feedback make navigation feel responsive without distracting from data entry.

## Architecture

The app is split into presentation, domain, and data concerns. Flutter widgets render state; BLoCs receive user events and emit immutable states; repository/service classes perform authentication, persistence, media handling, and export. `get_it` provides constructor dependencies so implementation details do not leak into widgets.

## Persistence

Drift/SQLite stores the feedback record and its media paths locally. This supports offline collection. The media files are copied into an app-private documents directory, while SQLite stores their paths and metadata. A feedback record is committed after the form is submitted.

## Authentication and export protection

Firebase Authentication with Google Sign-In identifies the device owner. CSV export is gated by `local_auth`, which can invoke biometrics or supported device credentials depending on OS configuration. The app does not store the user's Google password.

## Challenges and trade-offs

1. **Scoped storage:** Android's public Downloads/MediaStore API is platform-specific. The included portable version writes a CSV to app documents and opens the share sheet. A production Android build should add a MediaStore platform-channel implementation to save directly to public Downloads.
2. **Local media links:** CSV paths only resolve on the device that collected the data. If feedback needs to be reviewed elsewhere, upload media to authenticated cloud storage and export signed or access-controlled links.
3. **Offline security:** SQLite is not encrypted by default. Production deployments should evaluate encrypted SQLite, OS-backed key management, and a data-retention policy.
4. **Platform configuration:** Firebase, Google sign-in, camera/gallery access, and biometrics require native project configuration and real-device testing.

## Potential improvements

- Add Android MediaStore integration and iOS document export integration.
- Encrypt local database and sensitive fields.
- Add CSV preview, date filtering, search, and export audit logging.
- Add unit, widget, and integration tests plus GitHub Actions CI.
- Add accessibility labels, localization, and more robust validation.
- Offer optional authenticated cloud backup with explicit user consent.

## Run checklist

- [ ] `flutter pub get`
- [ ] Generate Drift code with build_runner
- [ ] Configure Firebase and enable Google provider
- [ ] Configure native OAuth IDs / SHA fingerprints
- [ ] Test Google sign-in on device
- [ ] Test image/video permissions
- [ ] Test biometric/device credential behavior
- [ ] Verify export content and storage location
