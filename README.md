
# AdMob Integration for Android

This guide explains how to integrate AdMob into your Android application, including setup, initialization, and loading test ads.


![132965870-d1049500-b3bc-407b-b236-36920a4b4a68](https://github.com/3mohamed-abdelfattah/AdMob/assets/142848460/1b498494-5748-4911-b3ae-5a8ef966abe4)

## Prerequisites

- Android Studio
- An Android project
- An AdMob account

## 1. Set Up AdMob Account

1. Sign in to your [AdMob account](https://admob.google.com/intl/ar/home/).
2. Create a new app and obtain your AdMob App ID and Ad Unit ID.

## 2. Integrate AdMob SDK

### Step 1: Add AdMob SDK to Your Project

1. Open your project in Android Studio.
2. Open the `build.gradle` file for your app module and add the AdMob dependency.

```groovy
dependencies {
    implementation 'com.google.android.gms:play-services-ads:22.1.0'
}
```

3. Sync your project with Gradle files.

### Step 2: Modify AndroidManifest.xml

Add the following permissions and metadata to your `AndroidManifest.xml` file.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.yourapp">

    <application
        ...>
        <!-- AdMob App ID -->
        <meta-data
            android:name="com.google.android.gms.ads.APPLICATION_ID"
            android:value="ca-app-pub-xxxxxxxxxxxxxxxx~yyyyyyyyyy"/>

        <!-- Network Permissions -->
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        
        ...
    </application>

</manifest>
```

Replace `ca-app-pub-xxxxxxxxxxxxxxxx~yyyyyyyyyy` with your AdMob App ID.

## 3. Load a Banner Ad

### Step 1: Update Layout File

Add a container for the AdView in your activity's layout file (`res/layout/activity_main.xml`).

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <FrameLayout
        android:id="@+id/adViewContainer"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```

### Step 2: Initialize and Load Banner Ad

Update your `MainActivity` to initialize the AdMob SDK and load a banner ad.

```kotlin
package com.example.admob

import android.os.Bundle
import android.util.DisplayMetrics
import androidx.appcompat.app.AppCompatActivity
import com.example.admob.databinding.ActivityMainBinding
import com.google.android.gms.ads.AdRequest
import com.google.android.gms.ads.AdSize
import com.google.android.gms.ads.AdView
import com.google.android.gms.ads.MobileAds

class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    private lateinit var adView: AdView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Initialize Mobile Ads SDK
        MobileAds.initialize(this) {}

        loadBanner()
    }

    // Determine the screen width (less decorations) to use for the ad width.
    private fun getAdSize(): AdSize {
        val display = windowManager.defaultDisplay
        val outMetrics = DisplayMetrics()
        display.getMetrics(outMetrics)

        val density = outMetrics.density

        var adWidthPixels = binding.adViewContainer.width.toFloat()
        if (adWidthPixels == 0f) {
            adWidthPixels = outMetrics.widthPixels.toFloat()
        }

        val adWidth = (adWidthPixels / density).toInt()
        return AdSize.getCurrentOrientationAnchoredAdaptiveBannerAdSize(this, adWidth)
    }

    private fun loadBanner() {
        // Create a new ad view.
        adView = AdView(this)
        adView.adSize = getAdSize() // Set ad size using a method
        adView.adUnitId = "ca-app-pub-3940256099942544/9214589741"

        // Add the AdView to the layout
        binding.adViewContainer.addView(adView)

        // Create an ad request.
        val adRequest = AdRequest.Builder().build()

        // Start loading the ad in the background.
        adView.loadAd(adRequest)
    }

    override fun onPause() {
        adView.pause()
        super.onPause()
    }

    override fun onResume() {
        super.onResume()
        adView.resume()
    }

    override fun onDestroy() {
        adView.destroy()
        super.onDestroy()
    }
}
```

## 4. Load Test Ads

To ensure everything is working correctly before going live, use test ads provided by AdMob. Use the following test ad unit ID in your code:

```kotlin
adView.adUnitId = "ca-app-pub-3940256099942544/6300978111" // Test ad unit ID
```

For more information on test ads, visit the [AdMob Test Ads documentation](https://developers.google.com/admob/android/test-ads).

## 5. Troubleshooting

If you encounter any issues, refer to the following resources:

- [AdMob Quick Start Guide](https://developers.google.com/admob/android/quick-start)
- [AdMob Test Ads Guide](https://developers.google.com/admob/android/test-ads)
- [AdMob Community Support](https://support.google.com/admob/)
