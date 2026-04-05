# Your Game

The previous chapters guided you through creating an UfM repository, building, and installing the produced application.

This chapter is about making your own game! Here, you'll learn how to include assets, modify the code, handle logging, and explore other advanced features.

Everything in this chapter is highly recommended to read, as developing for Android has a few minor differences from standard desktop development.

## Code Structuring

Your game's entry point must be `src/__main__.py` (or simply `src/main.py`). You can split your game logic across multiple Python files, but **all your game scripts must always only be inside the `src/` folder**. You can import them into your main script just like a normal Python project.

!!! warning "The Golden Rule of UfM"
    You **must** import and call `setup_ursina_android()` at the very top of `__main__.py`, **before** you import `ursina` or any other modules that rely on file paths.
    ```python
    from setup_ursina_android import setup_ursina_android
    setup_ursina_android()
    
    from ursina import *
    # ... rest of your game
    ```

## Asset Management

Thanks to the `setup_ursina_android` script, **you do not need to change how you load assets!** 

Simply drop your textures, models, and sounds into the `assets/game_assets/` folder. In your code, you can load them normally (e.g., `Entity(model='cube', texture='my_texture')`). The setup script ensures Ursina's internal pathing system automatically redirects to the correct location on the Android device.

## Logging and Debugging

When your game is running on an Android device, you won't have a standard terminal window to see your `print()` statements or Python errors.

Instead, everything is output to the Android system log. You can use standard Python `print()` statements in your code. To view these logs on your computer:

1. Connect your Android device via USB (with USB Debugging enabled).
2. Open your terminal and use ADB to filter for Python logs:

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

### Advanced ADB Logging
Here are some extremely useful ADB commands for debugging your Ursina game efficiently:

**Clear old logs before running your game:**
```bash
adb logcat -c
```

**Save the log output to a text file for easier reading:**
```bash
adb logcat -v threadtime "Panda3D:V" "Python:V" "*:F" > panda_log.txt
```

**Filter logs strictly by your Application ID (e.g., `com.your.app`):**
* **Windows**: `adb logcat | findstr /i "com.your.app"`
* **Linux/macOS**: `adb logcat | grep -i "com.your.app"`

**Viewing logs from an Emulator (e.g., BlueStacks):**
If you are testing on an emulator, use the `-s` flag to target its specific IP and port (BlueStacks is usually `127.0.0.1:5555`):
```bash
adb -s 127.0.0.1:5555 logcat "Panda3D:V" "Python:V" "*:S"
```

## Android-Specific Considerations

Developing for Android with Ursina introduces some unique quirks and limitations you need to keep in mind.

### Graphics and Rendering
Because standard OpenGL (`pandagl`) is not available on Android, UfM automatically switches Panda3D to use the `pandagles2` (or `pandagles`) renderer. 
The boilerplate handles this for you automatically, but keep this in mind if your game relies on writing custom shaders, as they must be compatible with GLSL ES.

### Input and Touch
* **Single Touch Only**: There is no multi-touch support for Ursina on Android. It only registers single touches.
* **Touch behaves like Mouse**: A single touch on Android maps directly to a **Left Mouse Button** click on PC. There is no middle mouse button or right-click.
* **The Hover Quirk**: Android does not have a concept of a "mouse hover". Because of this, if an action is wired strictly to a left mouse button click, you will often have to tap the screen **two times** to trigger it. The first tap registers as the "mouse hover", and the second tap registers as the actual "click".
    * *Workaround*: To solve this issue and make it work with a single tap on Android, you can wire your UI/game actions to trigger on the mouse hover event instead! This might feel strange when testing on PC, but it is a highly recommended practice for seamless mobile gameplay.

### Window Properties
* **Fullscreen Crashes**: There is no such thing as toggling fullscreen on Android. If your code includes `window.fullscreen = True`, it will **100% crash the app** on Android upon opening! The Ursina window is *always* fully maximized on mobile natively, regardless of PC resolution settings.
* **Titles and Icons**: Setting properties like `window.title` or `window.icon` in your Ursina script has no effect on Android. However, unlike the fullscreen setting, leaving these in your code won't cause any harm or crashes.