# Android

`Index`

- [Download](#download)
- [MemoryLeak](#memoryleak)
- [HandlingBitmap](#handling-bitmap)

## Download

---

- Starting in Android 11 (API 30), apps that use the scoped storage model can access only their own app-specific cache files. To access the directory that the system provides for your app, call getExternalFilesDirs(). [Read here](https://developer.android.com/about/versions/11/privacy/storage)

- Or use MediaStore:

  ```xml
  <manifest >
      ...
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <uses-permission android:name="android.permission.ACCESS_MEDIA_LOCATION" />
      <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
      ...
  </manifest>
  ```

  ```xml
  <application
      android:usesCleartextTraffic="true"
      android:requestLegacyExternalStorage="true"
      >
  </application>
  ```

- usesCleartextTraffic = true to connect "http://" (only https by default)

**[⬆ Back to Index](#index)**

## MemoryLeak

---

- Cause

  - use application context in activity scope
  - use activity context, view in static variable
  - hold reference of ui in a background tas
  - not unbind or unregister service

- Should
  - use static for inner class
  - use weekrefernce<view> in static class
  - use LeakCanary lib to detect memoryleak

**[⬆ Back to Index](#index)**

## Handling Bitmap

---

- OutOfMemoryError when load multiple image in comment
  - This error is thrown by the Java Virtual Machine (JVM) when an object cannot be allocated due to lack of memory space and also, the garbage collector cannot free some space.
  - Fix 1: use Glide and overide size [how to use glide effectively](https://github.com/tiennhot23/Android-Kit/tree/master/Glide)

```java
Glide.with(context).asBitmap().load(...).override(1080, 600).into(mImageView);
```

- Fix 2: add the below entities to manifest file

```manifest
android:hardwareAccelerated="false"
android:largeHeap="true"
```

- Fix 3: [Read here](https://developer.android.com/topic/performance/graphics)

**[⬆ Back to Index](#index)**
