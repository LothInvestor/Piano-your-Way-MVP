# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Piano your Way is a mobile solfège-training app built with React Native + Expo. The MVP is **visual note identification only**: the app shows a note (on a staff, as a piano key highlight, or both) and the user picks the correct solfège name (Do, Ré, Mi …). No audio / ear training in the MVP.

## Commands

```bash
# Start dev server
npx expo start

# Run on Android emulator / device
npx expo run:android

# Run on iOS simulator / device
npx expo run:ios

# Run tests
npx jest

# Run a single test file
npx jest path/to/file.test.ts

# Type-check without building
npx tsc --noEmit

# Lint
npx expo lint          # if eslint config present
```

## Architecture

### Stack
- **Expo SDK** (managed workflow) — keep managed until a native module forces a bare ejection
- **React Navigation** — stack + bottom-tab navigator
- **Zustand** — global state (user progress, settings, current session)
- **expo-av** (future) — reserved for audio; not needed in MVP

### Screen flow
```
Onboarding → Home → Exercise session → Result summary
```

### Core domain concepts
- **Note** — a pitch represented as a MIDI number (C4 = 60) plus an octave-independent pitch class (0–11)
- **Clef** — treble or bass; determines where notes sit on the staff
- **SolfegeLabel** — the user-visible name for a pitch class (Do/Ré/Mi … in French, or Do/Re/Mi … depending on locale setting)
- **ExerciseSession** — a timed or count-limited sequence of questions; tracks score and mistakes

### File layout conventions (to be created)
```
src/
  components/    # Dumb UI components (Staff, NoteHighlight, AnswerGrid)
  screens/       # One file per screen
  domain/        # Pure logic: note conversion, question generation, scoring
  store/         # Zustand slices
  hooks/         # Custom hooks wrapping domain + store
  utils/         # Generic helpers
```

### Key invariants
- All note logic lives in `src/domain/` and is framework-free (easy to unit-test)
- Staff rendering uses SVG (via `react-native-svg`); never canvas or images for note positions
- Solfège labels are looked up through a locale-aware mapping, not hardcoded strings

## Expo-specific notes
- Use `expo-constants` for env config rather than bare `process.env`
- `app.json` controls bundle identifiers; keep `android.package` and `ios.bundleIdentifier` consistent
- Avoid `expo eject` unless a native module absolutely requires it
