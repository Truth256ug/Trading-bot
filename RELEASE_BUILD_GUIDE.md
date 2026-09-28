# MT5 Android Companion - Release APK Generation Guide

## Prerequisites

Before generating a release APK, ensure you have:

1. **Android SDK** (API level 21+)
2. **Android Build Tools** (latest version recommended)
3. **Java Development Kit (JDK)** 11 or higher
4. **Gradle** (included in Android Studio)

## Step 1: Extract the Project

```bash
unzip MT5_Android_Companion_Project.zip
cd MT5_Android_Companion_Project
```

## Step 2: Create a Keystore (One-time setup)

Generate a signing keystore for release builds:

```bash
keytool -genkey -v -keystore mt5_release.keystore -keyalg RSA -keysize 2048 -validity 10000 -alias mt5_key
```

**Important:** Save the password and alias details securely. You'll need them for future releases.

Store the keystore in your project root or a secure location:
```bash
mv mt5_release.keystore ./keystore/
```

## Step 3: Configure Gradle Signing

Create or update `gradle.properties` in the project root:

```properties
# Release Signing Configuration
RELEASE_STORE_FILE=keystore/mt5_release.keystore
RELEASE_STORE_PASSWORD=your_keystore_password
RELEASE_KEY_ALIAS=mt5_key
RELEASE_KEY_PASSWORD=your_key_password
```

## Step 4: Update build.gradle

Ensure your `app/build.gradle` includes signing configuration:

```gradle
android {
    ...
    
    signingConfigs {
        release {
            storeFile file(RELEASE_STORE_FILE)
            storePassword RELEASE_STORE_PASSWORD
            keyAlias RELEASE_KEY_ALIAS
            keyPassword RELEASE_KEY_PASSWORD
        }
    }

    buildTypes {
        release {
            signingConfig signingConfigs.release
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}
```

## Step 5: Generate Release APK

### Using Gradle (Command Line)

```bash
# Debug Build (for testing)
./gradlew assembleDebug

# Release Build (for distribution)
./gradlew assembleRelease
```

### Using Android Studio

1. Open Android Studio
2. Go to **Build** → **Generate Signed Bundle/APK**
3. Select **APK** (or **App Bundle** for Google Play)
4. Select or create your keystore
5. Choose **Release** build variant
6. Click **Finish**

## Step 6: Locate Your APK

After successful build, find your release APK at:

```
app/build/outputs/apk/release/app-release.apk
```

## Step 7: Verify the APK

Test the APK on a device or emulator:

```bash
adb install -r app/build/outputs/apk/release/app-release.apk
```

## Release Checklist

- [ ] Version code and version name updated in `build.gradle`
- [ ] Keystore file created and secured
- [ ] `gradle.properties` configured with signing credentials
- [ ] Proguard/R8 rules configured (if using code obfuscation)
- [ ] App tested on multiple devices
- [ ] Manifest file validated
- [ ] Permissions reviewed and justified
- [ ] API levels configured correctly

## Common Issues

### Issue: "Keystore not found"
**Solution:** Verify the keystore path in `gradle.properties` and ensure the file exists.

### Issue: "Invalid keystore password"
**Solution:** Re-check the password entered during keystore creation.

### Issue: "Build fails with missing dependencies"
**Solution:** Run `./gradlew build --refresh-dependencies`

## Security Best Practices

⚠️ **Never commit** your keystore or `gradle.properties` with passwords to version control.

Add to `.gitignore`:
```
keystore/
gradle.properties
local.properties
```

## Distribution Options

1. **Direct APK Distribution:** Share the `.apk` file directly
2. **Google Play Store:** Upload App Bundle (`.aab`)
3. **F-Droid/Third-party Stores:** Follow their submission guidelines
4. **Self-hosted:** Host on your own server

---

For more information, see [Android Official Build Guide](https://developer.android.com/build)
