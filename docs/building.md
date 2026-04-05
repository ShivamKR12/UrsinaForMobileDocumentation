# Building for Android

Under the hood, UfM uses Panda3D 1.11's deployment tools to package your Python code, Ursina assets, and game assets into a native Android application.

For a comprehensive, deep-dive into how Panda3D handles app deployment, please refer to the official [Panda3D 1.11 Distribution Documentation](https://docs.panda3d.org/1.11/python/distribution/).

## Running the Build Command

Once you have configured your `project/settings.toml` and placed your code in `src/__main__.py`, you are ready to build.

Open your terminal in the root of your UfM directory and run:

**Windows**
```cmd
py -3.13 setup.py bdist_apps
```
**Linux**
```bash
python3.13 setup.py bdist_apps
```
**macOS**
```bash
python3.13 setup.py bdist_apps
```

### What happens during the build?

1. **Asset Collection**: The builder reads your `settings.toml` and gathers all files listed under `includes` (such as `.png`, `.egg`, and your scripts).
2. **Compilation**: Panda3D compiles your Python code and packages it alongside a pre-compiled Python interpreter for Android.
3. **APK/AAB Generation**: The final output is generated in the `build/` directory.

If the build is successful, you will find an installable `.apk` or publishable `.aab` file ready to be transferred to your Android device!

---

## Local Testing (APKs and Emulators)

While the `.aab` is required for the Play Store, you cannot install it directly onto your device. You must first convert it into a Universal APK using **Bundletool**.

### 1. Generating the Universal APK
Run the following command to generate an `.apks` archive containing a universal APK:

**Windows**
```cmd
java -jar path\to\bundletool.jar build-apks --bundle build\your_app_name.aab --output build\your_app_name.apks --mode universal
```
**Linux / macOS**
```bash
java -jar path/to/bundletool.jar build-apks --bundle build/your_app_name.aab --output build/your_app_name.apks --mode universal
```
*(Replace `path/to/bundletool.jar` and `your_app_name` with your actual file names/paths).*

### 2. Extracting the APK
An `.apks` file is just a ZIP archive. You can rename and extract it via your command line:

**Windows (PowerShell)**
```powershell
Rename-Item -Path "build\your_app_name.apks" -NewName "your_app_name.zip"
Expand-Archive -LiteralPath "build\your_app_name.zip" -DestinationPath "build\your_app_name_extracted" -Force
```
**Linux / macOS**
```bash
mv build/your_app_name.apks build/your_app_name.zip
unzip build/your_app_name.zip -d build/your_app_name_extracted
```
Inside the extracted folder, you will find `universal.apk`. This is your installable game!

### 3. Installing the Game
**On a Physical Device:**
Connect your device via USB (with USB Debugging enabled) and run:
```bash
adb install build/your_app_name_extracted/universal.apk
```

**On an Emulator (e.g., BlueStacks):**
You can also test your game entirely on your PC using an Android emulator!
1. Open your emulator (e.g., BlueStacks, which defaults to port `5555`).
2. Connect ADB to the emulator: `adb connect 127.0.0.1:5555`
3. Install the APK directly to the emulator: 
   ```bash
   adb -s 127.0.0.1:5555 install build/your_app_name_extracted/universal.apk
   ```

---

## Signing the Bundle (Production)

Before you can upload the app to the Play Store, it needs to be signed using an upload certificate. You can generate a certificate yourself using OpenSSL:

**Windows**
```cmd
openssl genpkey -algorithm RSA -aes256 -out private.pem
openssl req -new -x509 -sha256 -days 365 -key private.pem > cert.pem
```
**Linux**
```bash
openssl genpkey -algorithm RSA -aes256 -out private.pem
openssl req -new -x509 -sha256 -days 365 -key private.pem > cert.pem
```
**macOS**
```bash
openssl genpkey -algorithm RSA -aes256 -out private.pem
openssl req -new -x509 -sha256 -days 365 -key private.pem > cert.pem
```

Then, you can modify `setup.py` to use these keys:

```python
setup(
    # ...
    options={
        'bdist_apps': {
            'signing_certificate': 'cert.pem',
            'signing_private_key': 'private.pem',
        },
    },
)
```

!!! tip "Existing Android Studio Keystore"
    If you already have an upload key generated using `keytool` or Android Studio, you can convert it to a PEM certificate using this command:
    
    **Windows**
    ```cmd
    keytool -export -rfc -keystore path\to\keystore.jks -alias my-key -file cert.pem
    ```
    **Linux**
    ```bash
    keytool -export -rfc -keystore path/to/keystore.jks -alias my-key -file cert.pem
    ```
    **macOS**
    ```bash
    keytool -export -rfc -keystore path/to/keystore.jks -alias my-key -file cert.pem
    ```

---

## Submitting to the Play Store

Once you have tested the application and signed the `.aab` file with an upload key, you can upload it to the Google Play Store.

1. Create a developer account on the Google Play Console. Note that there is a one-time registration fee of $25 USD.
2. Click **Create App** in the upper-right corner and register your game.
3. It is recommended to first create an **Internal testing release** to test the app before finishing the store listing.
4. Use **Create release**, fill in the details, and upload the signed `.aab` file.

!!! warning "Version Code Updates"
    If you want to upload a new version, you **must** change the `vercode` in `project/settings.toml`. If you do not increment this integer, the Play Store will reject the upload!