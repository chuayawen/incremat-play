# IncreMat Play Flutter app

The senior-facing component of [IncreMat](../../README.md). It uses exercise sessions from the [Caregiver app](https://github.com/chuayawen/incremat) to support daily goals, a growing virtual pet, and a collection of companion creatures.

**[Project context and contribution](../../README.md#project-context-and-my-contribution)** · **[Full setup guide](../../SETUP.md)**

## Code tour

| Area | What to inspect |
| --- | --- |
| [Home screen](lib/features/home/home_screen.dart) | Exercise progress, live-session feedback, and processing newly logged sessions |
| [Game service](lib/services/game_service.dart) | Daily pet-experience and weekly egg awards, including repeat-award guards |
| [Consistency calculations](lib/services/consistency_service.dart) | Aggregate repetitions by day and count days that meet the daily goal |
| [Pet model](lib/models/pet.dart) and [pet service](lib/services/pet_service.dart) | Pet stages, stored state, and progression |
| [Senior service](lib/services/senior_service.dart) | Firestore streams, saved-session catch-up, and Duet lobbies |
| [Authentication](lib/services/auth_service.dart) | Join-code and NFC profile linking through anonymous Firebase authentication |
| [Accessibility settings](lib/providers/accessibility_provider.dart) | Persisted text scale, contrast, and theme preferences |

## Data flow

1. The caregiver app connects to the physical mat and writes live state and completed sessions to Firestore.
2. Play watches the senior profile, current live session, recent sessions, and pet experience events.
3. Newly logged sessions are processed against the senior's daily repetition goal and weekly consistency threshold.
4. An eligible daily goal completion can award experience to the active pet; an eligible consistency threshold can award an egg.
5. The app records award markers and a session-processing timestamp to support catch-up when it is reopened.

The public Play app uses **Firestore for live exercise data**, rather than a direct BLE connection. Its NFC integration supports profile login.

## App structure

```text
lib/
  core/         Theme and shared UI constants
  features/     Login, home, history, sanctuary, hatching, evolution, and Duet
  l10n/         English and Chinese strings
  models/       Senior, pet, live-session, and Duet data
  providers/    Authentication, senior state, locale, and accessibility
  services/     Firebase access, pet progression, and consistency calculations
assets/
  images/       App backgrounds
  pets/         Egg and pet-stage artwork
  animations/   Pet video assets
firestore.rules
test/
```

## Run locally

Use Flutter with Dart **3.12 or later within Dart 3**, an Android device or emulator, and a Firebase development project shared with the caregiver app. See the [setup guide](../../SETUP.md#run-the-flutter-app) for authentication and native Firebase configuration.

From this directory:

```bash
flutter pub get
flutter run
```

The caregiver workflow creates the senior profile and join code. Use a development profile for the first sign-in, then exercise the flow from the caregiver app or its mat simulator.

## Checks and current test coverage

```bash
flutter analyze
flutter test
```

The existing [`widget_test.dart`](test/widget_test.dart) is a placeholder assertion. A successful run of that file does not establish that the app's screens, reward logic, Firebase access rules, or Duet flows work end to end.

For a device check, follow one test senior through **profile linking → live repetitions → saved session → daily goal → pet progress**, then verify that reopening the app does not repeat the same award. Check text scaling, contrast, and language switching separately.
