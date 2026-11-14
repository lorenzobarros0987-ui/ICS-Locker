# PIN Locker
 
A lock screen replacement for Android with a classic **Holo / Ice Cream Sandwich (4.0)** look, a PIN pad, customizable shortcuts and a long list of appearance options.
 
It started with a simple problem: the original *Holo Locker* app was discontinued years ago, its premium features can no longer be bought, and the old APK does not run well on modern Android. Instead of patching or cracking the old app, this project is a **new, from-scratch implementation** that reuses the classic look and adds the features that were missing.
 
> **Status: experimental.** This is a hobby project, built mainly with an LG K11 (Android 7.0) in mind and also targeting newer Android versions. It has not been through extensive testing across devices. Read the [limitations](#limitations-and-security-notes) before relying on it.
 
---
 
## Features
 
### Lock screen
- Classic ICS-style unlock ring: drag the ring to the lock to unlock, or to a side target to open a shortcut.
- Two customizable shortcuts (left and right). Pick any installed app; the app's icon is shown on the target automatically.
- If you leave a shortcut app without entering the PIN (Home, switching apps, etc.), you are sent back to the lock screen.
- Clock and date with the original Android clock font.
### PIN pad
- PIN of 4 to 8 digits, stored only as a salted SHA-256 hash on the device.
- Pad built with the system **Holo** theme (`Theme.Holo`, `borderlessButtonStyle`), including the Holo underline on the entry field.
- Digits with letters underneath (ABC, DEF, ...), OK and Delete keys, shake animation on a wrong PIN.
### Lockdown behavior (best effort, no root)
- Navigation bar hidden with immersive mode while locked.
- Back button disabled on the lock screen.
- A foreground service watches which app is on top while the device is locked and brings the lock screen back if anything else shows up.
- An invisible overlay over the status bar to make pulling down the notification shade harder.
- Locks automatically when the screen turns off and after a reboot.
- Optional device-admin registration, which makes uninstalling the app harder without first deactivating it.
### Appearance options
- **Background:** 7 built-in gradients/solid colors, or a photo from your gallery with adjustable dimming.
- **Accent color:** Holo blue, green, purple, orange or red, optionally used to tint the rings.
- **Clock:** font (original Android clock font plus 8 system fonts), color, size (48 to 120 sp), position, 12h/24h/system format.
- **Date:** on/off and 4 formats.
- **PIN pad:** show/hide letters, shuffle keys on every attempt, haptic feedback, show typed digits.
- **Preview** button to see the lock screen without actually locking the phone, and **Restore defaults**.
---
 
## Requirements
 
- Android 7.0 (API 24) or newer.
- Permissions the app will ask for:
  - **Display over other apps** - used to cover the status bar and bring the lock screen to the front.
  - **Usage access** - used to detect which app is in the foreground while locked.
  - **Device administrator** *(optional)* - makes uninstalling harder.
  - **Notifications** (Android 13+) - needed for the foreground service notification.
---
 
## Building
 
The project is a standard Gradle/Android Studio project (Java, `compileSdk 33`, `minSdk 24`, `targetSdk 33`, AGP 8.1.4, Gradle 8.0).
 
### Option 1: Android Studio
1. Open the project folder (the one containing `settings.gradle`).
2. Let Gradle sync.
3. **Build > Build Bundle(s) / APK(s) > Build APK(s)**.
4. The APK is at `app/build/outputs/apk/debug/app-debug.apk`.
### Option 2: GitHub Actions
Add `.github/workflows/build.yml`:
 
```yaml
name: Build APK
on: [push, workflow_dispatch]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: 8.0
      - run: gradle assembleDebug
      - uses: actions/upload-artifact@v4
        with:
          name: PinLocker-apk
          path: app/build/outputs/apk/debug/app-debug.apk
```
 
Push, wait a few minutes, and download the APK from the **Actions** tab.
 
---
 
## First-time setup on the device
 
1. Install the APK (you may need to allow installs from unknown sources).
2. In the system settings, set the screen lock to **None** or **Swipe**. The system lock cannot be combined with this one.
3. Open PIN Locker and **set a PIN**.
4. Grant **Display over other apps** and **Usage access**. Optionally enable the device administrator.
5. Choose your shortcuts and tweak **Appearance**. Use **Preview** to check the result.
6. Tap **ACTIVATE**, then turn the screen off and on to test.
> **Tip:** test first *without* enabling the device administrator. While it is active, a plain `adb uninstall` fails.
 
---
 
## Recovery if something goes wrong
 
Third-party apps do not run in Android's **Safe Mode**, so that is the escape hatch:
 
1. Reboot into Safe Mode (usually by long-pressing "Power off" and confirming, or via the hardware key combination for your device).
2. If device administrator was enabled, deactivate it in the system settings.
3. Uninstall PIN Locker.
---
 
## Limitations and security notes
 
Please be honest with yourself about what this app is:
 
- **It is not a secure lock.** Without root or device-owner privileges, an Android app cannot truly block the Home or Recents buttons. This project brings the lock screen back almost instantly, so the launcher may briefly flash. Anyone with physical access, ADB access or Safe Mode knowledge can bypass it.
- The system lock must be set to *None* or *Swipe*, which means the system itself will not protect your device if this app is stopped, crashes or is removed.
- Detecting the foreground app relies on *Usage access*. Without it, the app cannot watch other apps and the lockdown is much weaker.
- Behavior varies between manufacturers and Android versions, especially for overlays, background activity launches and foreground services. Newer Android versions are stricter and may break parts of it.
- Use it as a customizable, nostalgic lock screen, not as a replacement for the system lock on a device with sensitive data.
---
 
## Project structure
 
```
app/src/main/java/com/meu/pinlocker/
  MainActivity.java        Settings: PIN, shortcuts, permissions, activate/deactivate
  AppearanceActivity.java  All appearance options and the preview button
  LockActivity.java        The lock screen and the PIN pad
  LockView.java            The unlock ring and targets (drag logic and drawing)
  LockService.java         Foreground service: locks on screen-off and watches the foreground app
  BootReceiver.java        Re-locks after a reboot
  AdminReceiver.java       Device-admin receiver
  Prefs.java / Look.java / Fonts.java   Storage for PIN, shortcuts and appearance settings
app/src/main/assets/AndroidClock.ttf     Classic Android clock font
app/src/main/res/drawable-*/             Lock screen ring and target images
```
 
---
 
## Credits and acknowledgements
 
- **Built with a lot of help from Claude (Anthropic).** The idea, the requirements, the testing on a real device and all of the decisions came from me, but Claude wrote most of the code, helped me plan the architecture, explained how the Holo theme is still bundled in the system, and iterated with me on every feature in this README. Thank you, Claude, for the patience and the many rounds of changes.
- The lock ring and target images and the clock font are the classic Android lock screen resources that were bundled with the discontinued *Holo Locker* app. As far as I know they originate from the **Android Open Source Project** (Apache License 2.0), but I am not a lawyer and cannot guarantee this for every file. If you are a rights holder and want something removed, please open an issue.
- Inspired by the original *Holo Locker* and by the look of Android 4.0 "Ice Cream Sandwich". This project is **not affiliated with, endorsed by, or derived from the source code of** that app or its developers. No premium features, licenses or protections of the original app were bypassed; nothing from its code is used.
---
 
## Disclaimer
 
This software is provided "as is", without warranty of any kind. It modifies how your device's lock screen behaves; you use it at your own risk. Make sure you know how to boot into Safe Mode on your device before activating it.
 
---
 
## License
 
Add your license of choice here (for example MIT for the source code). The images and font mentioned in the credits keep their original licenses.
 
