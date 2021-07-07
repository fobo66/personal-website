---
title: "Migrate from Play Services Location to Android's LocationManager API"
published: false
---

Hello! After recent announcement from Microsoft about Android apps support in Windows 11, I was excited to try to publish my app to Amazon Appstore, so I can discover more form factors and attract more users. However, there was one caveat: Windows can only run apps without Google Play Services dependency, since it will not be present in Windows and in Amazon Appstore. So, I started looking for workarounds.

My app was using only FusedLocationProviderClient API from Play Services, and only to load current location of the device, so I found a way how I can achieve similar results in more Kotlin-friendly way with help of existing Android SDK LocationManager API. It turns out that there was not so much info about Location API in Android, since even [official docs](https://developer.android.com/guide/topics/location/migration) are advising to use Play Services.

In this article, I will tell a bit about Play Services and LocationManager, warn you about some caveats of Android's Location API and show how you can load current location with `LocationManager`.
