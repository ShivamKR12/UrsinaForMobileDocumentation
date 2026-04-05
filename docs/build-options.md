# Advanced Build Options

While `project/settings.toml` handles the most common configuration settings for UrsinaForMobile, the underlying Panda3D distribution system is highly customizable. 

If you need advanced control over how your app is packaged, you can directly modify the `setup()` function in your `setup.py` file using the options below.

---

## Shrinking Build Size and Time (`android_abis`)

By default, Panda3D builds your game for four different Android architectures (`arm64-v8a`, `armeabi-v7a`, `x86_64`, `x86`). This ensures compatibility across all devices, but it significantly increases your compilation time and the final size of your Universal APK.

**For Local Testing:** 
If you are just testing locally on your own phone, you can shrink the build size and drastically speed up compilation by targeting only your specific device's CPU architecture (usually `arm64-v8a`). 

You can do this by adding the `android_abis` option to your `setup.py`:
```python
'android_abis': ['arm64-v8a'],
```

!!! info "Distribution on App Stores"
    When distributing your `.aab` file to the **Google Play Store** or **Steam**, you should leave `android_abis` to its default so it builds all architectures. Those platforms will automatically serve the correct, stripped-down APK matching the user's specific device, saving them download size. 
    
    However, for platforms like **itch.io**, you may need to provide separate download links for each architecture if you don't want users downloading a massive Universal APK.

---

## Full List of `build_apps` Options

These options define how the application is frozen and packaged into a runnable format.

### General Paths & Inclusions

* **`build_base`**: The directory to build the applications in (defaults to `build` in the current working directory).
* **`gui_apps`**: A dictionary of applications that can open a window, with executable names as keys and the path to the main script as the value.
* **`console_apps`**: A dictionary of apps that do not open a window.
* **`include_patterns`**: A list of patterns of files to include in the built applications (e.g., `**/*.png`).
* **`exclude_patterns`**: A list of patterns of files to NOT include (takes precedence over `include_patterns`).
* **`rename_paths`**: A dictionary to match a path and replace it with another during the build.

### Python Modules & Plugins

* **`include_modules`**: A dictionary forcing specific Python modules to freeze into the application. Very useful for "hidden" imports that the FreezeTool fails to detect automatically.
* **`exclude_modules`**: A dictionary of Python modules to explicitly exclude from the build.
* **`plugins`**: A list of dynamically loaded Panda3D plug-ins included with the build (e.g., `pandagles2`).
* **`requirements_path`**: Path to a `requirements.txt` file to use with PIP when fetching wheels.

### Optimization & Wheels

* **`use_optimized_wheels`**: (Defaults to `True`). Tries to download optimized Panda3D wheels that lack debug info and error checks. This reduces disk space and runs faster, making it ideal for production.
* **`optimized_wheel_index`**: An extra index URL to use to find the optimized wheels.
* **`strip_docstrings`**: (Defaults to `True`). Removes all Python docstrings as an optimization.
* **`prefer_discrete_gpu`**: On Windows systems with dual GPUs, this tells the driver that the application prefers the dedicated GPU for higher performance.

### Logging & Icons

* **`log_filename`**: If specified, routes all print statements and error messages to a file. You can use `$USER_APPDATA/` to target the user's AppData folder, and it supports date/time formatting.
* **`log_append`**: If `True`, preserves existing contents of the log file and appends to it, rather than erasing it every run.
* **`icons`**: A dictionary mapping app names to a list of images. Panda3D automatically generates the required icon resolutions from these. Use `"*"` as the key to apply it to all apps.

### Asset Handling

* **`file_handlers`**: A dictionary overriding the default functions run when encountering specific file extensions.
* **`bam_model_extensions`**: A list of model extensions (like `.egg`, `.gltf`, `.glb`) automatically converted to Panda3D's optimized `.bam` format during the build.

---

## Android-Specific Options

These options only apply when `platforms` includes `android`.

* **`application_id`**: The unique identifier for your app (e.g., `com.gamestudio.mygame`). *Caution: Once uploaded to the Google Play Store, this can never be changed!*
* **`android_version_code`**: An integer that must be incremented by 1 with every new update uploaded to the Play Console.
* **`android_app_category`**: How the app is categorized. Valid options include `game`, `audio`, `video`, `productivity`, etc.
* **`android_debuggable`**: A boolean indicating whether the app can be debugged. Recommended to be `True` during local testing.
* **`android_min_sdk_version`**: Overrides the lowest Android version supported. Defaults to 21. Increase this if your app relies on newer Android APIs.
* **`android_max_sdk_version`**: Overrides the highest Android version supported. Rarely needs to be set.
* **`android_target_sdk_version`**: The version of Android targeted. Must be at least 30 to upload to the Play Store.
* **`android_abis`**: A list of CPU architectures to target (e.g., `['arm64-v8a', 'armeabi-v7a', 'x86_64', 'x86']`). 
* **`android_manifest_file`**: Path to a custom `AndroidManifest.xml` to replace the auto-generated one. Use with caution, as improper settings here can easily break the app.

---

## Full List of `bdist_apps` Options

These options control the final packaging step (e.g., generating the `.aab` or installers).

* **`installers`**: Configurations for generating platform-specific installers.
* **`signing_certificate`**: Path to a `.pem` file used to sign the Android package.
* **`signing_private_key`**: Path to the `.pem` file containing the matching private key for the certificate.
* **`signing_passphrase`**: The password required to decrypt the private key (if it is encrypted). If omitted, the terminal will prompt you for it during the build.