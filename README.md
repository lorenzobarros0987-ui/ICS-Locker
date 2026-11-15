# PIN Locker
 
A custom lock screen for old Android phones, styled after the Holo / Android 4.0 (ICS) lock screen.
It replaces the discontinued *Holo Locker*, with a PIN, shortcuts and lots of appearance options.
 
Built with a lot of help from [Claude](https://claude.ai) (Anthropic).
 
## Features
 
- **Holo-style lock screen**: ring handle, slide-to-unlock, left/right shortcut targets, clock and date.
- **Slide effects**: idle ripples, finger trail, targets that grow as you get close, snap and vibration.
- **PIN screen**: Holo keypad on screen, or a **full page that uses your phone's keyboard**.
- **Optional PIN**: you can remove the PIN and use the lock screen with just a slide.
- **Strict lock**: the lock screen is a window drawn above everything at all times (status bar and navigation bar included). Only the power-off menu (long-press power) is allowed through, for a few seconds.
- **Shortcuts**: any app, or presets like the original Holo Locker (Phone, Messages, Camera, Browser, Gallery, Email, Contacts, Music, Calendar, Maps, Clock, Calculator). Each shortcut can have a custom photo.
- **Background**: your phone's wallpaper (default), gallery photo, or Holo gradients.
- **Appearance**: accent color, clock font (including the original Holo Locker font), size, position, 12/24h, date format, keypad options.
- **Languages**: English and Brazilian Portuguese. Automatic by system language, can be changed in the app.
## Requirements
 
- Android 7.0+ (minSdk 24). Developed and tested on an LG K11 (Android 7).
- Permissions (the app guides you through them):
  1. **Display over other apps**: needed for the always-on-top lock window.
  2. **Usage access**: to know which shortcut app is in front.
  3. **Device administrator**: makes it harder to uninstall without the PIN.
  4. **Read storage**: only to use the phone wallpaper as background.
## Setup
 
1. In Android **Settings > Lock screen**, choose **None** or **Swipe** (the system lock can't use a PIN together with this app).
2. Open PIN Locker, set a PIN (or skip it), and grant the permissions.
3. Tap **ENABLE lock**, then turn the screen off and on to test.
> **Safety:** if something goes wrong, reboot into **safe mode** (third-party apps are disabled) and uninstall PIN Locker.
 
## Build
 
Open the `PinLocker` folder in Android Studio and run, or from the terminal:
 
```bash
./gradlew assembleDebug
```
 
The APK is generated in `app/build/outputs/apk/debug/`.
 
## Project structure
 
| File | What it does |
|---|---|
| `LockService.java` | Foreground service: locks on screen off, draws the always-on-top window, watches shortcut apps |
| `LockScreen.java` | The whole lock screen UI (clock, rings, PIN keypad) |
| `LockView.java` | Rings, targets, drag logic and slide effects |
| `LockActivity.java` | Preview, full-page keyboard PIN, and fallback if overlay permission is missing |
| `MainActivity.java` | Main settings: PIN, shortcuts, permissions, language |
| `AppearanceActivity.java` | Background, colors, clock and keypad options |
| `Presets.java` | Preset shortcuts like the original Holo Locker |
| `L.java` | PT-BR / English strings |
 
## Notes and limitations
 
- The strongest "above everything" behavior works on Android 7 (the target device). On newer Android versions the status bar window sits above app overlays, so the app also covers it with a touch-blocking strip and keeps collapsing the notification shade.
- Using the phone wallpaper needs the storage read permission; without it the app falls back to a Holo gradient.
- This is a hobby project, not a security product. A determined person can still bypass it (for example with safe mode).
## License
 
Choose the license you prefer (for example MIT) and add a `LICENSE` file.
 
