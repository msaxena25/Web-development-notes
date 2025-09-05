# APK build in React Native

To create an **APK build in React Native**, you need to use the **Gradle build system** inside the `android` folder of your project.
Here’s a step-by-step guide:

---

### **1️⃣ Go to the Android folder**

Open a terminal in your React Native project root and run:

```bash
cd android
```

---

### **2️⃣ Clean the project (optional but recommended)**

```bash
./gradlew clean
```

For Windows PowerShell or Command Prompt:

```cmd
gradlew clean
```

---

### **3️⃣ Build a Debug APK**

For testing purposes:

```bash
./gradlew assembleDebug
```

or on Windows:

```cmd
gradlew assembleDebug
```

📂 The APK will be generated at:

```
android/app/build/outputs/apk/debug/app-debug.apk
```

---

### **4️⃣ Build a Release APK**

For a production-ready build:

```bash
./gradlew assembleRelease
```

or on Windows:

```cmd
gradlew assembleRelease
```

📂 The release APK will be located at:

```
android/app/build/outputs/apk/release/app-release.apk
```

---

### **5️⃣ (Optional) Sign the Release APK**

To upload your app to Play Store, you must sign it:

1. Generate a keystore (only once):

   ```bash
   keytool -genkey -v -keystore my-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
   ```
2. Place `my-key.keystore` in `android/app/`.
3. Configure `android/gradle.properties`:

   ```
   MYAPP_UPLOAD_STORE_FILE=my-key.keystore
   MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
   MYAPP_UPLOAD_STORE_PASSWORD=your-password
   MYAPP_UPLOAD_KEY_PASSWORD=your-password
   ```
4. Edit `android/app/build.gradle` to use the signing config.

Then rerun:

```bash
./gradlew assembleRelease
```

---

### **6️⃣ Install the APK on a device**

Debug build:

```bash
npx react-native run-android
```

Or manually:

```bash
adb install android/app/build/outputs/apk/debug/app-debug.apk
```

---

### **Quick Tip**

For faster builds, you can use:

```bash
cd android && ./gradlew assembleRelease --parallel
```

---
