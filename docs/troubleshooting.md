# Troubleshooting

Building for Android can sometimes be tricky. If you run into issues during the build process or while running your game, check the common solutions below.

## Build Errors

### Protobuf Version Mismatch
If your build fails with errors related to `google.protobuf` or `builder` scripts crashing, you likely have the wrong version of the `protobuf` library installed. 

**Solution:** Ensure you are using exactly version `3.20.0`. Run this in your terminal:

**Windows**
```cmd
py -3.13 -m pip install protobuf==3.20.0
```
**Linux**
```bash
python3.13 -m pip install protobuf==3.20.0
```
**macOS**
```bash
python3.13 -m pip install protobuf==3.20.0
```

## Runtime Errors

### App Crashes Immediately on Startup
If you install your APK/AAB and it closes the moment you open it, this is usually a Python exception occurring during initialization.

**Solution 1:** Check `__main__.py`. Did you forget to call `setup_ursina_android()` at the very top of the file before importing Ursina?

**Solution 2:** Check the logs. Connect your device to your PC, open a terminal, and run:

**Windows**
```cmd
adb logcat "Panda3D:V" "Python:V" "threaded_app:V" "*:F"
```
**Linux**
```bash
adb logcat 'Panda3D:V' 'Python:V' 'threaded_app:V' '*:F'
```
**macOS**
```bash
adb logcat 'Panda3D:V' 'Python:V' 'threaded_app:V' '*:F'
```
Then, open the app on your phone. The terminal will print out the exact Python traceback (e.g., a missing module or a syntax error) causing the crash.

### ADB "Device Not Found"
If `adb logcat` or bundletool cannot find your device:
- Make sure **Developer Options** and **USB Debugging** are enabled on your Android device.
- Ensure your Android Platform Tools are correctly added to your system's `PATH`.
- If the ADB server is stuck or malfunctioning, you can restart it by running:
  ```bash
  adb kill-server
  adb start-server
  adb devices
  ```