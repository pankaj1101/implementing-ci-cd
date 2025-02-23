# Flutter Signed APK Build

This GitHub Action automates the process of building a signed APK for a Flutter application. It runs on every push or pull request to the `main` branch, builds the APK, and uploads it as an artifact and a GitHub release. It also uploads the APK to Firebase App Distribution for testing.

## Workflow Overview

### Triggers

- Runs on `push` to the `main` branch.
- Runs on `pull_request` to the `main` branch.

### Jobs

#### 1. **Build Signed APK**

This job builds a signed APK using Flutter and uploads it to GitHub Releases and Firebase App Distribution.

#### Steps:

1. **Checkout Repository**

   - Clones the repository to the GitHub Actions runner.

2. **Setup Flutter**

   - Uses the `subosito/flutter-action@v2` to install Flutter version `3.27.0`.
   - Uses the `stable` channel and enables caching.

3. **Install Dependencies**

   - Runs `flutter pub get` to install project dependencies.

4. **Decode Keystore and Key Properties Files**

   - Decodes the base64-encoded keystore and key properties file stored in GitHub Secrets.

5. **Build Signed APK**

   - Runs `flutter build apk --release` with dynamically generated build name and number.

6. **Upload Signed APK as Artifact**

   - Uses `actions/upload-artifact@v4` to store the generated APK in GitHub Actions artifacts.

7. **Create GitHub Release**

   - Uses `softprops/action-gh-release@v1` to create a release and attach the signed APK.

8. **Upload to Firebase App Distribution**
   - Uses `wzieba/Firebase-Distribution-Github-Action@v1` to distribute the APK to Firebase testers.

## Required Secrets

To use this workflow, you need to add the following secrets in your GitHub repository:

- `ANDROID_KEYSTORE_BASE64`: Base64-encoded Android keystore file.
- `ANDROID_KEY_PROPERTIES_BASE64`: Base64-encoded key properties file.
- `TOKEN`: GitHub token for creating releases.
- `FIREBASE_APP_ID`: Firebase App ID for distribution.
- `GOOGLE_APPLICATION_CREDENTIALS`: Firebase service account credentials in JSON format.

## Generating Base64 Encoding

To encode your keystore and key properties files into base64, use the following commands:

### Windows (PowerShell)

```powershell
[convert]::ToBase64String((Get-Content -Path android\app\keystore.jks -Encoding byte))
```

```powershell
[convert]::ToBase64String((Get-Content -Path android\key.properties -Encoding byte))
```

### Linux/macOS

```bash
base64 android/app/keystore.jks
```

```bash
base64 android/key.properties
```

## Usage

1. Add the `.github/workflows/flutter_signed_apk.yml` file to your repository.
2. Ensure the required secrets are added to the repository settings.
3. Push code to the `main` branch to trigger the build process.
4. Download the signed APK from the GitHub Releases or Firebase App Distribution.

## Outputs

- The built APK is available as an artifact in GitHub Actions.
- The APK is attached to a GitHub Release.
- The APK is distributed to Firebase testers.

## Notes

- Make sure your Flutter project is properly configured with a release signing key.
- Ensure that the `android/key.properties` file references the correct keystore path and credentials.

---

**Author:** _Pankaj Ram_  
**License:** MIT
