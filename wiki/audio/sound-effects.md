---
title: Sound effects
---
Sound effects are small audio samples, usually no longer than a few seconds, that are played back on specific game events such as a character jumping or shooting a gun.

Sound effects can be stored in various formats, including MP3, OGG and WAV. Which format you should use, depends on your specific needs, as each format has its own advantages and disadvantages. For example, WAV files are quite large compared to other formats, OGG files don’t work on RoboVM (iOS) nor with Safari (GWT), and MP3 files have issues with seemless looping.

## Android limitations

On Android, `Sound` instances are limited to 1 MiB in size, as raw PCM (not the file size).

The table below shows the number of seconds (rounded down to 3 decimal places) achievable with different sample rates and channels. Sounds always use a bit depth of 16.

| Sample rate | Stereo | Mono   |
|-------------|--------|--------|
| 48 kHz      | 5.461  | 10.922 |
| 44.1 kHz    | 5.944  | 11.888 |
| 32 kHz      | 8.192  | 16.384 |
| 24 kHz      | 10.922 | 21.845 |

The difference in fidelity between 32kHz and above is inaudible to most people, assuming it isn't stored as MP3 (which discards high frequencies). It is [not recommended](https://developer.android.com/ndk/guides/audio/sampling-audio#use-simple-resampling-ratios-fixed-versus-interpolated-polyphases) to use completely arbitrary sample rates.

For longer sounds, [`Music`](/wiki/audio/streaming-music) can be used instead. Even on other backends, loading extremely long sounds is best avoided for compatibility, resources, and load times reasons.

## Loading sounds

Sound effects are represented by the [Sound](https://javadoc.io/doc/com.badlogicgames.gdx/gdx/latest/com/badlogic/gdx/audio/Sound.html) interface. Loading a sound effect works as follows:

```java
Sound sound = Gdx.audio.newSound(Gdx.files.internal("mysound.mp3"));
```

This loads an audio file called `"mysound.mp3"` from the assets directory.

Once we have the sound loaded we can play it back:

```java
sound.play(1.0f);
```

This will playback the sound effect once, at full volume. The play method on a single `Sound` instance can be called many times in a row, e.g. for a sequence of shots in a game, and will be overlaid accordingly.

More fine-grained control is available. Every call to `Sound.play()` returns a long which identifies that sound instance. Using this handle we can modify that specific playback instance:

```java
long id = sound.play(1.0f); // play new sound and keep handle for further manipulation
sound.stop(id);             // stops the sound instance immediately
sound.setPitch(id, 2);      // increases the pitch to 2x the original pitch

id = sound.play(1.0f);      // plays the sound a second time, this is treated as a different instance
sound.setPan(id, -1, 1);    // sets the pan of the sound to the left side at full volume
sound.setLooping(id, true); // keeps the sound looping
sound.stop(id);             // stops the looping sound 
```

***note:*** These modifier methods have limited functionality in the JavaScript/WebGL back-end for now. As of 1.9.6 `setPan()` is only working when flash is supported and enabled `GwtApplicationConfiguration.preferFlash = true`

***note:*** `setPan()` method does not work with stereo sounds

Once you no longer need a Sound, make sure you dispose of it:

```java
sound.dispose();
```

Accessing the sound after you disposed of it will result in undefined errors.

## Simultaneous sounds

By default, libGDX is restricted to playing 16 sounds simultaneously. This limit can be raised or lowered as desired via each module's `ApplicationConfiguration` object.

### Android

Set `maxSimultaneousSounds`. Bear in mind that the operating system only allows a maximum of 32 active audio instances at once, system-wide! In addition to your `Sound`s, this includes `Music`, other apps in the foreground, media players, and system sounds such as notifications. Think twice before raising it.

### Desktop

Use `setAudioConfig(simultaneousSources, 512, 9)`, where `simultaneousSources` is your desired maximum number of simultaneous audio sources. The other two parameters are the default values for `AudioDevice` buffer size and count. 

For applications still using the legacy LWJGL2 backend, set `audioDeviceSimultaneousSources`.

Increasing this value does not have ramifications in the same way that Android does.

### Other platforms

iOS and GWT can play a theoretically unlimited number of sounds at once without any configuration required.

## Multiple sounds cause freezes on Android
As stated on an [Audio](/wiki/audio/audio) topic the Android has many issues with audio in general. One of them, is that waiting for sound ID might take quite a lot of time. The sounds in libGDX play synchronously by default. It causes main loop to be frozen for significant time if you play a lot of sounds at once. This issue is especially noticeable on Android 10.

The solution is to make them play asynchronously. However, it will cause an inability to use `Sound`'s methods where ID is required. More info provided here - [Audio#audio-on-android](/wiki/audio/audio#audio-on-android)
