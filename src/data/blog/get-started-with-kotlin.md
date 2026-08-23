---
title: Get Started with Kotlin How to Create Your First Android App
author: Sunny
pubDatetime: 2026-08-15T10:06:31Z
slug: get-started-with-kotlin
featured: false
draft: false
tags:
  - Kotlin
  - Android development
  - Jetpack Compose
description:
  If you want to build an Android app today, Kotlin and Jetpack Compose are the tools you need.
  While older Android apps were built using a mix of Java/Kotlin and XML files for the layout, modern Android development is entirely Kotlin-first. Jetpack Compose is Android’s modern UI toolkit, allowing you to build your app's interface using only Kotlin code—saving you time, reducing bugs, and providing instant previews as you type.
---


Here is a step-by-step guide to building and running your very first "Hello World" app.

---

## Building Your First App

Before you begin, ensure you have downloaded and installed **Android Studio**, the official Integrated Development Environment (IDE) built by Google specifically for Android.

1. **Create a New Project:** Select the Compose template.
Open Android Studio and click **Start a new Android Studio project** (or File > New > New Project).

In the template selection window, choose **Empty Activity**.

*Note: In modern versions of Android Studio, "Empty Activity" creates a Jetpack Compose project. If you see "Empty Views Activity", that creates the older XML-based project—avoid that for this guide.*


2. **Configure Your App:**
Fill out the basic details for your app:

* **Name:** My First App
* **Package name:** `com.example.myfirstapp` (this acts as a unique identifier for your app)
* **Language:** Kotlin (this is enforced for Compose)
* **Minimum SDK:** Choose API 24 or higher.

Click **Finish** and allow Android Studio a few moments to download necessary dependencies and build the initial project.


3. **Explore MainActivity.kt:** Where the magic happens.
Once the project loads, open the `MainActivity.kt` file. This is the entry point of your app. You will see code that looks something like this:

```kotlin
package com.example.myfirstapp

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Greeting("Android Developer")
        }
    }
}

@Composable
fun Greeting(name: String) {
    Text(text = "Hello, $name!")
}

```


4. **Run the App:** Test your code on a virtual device.
In the top toolbar of Android Studio, look for the **Device Manager** dropdown. Select an existing emulator (like a Pixel device) or create a new Virtual Device.

Click the green **Play (Run)** button. Android Studio will compile your code, start the emulator, and launch your app. You should see a white screen displaying "Hello, Android Developer!".


---

## Key Concepts to Understand

As you begin your journey, there are two primary concepts you'll interact with constantly:

### 1. Composable Functions

In Jetpack Compose, UI components are called "Composables". You create them by adding the `@Composable` annotation above a Kotlin function. Composables describe *what* the UI should look like based on the data passed to them, rather than dictating *how* to construct it step-by-step. In the code above, `Greeting()` is a Composable function.

### 2. State and Recomposition

Unlike the older XML system where you had to manually find a text box on the screen and update it when data changed, Compose is reactive. If you create a variable that holds a state (like a counter or a user's name) and pass it to a Composable, the UI will automatically redraw itself (a process called "recomposition") whenever that state changes.