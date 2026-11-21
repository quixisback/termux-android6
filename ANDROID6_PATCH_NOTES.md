# Android 6 (API 23) patch for termux-app 0.118.3

Changes (not compiled/tested here, patched by static analysis):
- gradle.properties: minSdkVersion 24 -> 23 (targetSdk stays 28)
- ExtraKeysView / ExtraKeyButton / SpecialButtonState / TerminalExtraKeys:
  removed java.util.stream, Iterable.forEach on lists, String.codePoints (API 24+) -> plain loops
- ViewUtils: isInMultiWindowMode() (API 24) guarded with SDK_INT >= 24

Everything else with newer APIs (notifications/channels, autofill, startForegroundService,
hidden-API bypass, java.nio.file.Files) was already guarded by SDK_INT checks.

Known risk: the downloaded bootstrap (2025.03.28-r1+apt-android-7) is built for Android 7+
(API 24). The app may start, but bootstrap binaries can fail on Android 6 with missing
libc/linker symbols. Fix = use a bootstrap built with a lower API level.

## Bootstrap (added)
- app/src/main/cpp/bootstrap-aarch64.zip = user-supplied legacy bootstrap (apt repo termux-main-21,
  prefix /data/data/com.termux/files/usr), sha256 3154a32a61699db1d34397cc1ec747e40f894312a4df6fad763a3f5c72ffc9c6
- app/build.gradle: aarch64 checksum updated; arm/i686/x86_64 downloads disabled;
  build restricted to arm64-v8a (abiFilters + splits). Only works on a 64-bit Android system.
  For a 32-bit system you need a bootstrap-arm.zip and must undo these ABI changes.
