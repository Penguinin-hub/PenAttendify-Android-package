
# PenAttendify Framework Integration Guide (for Android)

## Table of Contents
- [PenAttendify Framework Integration Guide (for Android)](#penattendify-framework-integration-guide-for-android)
  - [Table of Contents](#table-of-contents)
  - [1. Introduction](#1-introduction)
  - [2. Requirements](#2-requirements)
  - [3. Installation](#3-installation)
    - [3.1 Gradle Integration](#31-gradle-integration)
    - [3.2 Manual Installation](#32-manual-installation)
  - [4. Getting Started](#4-getting-started)
  - [5. Usage](#5-usage)
    - [5.1 Initialize PenAttendify](#51-initialize-penattendify)
    - [5.2 Start Scanning](#52-start-scanning)
    - [5.3 Stop Scanning](#53-stop-scanning)
    - [5.3 is Initialized](#53-is-initialized)
  - [6. Callback Methods](#6-callback-methods)
    - [6.1 onReady](#61-onready)
    - [6.2 onInitilizationError](#62-oninitilizationerror)
    - [6.3 onDetectionReady](#63-ondetectionready)
  - [7. Error Handling](#7-error-handling)
  - [8. Deinitialization](#8-deinitialization)
  - [9. FAQs](#9-faqs)
  - [10. Contact \& Troubleshooting](#10-contact--troubleshooting)

## 1. Introduction
PenAttendify is a powerful library that enables your Android app to engage with Bluetooth-enabled beacons, facilitating proximity-based user location detection. Through PenAttendify, seamless scanning for nearby beacons becomes achievable, allowing you to identify the closest zones with a degree of confidence indicated by the SDK's detection certainty.

This integration guide will walk you through the steps to integrate the PenAttendify library into your Android app. It will also aid you in initiating the utilization of its functionalities.

---

## 2.1 Requirements
- Android API level 24+
- Android Studio 4.0+
- Device with Bluetooth support.
- Device with GPS (Location) support.

The PenAttendify library relies on Bluetooth and GPS functionality to scan for nearby beacons and determine the user's location. Therefore, it is essential to ensure that the target devices have both Bluetooth and GPS capabilities enabled. 

Please note that some older devices or devices with custom firmware may have limited or restricted support for Bluetooth and GPS. Ensure that your target devices meet the specified requirements to achieve optimal functionality with PenAttendify.

---

## 2.2 Pre-request dependencies
It is essential to obtain a list of dependencies. The following dependencies need to be added to the build.gradle file:
```gradle
implementation 'com.squareup.retrofit2:adapter-rxjava2:2.9.0'
implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
```
## 3. Installation
### 3.1 Gradle Integration
You can integrate PenAttendify into your Android project using Gradle. Follow these steps:

Include the Maven repository in your project's settings.gradle file by adding the following code:
```gradle
allprojects {
    repositories {
        ...
          maven {

            url "https://maven.pkg.github.com/Penguinin-hub/PenAttendify-Android-package"
            credentials {
                username "PenguinINHub"
                password "your_access_token_provided_by_penguin"

            }
        }
    }
}
```

To add the PenAttendify library dependency to your app's build.gradle file, include the following code:
```gradle
dependencies {
   implementation 'com.penguinin:penattendify:0.0.12'
}
```

Sync your project with Gradle files to resolve the dependencies.



---

## 4. Getting Started
Before you start using PenAttendify in your app, you need to perform the following steps:

1. Obtain API Credentials: Contact our support team to get your API credentials, including clientID, clientKey, baseUrl, companyId, etc.

2. Request Permissions: Ensure that your app has the necessary permissions to use PenAttendify. Add the following permissions to your AndroidManifest.xml::
```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
    <uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
    <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
```

Make sure to include these permissions to enable PenAttendify to scan for nearby beacons and access necessary network resources.

---

## 5. Usage
### 5.1 Initialize PenAttendify
To use PenAttendify in your app, you must initialize the library with the obtained API credentials. The initialization process should happen early in your app's lifecycle, preferably in the `onCreate` method of your Application class:

```kotlin
import android.app.Application
import com.penguinin.penattendify.PIAttendify
import com.penguinin.penattendify.PIConfiguration

class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        // Replace the following placeholders with your actual API credentials.
        val configuration = PIConfiguration.Builder()
            .setBaseUrl("your_base_url")
            .setClientID("your_client_id")
            .setClientKey("your_client_key")
            .setCompanyId(1)
            .build()

        PIAttendify.getInstance().initialize(this, configuration, object : PIAttendifyInitializerDelegate {
            override fun onReady() {
                // PenAttendify is ready to use. Proceed with other actions...
            }

            override fun onInitilizationError(errorType: PenAttendifyError, message: String) {
                // Handle initialization errors here.
            }
        })
    }
}
```

### 5.2 Start Scanning
After successful initialization, you can start scanning for nearby beacons by calling the `startScan` method:

```kotlin
PIAttendify.getInstance().startScan(this, object : PIAttendifyDetectionDelegate {
    override fun onDetectionReady(detectedZones: ArrayList<DetectedZone>) {
        // Process the detected zones here.
    }
})
```

### 5.3 Stop Scanning
To stop the beacon scanning process, use the `stopScan` method:

```kotlin
PIAttendify.getInstance().stopScan()
```

### 5.3 is Initialized
The isInitialized() method allows you to check if the PenAttendify library has been successfully initialized. It returns a boolean value indicating whether the library has been initialized or not.

```kotlin
PIAttendify.getInstance().isInitialized()
```

## 6. Callback Methods
PenAttendify provides delegate methods to receive important events and error notifications. Implement the callback methods to handle these events:

### 6.1 onReady
This method is called when PenAttendify has finished initializing successfully. You can use this event to proceed with other actions in your app after successful initialization:

```kotlin
override fun onReady() {
    // PenAttendify initialized successfully. Proceed with other actions...
}
```

### 6.2 onInitilizationError
This method is called when an error occurs during PenAttendify initialization

. The `errorType` parameter indicates the type of error that occurred, and the `message` parameter provides additional information about the error:

```kotlin
override fun onInitilizationError(errorType: PenAttendifyError, message: String) {
    // Handle initialization errors here.
}
```

### 6.3 onDetectionReady
This method is called when the beacon scanning process finishes with the scan results. It provides an ArrayList of `DetectedZone` objects, representing the detected zones from scanned beacons. Each `DetectedZone` object contains zone information such as zone ID, zone name, and trust factor:

```kotlin
override fun onDetectionReady(detectedZones: ArrayList<DetectedZone>) {
    // Process the detected zones here.
}
```

## 7. Error Handling
The `PenAttendifyError` enum represents the possible errors that can occur in the PenAttendify library. You should handle these errors in the `onInitilizationError` method:

```kotlin
enum class PenAttendifyError {
    LocationPermissionUnauthorized,
    BluetoothTurnedOff,
    ServiceNotInitialized
}
```

## 8. Deinitialization
To release resources and properly deinitialize the PenAttendify library, call the `deinitialize()` method:

```kotlin
PIAttendify.getInstance().deinitialize()
```
---
## 9. FAQs
-  **Q:How can I check if the PenAttendify library is already initialized?**
  A: You can use the `PIAttendify.getInstance().isInitialized()` method to check the initialization status. This method returns a Boolean value indicating whether PenAttendify has been initialized or not.


-  **Q: Can I run multiple instances of PenAttendify in the same app?**
A: PenAttendify is designed to work as a singleton instance in your app. Running multiple instances may lead to unexpected behavior and is not recommended. It is best to use a single instance of PenAttendify throughout your app's lifecycle.
 

- **Q: Can I use PenAttendify with background app refresh enabled?**
  A: No, it does not support background processes.

- **Q: Can PenAttendify scan for beacons while the app is in the background??**
A: PenAttendify does not support scanning for beacons while the app is in the background. Android imposes restrictions on Bluetooth scanning in the background to preserve battery life. When the app moves to the background, PenAttendify's scanning will be paused, and you will not receive beacon detection updates.


- **Q: When should I use the `PIAttendify.getInstance().deinitialize()` method?**
  A: You should use the `deinitialize()` method when you want to release any allocated resources and stop using the PenAttendify library in your app. It is recommended to call `deinitialize()` before reinitializing PenAttendify with a new configuration or when your app no longer needs to use the library.

-  **Q: Can I customize the scanning interval or other settings in PenAttendify?**
A: Currently, PenAttendify does not expose direct settings for customization, including the scanning interval. The library is designed to work optimally with default settings for most use cases. If you have specific customization requirements, please contact our support team for further assistance.


-  **Q: Does PenAttendify work in airplane mode or with Bluetooth turned off?**
A: PenAttendify requires both Bluetooth and location services to be enabled on the device to function properly. If the device is in airplane mode or has Bluetooth turned off, PenAttendify will not be able to scan for nearby beacons. Users are advised to have Bluetooth and location services enabled for the best experience with PenAttendify.


- **Q: How can I receive events from PenAttendify?**
  A: To receive events from PenAttendify, you need to conform to the `PIAttendifyInitializerDelegate` and `PIAttendifyDetectionDelegate` interfaces in your class and set the class as the delegate for PenAttendify by assigning it to `PIAttendify.getInstance().initializeDelegate` and `PIAttendify.getInstance().detectionDelegate` respectively.

- **Q: How does PenAttendify handle battery consumption?**
A: PenAttendify is designed to minimize battery consumption while providing accurate beacon scanning and detection. The library employs optimized scanning intervals and Bluetooth Low Energy (BLE) technology to reduce power usage. However, keep in mind that continuous scanning for nearby beacons may still have some impact on the device's battery life.

- **Q: Does PenAttendify store user location data or personal information?**
A: PenAttendify does not store or transmit any user location data or personal information. The library only provides beacon detection results based on nearby beacons' signals. It is the responsibility of the app developer to handle and manage user data and privacy in compliance with relevant data protection laws and regulations.

---
## 10. Contact & Troubleshooting
If you encounter any issues or need further assistance, please refer to the FAQs section or contact our support team at devsupport@penguinin.com.
