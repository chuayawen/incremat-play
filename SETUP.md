# IncreMat Play setup

This repository contains a Flutter app and an ESP32 starter project. They use different working directories and build tools.

| Task | Working directory | Tool |
| --- | --- | --- |
| Run the senior-facing app | `src/incremat_play` | Flutter |
| Build the ESP32 starter | Repository root | PlatformIO |

## Run the Flutter app

### Requirements

- Flutter with Dart **3.12 or later within Dart 3**, as required by [`pubspec.yaml`](src/incremat_play/pubspec.yaml).
- Android SDK tools and an Android device or emulator.
- A Firebase development project you control, shared with the [Caregiver app](https://github.com/chuayawen/incremat).

### Get the source

```bash
git clone https://github.com/chuayawen/incremat-play.git
cd incremat-play/src/incremat_play
flutter pub get
flutter doctor
```

Run Flutter commands from `src/incremat_play`, where the app's `pubspec.yaml` lives.

### Configure Firebase

1. Follow the [official Flutter Firebase setup guide](https://firebase.google.com/docs/flutter/setup) to register the Android app in your development project and configure its native Firebase integration. The current entry point uses `Firebase.initializeApp()` without passing generated Dart options, so Android's matching `google-services.json` and Gradle integration must be configured.
2. Enable **Anonymous Authentication** for Play. If setting up the caregiver app in the same project, enable **Email/Password Authentication** for it as well.
3. Set up Firestore and review the included [`firestore.rules`](src/incremat_play/firestore.rules) and [`firestore.indexes.json`](src/incremat_play/firestore.indexes.json) against your development project's data model.
4. Point the caregiver app at this same Firebase project. Use its workflow to create a test senior profile and generate a join code.
5. Use the development join code to link Play to that profile. NFC login additionally requires a supported device and a registered test tag.

Bundled Firebase configuration belongs to the original prototype project. A public configuration file identifies a project; it does not provide permission to use its accounts or data.

### Start the app

```bash
flutter run
```

Use the caregiver app's live session or **Settings → Developer · Simulator** to supply exercise data for the same test senior. The public Play app reads live exercise state through Firestore. It does not contain a direct BLE mat driver.

### Run checks

```bash
flutter analyze
flutter test
```

The current Play widget test is a placeholder. Check the account-linking, live-session, reward, and accessibility flows on an Android device separately; see the [app README](src/incremat_play/README.md#checks-and-current-test-coverage).

## Build the ESP32 starter

The root [`src/main.cpp`](src/main.cpp) contains the original board diagnostics and clock/display/connectivity examples. It is separate from the Flutter app and is not the complete IncreMat exercise-mat firmware.

Install PlatformIO Core or the PlatformIO IDE extension, then run from the repository root:

```bash
pio run --environment seeed-xiao-esp32-s3-plus
```

The environment is defined in [`platformio.ini`](platformio.ini). Building compiles the starter; it does not flash a connected device.

If you intend to use that starter on a compatible board, its upload and serial-monitor commands are:

```bash
pio run --environment seeed-xiao-esp32-s3-plus --target upload
pio device monitor
```

See the [PlatformIO command reference](https://docs.platformio.org/en/latest/core/userguide/cmd_run.html) for environment, target, and port options.

## Common setup issues

- **Flutter cannot find a project:** change into `src/incremat_play` before running Flutter commands.
- **Firebase fails during startup:** check that the Android package registration, `google-services.json`, and Gradle Firebase integration match your development project.
- **A join code is rejected:** verify the code belongs to an active test senior in the same backend used by both apps, and that Anonymous Authentication is enabled.
- **The Play count does not update:** confirm the caregiver app is writing live state for that senior. Play reads Firestore rather than scanning for the mat.
- **NFC login is unavailable:** use the join-code route on an emulator or device without NFC support.

## Template attribution

The ESP32 starter was supplied through [Sustainable Living Lab's UTC_2738_STEER](https://github.com/SustainableLivingLab/UTC_2738_STEER). Its original [MIT license](LICENSE) and copyright notice remain in place.
