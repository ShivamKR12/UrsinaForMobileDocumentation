# Bundletool Guide

When you run `python setup.py bdist_apps`, Panda3D packages your Ursina game into an Android App Bundle (`.aab`). While this is the exact format required for publishing to the Google Play Store, **you cannot install an `.aab` directly onto your phone for testing.**

That is where `bundletool` comes in. It is a command-line utility that converts your game's `.aab` into installable APKs, allowing you to test your game locally just as if it were downloaded from the Play Store.

---

## 1. Generating APKs from your Game Bundle

When bundletool processes your `.aab`, it wraps the resulting APKs into a single container archive with an `.apks` extension.

To generate an APK set for your game, use the `build-apks` command:

```bash
java -jar bundletool.jar build-apks --bundle=build/my_game.aab --output=build/my_game.apks
```

If you want to deploy the APKs to a device, you must include your app's signing information. If you don't specify signing information, bundletool attempts to sign your APKs with a debug key.

```bash
java -jar bundletool.jar build-apks --bundle=/MyApp/my_app.aab --output=/MyApp/my_app.apks \
--ks=/MyApp/keystore.jks \
--ks-pass=pass:your_password \
--ks-key-alias=MyKeyAlias \
--key-pass=pass:your_key_password
```

### Options for `build-apks`

| Flag | Description |
|---|---|
| `--bundle=path` | **(Required)** Specifies the path to the app bundle `.aab` file. |
| `--output=path` | **(Required)** Specifies the name of the output `.apks` file. |
| `--overwrite` | Overwrites any existing output file with the specified path. |
| `--ks=path` | *(Optional)* Specifies the path to the deployment keystore used to sign the APKs. |
| `--ks-pass=pass:password` | Specifies your keystore password in plain text. (Use `file:/path` to pass a file instead). |
| `--ks-key-alias=alias` | Specifies the alias of the signing key you want to use. |
| `--key-pass=pass:password` | Specifies the password for the signing key itself. |
| `--connected-device` | Instructs bundletool to build APKs targeting *only* the configuration of a currently connected device. |
| `--device-id=serial` | If multiple devices are connected, specifies the serial ID of the target device. |
| `--device-spec=path` | Provides a path to a `.json` file specifying the target device configuration. |
| `--mode=universal` | Builds a single `.apk` compatible with all supported configurations, rather than split APKs. |
| `--local-testing` | Enables the app bundle for local testing, allowing quick, iterative testing cycles. |

---

## 2. Deploying APKs to a Connected Device

After generating an APK set, bundletool can deploy the exact right combination of APKs to a connected device.

To deploy your app from an APK set, use the `install-apks` command:

```bash
java -jar bundletool.jar install-apks --apks=/MyApp/my_app.apks
```

!!! note "Local Testing Flag"
    If you used the `--local-testing` flag with the `build-apks` command, you **must** use `install-apks` to install your APKs to ensure the local testing environment works correctly.

---

## 3. Device-Specific APK Sets

If you don't want to build an APK set for every possible device, you can target a specific device's configuration.

### Generating a Device Specification JSON
To generate a JSON configuration file from a connected device, run:

```bash
java -jar bundletool.jar get-device-spec --output=/tmp/device-spec.json
```

You can then pass this file to `build-apks` to generate a targeted APK set:

```bash
java -jar bundletool.jar build-apks --device-spec=/tmp/device-spec.json --bundle=/MyApp/my_app.aab --output=/MyApp/my_app.apks
```

### Manually Creating a Device Specification
If you don't have access to the physical device you want to build for, you can manually create the `.json` file:

```json
{
  "supportedAbis": ["arm64-v8a", "armeabi-v7a"],
  "supportedLocales": ["en", "fr"],
  "screenDensity": 640,
  "sdkVersion": 27
}
```

### Extracting from an Existing APK Set
If you already have a massive `.apks` file and want to extract just the APKs for a specific device JSON:

```bash
java -jar bundletool.jar extract-apks --apks=/MyApp/existing.apks --output-dir=/MyApp/extracted_apks --device-spec=/MyApp/pixel2.json
```

---

## 4. Measuring Estimated Download Sizes

To measure the estimated download sizes of APKs in an APK set (as they would be served compressed over the internet by Google Play), use the `get-size total` command:

```bash
java -jar bundletool.jar get-size total --apks=/MyApp/my_app.apks
```

### Options for `get-size total`

| Flag | Description |
|---|---|
| `--apks=path` | **(Required)** Specifies the path to the `.apks` file being measured. |
| `--device-spec=path` | Specifies the path to a device spec JSON file to use for matching configurations. |
| `--dimensions=dim` | Comma-separated list (`SDK`, `ABI`, `SCREEN_DENSITY`, `LANGUAGE`, or `ALL`) to compute sizes across. |