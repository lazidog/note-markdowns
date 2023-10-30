# Glide

By default, Glide checks multiple layers of caches before starting a new request for an image:

1. Active resources — Is this image displayed in another View right now?
2. Memory cache — Was this image recently loaded and still in memory?
3. Resource — Has this image been decoded, transformed, and written to the disk cache before?
4. Data — Was the data this image was obtained from written to the disk cache before?

If all four steps fail to find the image, then Glide will go back to the original source to retrieve the data from the URL

## Best Image Loading and Caching Strategy

### 1. Enable Disk Cache

- Applications that use the same resource multiple times in multiple sizes and are willing to trade off some speed and disk space in return for lower bandwidth usage may want to consider enabling disk cache.

  ```kotlin
  val requestOptions = RequestOptions().diskCacheStrategy(DiskCacheStrategy.ALL)
  Glide.with(context).load(url).apply(requestOptions).into(imageView)
  ```

### 2. Add Image Signature

- Guess, what if the image on same URL is changed? Glide will show the old image from cache.
- Make sure whenever the image on server is changed, it also notifies client. Let it be the latest date time stamp.
- Now it can be used for versioning in cache as well. Whenever new signature is provided, it will fetch the new image and cache it as well

  ```kotlin
  val requestOptions = RequestOptions().diskCacheStrategy(DiskCacheStrategy.ALL)
                                          .signature(ObjectKey(signature))
  Glide.with(context).load(url).apply(requestOptions).into(imageView)
  ```

### 3. Override Image Size

- ```kotlin
  val requestOptions = RequestOptions()
      .diskCacheStrategy(DiskCacheStrategy.ALL)
      .signature(ObjectKey(signature))
      .override(100, 100) // resize does not respect aspect ratio

  Glide.with(context).load(url).apply(requestOptions).into(imageView)
  ```

### 4. Add Thumbnail Url

- The thumbnail() will be displayed while the primary request is loading.
- If you only have a single remote URL, you can force Glide to load a lower resolution image in the thumbnail request

  ```kotlin
  // With thumbnail url
  Glide.with(context).load(url)
          .thumbnail(Glide.with(context).load(thumbUrl))
          .apply(requestOptions).into(imageView)
  // Without thumbnail url
  // If you know thumbnail size
  Glide.with(context).load(url)
          .thumbnail(Glide.with(context).load(url).apply(RequestOptions().override(thumbSize)))
          .apply(requestOptions).into(imageView)
  // With size multiplier
  Glide.with(context).load(url)
          .thumbnail(0.25f)
          .apply(requestOptions).into(imageView)
  ```

### 5. Setup Monthly Schedule for Cleaning

- ```kotlin
    // This method must be called on the main thread.
    Glide.get(context).clearMemory()

    Thread(Runnable {
        // This method must be called on a background thread.
        Glide.get(context).clearDiskCache()
    }).start()
  ```
