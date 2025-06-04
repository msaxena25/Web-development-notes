# 🚀 What is Expo?

> **Expo** is an **open-source platform** built on top of **React Native** that simplifies the development, building, and deployment of mobile apps.

Think of it as a **toolkit + service layer** that helps you get started and go faster with React Native — especially if you don’t want to dive deep into native code initially.

---

## 🔗 How is Expo Connected to React Native?

* **Expo is built on React Native** — it uses React Native internally.
* It wraps the React Native APIs and provides:

  * Pre-built libraries (camera, maps, sensors, notifications, etc.)
  * Tools (CLI, Expo Go app)
  * Hosting & build services (via [Expo.dev](https://expo.dev))

---

## ⚙️ What Does Expo Provide?

| Tool/Feature                        | Description                                                                           |
| ----------------------------------- | ------------------------------------------------------------------------------------- |
| **Expo Go App**                     | A mobile app to run your React Native code instantly without building native binaries |
| **Expo CLI**                        | Command line tool to start and manage Expo projects                                   |
| **Expo SDK**                        | A collection of pre-built native modules (e.g., camera, location, audio)              |
| **EAS (Expo Application Services)** | Cloud services for building, submitting, and updating your app (build, updates, etc.) |
| **OTA Updates**                     | Over-the-air updates to your JS code without app store resubmission                   |

---

## 🧠 Key Differences Between Expo and Plain React Native

| Feature               | Expo (Managed)                         | React Native (Bare)               |
| --------------------- | -------------------------------------- | --------------------------------- |
| Native Code Access    | ❌ Not allowed (in managed workflow)    | ✅ Full access                     |
| Setup Time            | ⚡ Super fast (1 command to start)      | 🛠️ Requires Xcode/Android Studio |
| Pre-built APIs        | ✅ Comes with many (camera, maps, etc.) | ❌ Must add manually               |
| OTA Updates           | ✅ Built-in via Expo                    | ❌ Manual or via third-party       |
| Custom Native Modules | ❌ Limited (unless using Bare Workflow) | ✅ Fully customizable              |

---

## 🛠️ Two Workflows in Expo

1. **Managed Workflow**

   * Write only JS/TS (no native code)
   * Fastest setup, best for prototyping & small apps

2. **Bare Workflow**

   * Full access to iOS/Android native code
   * Works like plain React Native + Expo SDK

---

Full list of **important Expo commands** to **create**, **start**, **build**, and **manage** your React Native app using **Expo CLI** and **EAS CLI (Expo Application Services)**.

---

## 🔧 **1. Install Expo CLI (One-time)**

```bash
npm install -g expo-cli
```

---

## 🚀 **2. Create a New Expo App**

```bash
npx create-expo-app my-app
cd my-app
```

### Optional: With template (e.g., with TypeScript)

```bash
npx create-expo-app my-app --template
```

---

## ▶️ **3. Start the App in Dev Mode**

```bash
npx expo start
```

* Opens Metro Bundler in your browser.
* Scan QR code with **Expo Go app** to preview.

---

## 🛠️ **4. Run on Specific Platforms (Simulators or Devices)**

| Platform | Command                         |
| -------- | ------------------------------- |
| Android  | `npx expo run:android`          |
| iOS      | `npx expo run:ios` *(mac only)* |
| Web      | `npx expo start --web`          |

---

## 📦 **5. Install Expo SDK Packages**

```bash
npx expo install expo-camera expo-location
```

> ⚠️ Use `expo install` instead of `npm install` so it picks the correct compatible versions.

---

## 🧪 **6. Test App on Real Device**

* Install **Expo Go** from the Play Store / App Store.
* Scan the QR code from Metro Bundler (`expo start`).

---

## 📲 **7. Build APK / AAB / IPA using EAS**

### First, install EAS CLI:

```bash
npm install -g eas-cli
```

### Login to your Expo account:

```bash
eas login
```

---

### Configure EAS (One-time):

```bash
eas build:configure
```

---

### Android Build (APK or AAB):

```bash
eas build --platform android
```

### iOS Build (requires Apple Developer account & macOS):

```bash
eas build --platform ios
```

> Output will be hosted on Expo servers and you'll get a shareable link.

---

## 🌐 **8. Publish OTA (Over-the-Air) Update**

```bash
npx expo publish
```

> Sends JS-only updates instantly to users without app store submission.

---

## 🛑 **9. Stop Development Server**

```bash
CTRL + C
```

---

## 🧹 **10. Clear Cache**

```bash
npx expo start --clear
```

---

## 🗑️ **11. Delete Build Artifacts (optional)**

```bash
rm -rf node_modules .expo .expo-shared
```

Then reinstall:

```bash
npm install
```

---

# ⚙️ What is `npx`?

> **`npx`** is a command-line utility that comes with **Node.js** (since version 5.2.0 of `npm`).  
> It allows you to **run Node.js packages (binaries) directly** without installing them globally.

---

## 🔍 Why Use `npx`?

Because it:
- Runs the **latest version** of a CLI tool temporarily.
- **Avoids global installs**, keeping your system clean.
- Executes CLI commands from your `node_modules/.bin` folder if already installed locally.
- Useful for **one-time or project-specific tools**.

---

## 🧪 Example with Expo:

```bash
npx create-expo-app myApp
```

- This downloads the latest version of `create-expo-app` from npm.
- Runs it **without permanently installing it** on your system.
- Once the command finishes, the binary is discarded (unless cached).

---

## ✅ When to Use `npx` vs `npm install`

| Task                        | Command                        | Notes                              |
|-----------------------------|--------------------------------|-------------------------------------|
| Run once-off CLI tools      | `npx create-expo-app`          | Fast, temporary use                 |
| Install tools globally      | `npm install -g expo-cli`      | Available system-wide, version-controlled |
| Install for local project   | `npm install expo`             | Persisted in `node_modules`         |

---

## 💡 Pro Tip:
If you run `npx expo start` in a project that already has `expo` installed locally, `npx` will run the **local version**, ensuring compatibility.

-------------

# 📦 What is Expo SDK?

> **Expo SDK** is a collection of **pre-built libraries and APIs** that help you access native device features in a **React Native app** without writing native code.

It allows you to use features like camera, location, notifications, sensors, biometrics, etc., using **pure JavaScript or TypeScript**.

---

## 🧰 Expo SDK Includes:

Here are some of the powerful native modules included:

| Feature / Module     | Description                                      |
| -------------------- | ------------------------------------------------ |
| `expo-camera`        | Access device camera                             |
| `expo-location`      | Get GPS location and geofencing                  |
| `expo-notifications` | Handle push/local notifications                  |
| `expo-image-picker`  | Pick images from gallery or camera               |
| `expo-media-library` | Access and manage media on the device            |
| `expo-sensors`       | Use accelerometer, gyroscope, magnetometer, etc. |
| `expo-file-system`   | Read/write files on device                       |
| `expo-contacts`      | Access phonebook contacts                        |
| `expo-speech`        | Use text-to-speech                               |
| `expo-clipboard`     | Read/write clipboard content                     |
| `expo-av`            | Work with audio & video playback                 |
| `expo-secure-store`  | Store data securely (encrypted keychain/storage) |

…and many more.

---

## 🚀 Key Features of Expo SDK

| Feature                         | Description                                         |
| ------------------------------- | --------------------------------------------------- |
| ✅ **No native code required**   | Use device features with pure JS                    |
| 🔄 **Cross-platform**           | Works on iOS, Android, and sometimes Web            |
| 📱 **Hot reloading compatible** | Works instantly with `expo start`                   |
| 🔄 **Over-the-air updates**     | APIs stay compatible with Expo OTA update mechanism |
| 🔧 **Auto linking**             | Modules are linked automatically                    |

---

## 🛠️ Example: Using Expo Camera

```bash
npx expo install expo-camera
```

Then in your code:

```js
import { Camera } from 'expo-camera';

const permission = await Camera.requestCameraPermissionsAsync();
const { status } = permission;

if (status === 'granted') {
  // Open camera
}
```

---

## 📅 Expo SDK Versions

Each version of the Expo SDK is **tightly coupled with a specific React Native version**.

* Latest stable (as of mid-2025): **Expo SDK 50**
* SDK versions follow a consistent release cycle (\~ every quarter)

Check [https://expo.dev](https://expo.dev) for the current version.
