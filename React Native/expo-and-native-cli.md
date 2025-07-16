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
> It allows you to **run Node.js packages (binaries) directly** without installing them globally. It downloads that library on fly temporary basis and remove that after use.

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

-------

# Reading SMS, Sending SMS with Expo SDK

Accessing **SMS functionality** with the **Expo SDK** depends on *what exactly you want to do* — reading SMS, sending SMS, or auto-verifying codes. Here's the breakdown:

---

## 📤 **1. Sending SMS Messages (with user interaction)**

Expo provides a module to **send SMS** using the device's native SMS app (but it requires user confirmation).

### ✅ Use: `expo-sms`

### 🔧 Install:

```bash
npx expo install expo-sms
```

### 💻 Example:

```js
import * as SMS from 'expo-sms';

const sendSMS = async () => {
  const isAvailable = await SMS.isAvailableAsync();

  if (isAvailable) {
    const { result } = await SMS.sendSMSAsync(
      ['1234567890'],      // Phone numbers
      'Hello from Expo!'   // Message
    );
    console.log(result);   // sent / cancelled
  } else {
    alert('SMS is not available on this device');
  }
};
```

---

## ❌ **2. Reading SMS Messages (e.g., OTP Auto-Read)**

> ❌ Expo **does NOT support reading SMS inbox** or auto-reading OTPs in the **Managed Workflow** because that requires sensitive Android permissions and native code (like `RECEIVE_SMS` or `READ_SMS`).

### To do this:

You must **eject to the Bare Workflow** and use a **custom native module** like:

* [`react-native-sms-retriever`](https://github.com/ammarahm-ed/react-native-sms-retriever)
* Native Java/Kotlin implementation on Android

> iOS does **not** allow SMS reading at all — Apple restricts this entirely.

---

## ✅ Alternative: SMS Code Auto-Fill (Android only)

You can use **Google’s SMS Retriever API** via the above libraries, which auto-fills verification codes **without reading SMS content directly**.

This also requires native permissions and setup, so it is **not available in Expo Managed Workflow**.

---

## 🔐 Summary Table

| Feature                    | Supported in Expo Managed | Requires Eject (Bare Workflow) |
| -------------------------- | ------------------------- | ------------------------------ |
| Send SMS (via user prompt) | ✅ Yes (`expo-sms`)        | ❌ Not needed                   |
| Read SMS (inbox)           | ❌ No                      | ✅ Yes                          |
| Auto-verify OTP (Android)  | ❌ No                      | ✅ Yes (with native libs)       |

--------------------------------


# React native cli 

npx react-native@latest init my-react-native-cli-app

## Some Errors during setup.

### Error - "The init command is deprecated. Switch to npx @react-native-community/cli init for identical behavior."

use command now  -

npx @react-native-community/cli init MyApp


### ERROR: JAVA_HOME is not set and no 'java' command could be found in your PATH.

Install java and jdk and add in env files.


### Error - Filename longer than 260 characters during andriod build after adding react-native-safe-area-context package.

This happens because Windows has a path length limit of 260 characters (aka MAX_PATH). React Native native modules (like react-native-safe-area-context) often generate deep paths when building for Android, which exceeds this limit.

Sol 1 - Enable Win32 long paths from gpedit.msc (Local Computer Policy → Computer Configuration → Administrative Templates → System → Filesystem)

Sol 2 - Move Your Project to a Shorter Path 
Current path is - C:\Users\mohitsaxena\MOHIT_WORKS\Learning Projects\MyReactNativeApp\
Make it  - C:\RNApp\
Then rebuild.

------------


# React native cli app does not work on Browser Correctly.

You've hit upon a very important distinction that often causes confusion! A standard React Native app, as created with `npx @react-native-community/cli init MyApp`, **does not natively run in a web browser**.

Here's why and what's involved if you want to make it work on the web:

## Why a Standard React Native App Doesn't Work Natively on the Browser

The core reason lies in the fundamental design principles and rendering mechanisms of React Native vs. web browsers:

1.  **Rendering Target:**
    * **React Native:** Is designed to render **native UI components**. When you use `<View>`, `<Text>`, `<Image>`, etc., React Native translates these into actual `UIView` and `UILabel` objects on iOS, and `android.view.View` and `TextView` on Android. It interacts directly with the operating system's native UI layer.
    * **Web Browsers:** Render **HTML elements** (`<div>`, `<p>`, `<img>`) and style them with **CSS**. They run JavaScript within a browser environment, not directly on the operating system's UI layer in the same way native apps do.

2.  **Layout and Styling:**
    * **React Native:** Uses **Yoga**, a cross-platform layout engine that implements a subset of Flexbox. It's designed to calculate layouts consistently across different native platforms. React Native styles are written in JavaScript objects (similar to inline CSS in React for web) but are *not* actual CSS. They are converted to native styling properties.
    * **Web Browsers:** Use the standard **CSS Box Model** and CSS properties for layout and styling. While Flexbox is a part of CSS, React Native's styling is a layer of abstraction *above* standard CSS. Things like pseudo-classes (`:hover`, `:focus`), direct child/sibling selectors, and complex CSS selectors aren't directly supported in standard React Native styling.

3.  **Bundling and Execution:**
    * React Native uses Metro for bundling, which produces a JavaScript bundle for native execution. This bundle isn't directly interpretable by a web browser without further tooling.
    * Web apps typically use bundlers like Webpack, Vite, or Rollup to produce JavaScript, HTML, and CSS bundles that browsers can understand.

## How to Make a React Native App Work on the Browser: **React Native for Web**

This is where the excellent library **`react-native-web`** comes in. Developed by Nicolas Gallagher (who also works at Meta/Facebook), `react-native-web` acts as a **compatibility layer**.

### What `react-native-web` Does:

`react-native-web` translates React Native's core components and APIs into their web equivalents:

* **`<View>` becomes `<div>`**
* **`<Text>` becomes `<p>` or `<span>`**
* **`<Image>` becomes `<img>`**
* **`StyleSheet.create` styles are converted into actual CSS styles.**
* It provides polyfills or web-specific implementations for common React Native APIs where possible.
 
 ---

 # Why Expo Works Everywhere Smoothly (web, android and ios) (Managed Workflow)

 You've hit on one of the **biggest advantages and core selling points of Expo!**

Yes, you're absolutely right: **apps created with Expo (specifically using the "Managed Workflow") are designed to work across iOS, Android, and Web much more smoothly and with far less setup than a "bare" React Native CLI project integrated with `react-native-web`.**

Let's break down why Expo achieves this smoothness:

Expo is a set of tools and services built on top of React Native that aims to simplify and accelerate the development of universal apps. Here's how it makes "everywhere" smooth:

1.  **Unified API (Expo SDK):**
    * Expo provides a comprehensive "SDK" (Software Development Kit) that wraps many native functionalities (camera, maps, push notifications, file system, etc.) with a single, consistent JavaScript API.
    * This means instead of `react-native-camera` for bare RN, you use `expo-camera`. Expo internally handles the native code for iOS, Android, and often the web equivalent, abstracting away the platform-specific differences.

2.  **Pre-built Native Modules:**
    * A significant portion of common native functionalities are already integrated and pre-built into the Expo Go client app and the standard Expo build process. This is the **key differentiator**.
    * When you use `expo-camera`, Expo already has the necessary native camera code linked. You don't need to touch `Podfile` for iOS, `build.gradle` for Android, or configure `webpack.config.js` for web.
    * This eliminates the need for `cd ios && pod install`, manual linking, or complex `webpack` configurations for most common use cases.

3.  **Automatic Web Configuration:**
    * Expo deeply integrates `react-native-web` and manages all the complex Webpack, Babel, and HTML configurations for you.
    * When you run `expo start --web`, it automatically spins up a web server, builds your app for the browser, and handles the `react-native-web` translations, dynamic imports, and asset management. You literally just run one command, and your app appears in the browser.

4.  **No Native Build Tools Locally (Managed Workflow):**
    * In the Managed Workflow, you don't typically need Android Studio, Xcode, or even CocoaPods installed on your local machine *just to develop*.
    * You run your app in the Expo Go client app (on your phone or emulator) during development.
    * When you need to build a standalone APK/AAB or IPA, you use Expo's cloud build service (`eas build`), which handles all the native compilation for you in the cloud.

5.  **Simplified Updates and Maintenance:**
    * Expo manages the native runtime versions. When you update your Expo SDK version, they ensure compatibility across all platforms.
    * "Over-the-air" (OTA) updates are a big feature, allowing you to push JavaScript updates to your app without going through app store review processes.

## The Trade-offs (Why "Bare" React Native Still Exists)

While Expo's Managed Workflow is incredibly smooth, it does come with some trade-offs:

1.  **Limited Native Code Access (Managed Workflow):**
    * You cannot directly add or modify native Objective-C/Swift (iOS) or Java/Kotlin (Android) code in the Managed Workflow. If your app needs a very specific native module that isn't part of the Expo SDK or the Expo ecosystem, you're stuck.
    * To use such custom native code, you would need to "eject" from the Managed Workflow to the "Bare Workflow" (which essentially converts your Expo project into a standard React Native CLI project), or use Expo's newer "Custom Development Clients" and "Config Plugins."

2.  **Bundle Size:**
    * Since the Expo Go app and cloud builds include *many* native modules in their runtime, your app's initial download size might be slightly larger, even if you only use a few of them.

3.  **Expo Ecosystem Reliance:**
    * You're more tied into the Expo ecosystem and their update cycles. While this is often a benefit (they handle compatibility), it means you can't just pick and choose *any* native library directly.

---------------

# Expo - bare workflow

The Bare Workflow allows you to **have full control over your `android/` and `ios/` directories**, just like a traditional React Native CLI project. However, you still benefit from many of Expo's tools and a significant portion of its SDK.

If you start with a Managed Workflow project and realize you need native code access, you can use the command:

```bash
npx expo prebuild
```

If `npx expo prebuild` only created the `android` folder and *not* the `ios` folder, the most probable reason is that you are running the command on a **Windows or Linux machine**.

**To generate the `ios` folder, you must run `npx expo prebuild` on a macOS machine.**

### **Evolution of Expo and the "Blurring Lines"**

It's important to note that Expo has continuously evolved to blur the lines between Managed and Bare. With the introduction of **Config Plugins** and **Custom Development Clients** within the Managed Workflow, many of the reasons one *had* to "eject" to the Bare Workflow have diminished.

  * **Config Plugins:** Allow library authors (or you) to define how native configurations should be automatically applied based on your `app.json` file. This means you can use many libraries that *would* require manual native changes, without ever touching the `android/` or `ios/` folders yourself.
  * **Custom Development Clients:** Let you build your *own* Expo Go-like app with only the native modules your app needs (including custom ones), still allowing you to develop with `expo start` and fast refresh.

---------


# When to Use Expo vs. Bare React Native CLI:

* **Choose Expo (Managed Workflow) if:**
    * You want the fastest development setup.
    * Your app primarily uses common mobile functionalities (camera, maps, notifications, etc.) that are covered by the Expo SDK.
    * You want to build for web easily.
    * You prefer not to deal with native build environments (Xcode, Android Studio, Gradle, CocoaPods).
    * You value rapid iteration and OTA updates.
    * You are building an MVP, a simple app, or prototyping.

* **Choose Bare React Native CLI (or Expo Bare Workflow/Custom Development Client) if:**
    * Your app requires highly specialized native modules that are not part of the Expo SDK and cannot be covered by a Config Plugin.
    * You need to write custom native code (Objective-C/Swift/Java/Kotlin) or have very specific native build configurations.
    * You have an existing native codebase you want to integrate with React Native.
    * You need ultimate control over the native build process and dependencies.


## Recommendation:

For most React Native applications that anticipate needing *some* native functionality beyond what the Managed Workflow offers, the **Expo Bare Workflow with Config Plugins and Custom Development Clients is generally the better approach.**

It gives you:
* The **flexibility of full native access** (just like CLI).
* The **convenience of Expo's powerful tools** (SDK, EAS Build, Config Plugins, Development Clients).
* A **smoother developer experience** for common native integrations (via config plugins).

It strikes a very good balance between control and developer productivity. You get the best of both worlds without the full burden of managing everything from scratch.

--------------

# React Native Debugger

React Native Debugger works only up to RN 0.78. At RN 0.79+ it's broken, since the remote debugging bridge was removed. Remote JS Debugging, the mechanism that tools like React Native Debugger relied on, was removed in v0.79.
React Native DevTools is now the official, supported debugger (requires Hermes, RN ≥ 0.76).
