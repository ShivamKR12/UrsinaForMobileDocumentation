# Prerequisites

Before you start porting your Ursina game to Android, you must configure your development environment with very specific versions of Python and required libraries. 

!!! warning "Strict Requirements"
    Failure to use the exact versions listed below will result in build errors or a crashing application on Android.

## 1. Python 3.13
The underlying Panda3D Android build system for this project currently relies on **Python 3.13**. 

Ensure you download and install Python 3.13 from the [official Python website](https://www.python.org/downloads/). 
*Tip: When running commands, it is highly recommended to use `python3.13` (or `py -3.13` on Windows) instead of just `python` to ensure you don't accidentally use a different installed version.*

## 2. Java Development Kit (JDK)
To build Android applications (APKs and AABs), your system needs Java.

Ensure you have a Java Development Kit (JDK) installed on your system, and make sure your `JAVA_HOME` environment variable is correctly set.

## 3. Python Dependencies

You must install exactly version `3.20.0` of the `protobuf` package. Newer versions will cause compatibility issues.

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

## 4. Android Platform Tools (ADB)

If you want to debug your application from your host PC using `adb` (Android Debug Bridge), you will need the Android Platform Tools. 
You can download the latest version from here: 

* **Windows**: https://dl.google.com/android/repository/platform-tools-latest-windows.zip
* **MacOS**: https://dl.google.com/android/repository/platform-tools-latest-darwin.zip
* **Windows**: https://dl.google.com/android/repository/platform-tools-latest-linux.zip

## 5. Bundletool

To manage and test Android App Bundles (`.aab` files), you also need to download `bundletool`.
You can get the latest release from the official Bundletool GitHub repository.

* **Bundletool**: https://github.com/google/bundletool/releases