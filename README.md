# expo-modules-jsi SWIFT_RETURNS_RETAINED repro

Minimal reproduction for the build failure described in
[expo/expo#50067](https://github.com/expo/expo/issues/50067): `expo-modules-jsi`
57.1.0/57.1.1's `RuntimeScheduler.h` annotates its two constructors
`SWIFT_RETURNS_RETAINED`, which Xcode 26.2/26.3's clang reject.

## Reproducing

```bash
npm install
npx expo run:ios
```

Fails during the `[CP-User] Build ExpoModulesJSI xcframework` phase.

## Workaround that needs no patch

If you can, **build with Xcode 26.6 instead.** Verified (both here and independently
by others in the linked issue): the unpatched source compiles clean under 26.6 — the
annotation issue is only a warning there, and the Swift 6 concurrency errors that are
masked behind it on 26.2/26.3 never trigger. No source changes needed.

```bash
DEVELOPER_DIR=/Applications/Xcode_26.6.app/Contents/Developer npx expo run:ios
```

If you're stuck on an older Xcode, see #50067 for a patch-package workaround (wrapping
the affected JSI trampoline pointers in the package's own `NonisolatedUnsafeVar`).
