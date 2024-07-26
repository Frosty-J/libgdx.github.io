---
title: Audio formats
---
# DRAFT DRAFT DRAFT

There are various audio formats that can be used for libGDX games.

For simplicity's sake, this page refers to them by their common name, whether that is the name of the container, file extension, or codec.  

## Compatibility table

| Platform | MP3 | Vorbis | WAV | Opus | HE-AAC | FLAC |
|----------|-----|--------|-----|------|--------|------|
| Desktop  | ✔️  | Y      | Y   | ❌    | ❌      | **   |
| Android  | Y   | Y      | Y   | Y    | Y      | Y    |
| iOS      | Y   | N      | ?   | ?    | Y      | ?    |
| Web*     | Y   | Y      | Y   | Y    | Y      | Y    |

⚠️

&ast; "Web" refers to the likes of Google Chrome and Mozilla Firefox. For Safari on iOS, refer to the iOS column.

&ast;&ast; Supported via third-party [TuningFork](https://github.com/Hangman/TuningFork) library.

On Android, Opus audio requires Android 5 Lollipop (API 21).

## Pros and cons

### MP3

A classic format, widely supported.

### Ogg Vorbis

Works on all platforms except iOS.

### PCM WAV

Provides lossless, uncompressed audio. Because of its large size, this should be avoided for music and other long pieces of audio. It may be appropriate for short sound effects, as they can be copied into memory without decompression.

WAV files can contain other types of encoding, such as IMA ADPCM compression, but these are not widely supported by libGDX's targets.

### Ogg Opus

Provides excellent sound quality at low bitrates. It is commonly used for voice recordings, but is also suitable for more complex audio such as music.

Like its Ogg Vorbis cousin, Opus supports perfect looping.

### HE-AAC

Like Ogg Opus, this codec achieves remarkable efficiency. However, it is proprietary and can add a gap.

As an example of how low it can go, many DAB+ radio stations broadcast music in stereo at 24kbps. Please don't follow in their footsteps.

### FLAC

Like PCM WAV, this is a lossless audio format. But with a layer of compression, in the same vein as PNG.

## Per-platform formats

It is possible to ship different formats per platform. For example, you might include Ogg Opus with the Android version of your game, MP3 with iOS, and Vorbis with desktop.

```gradle
todo
```

For GWT, you can try playing a Vorbis file to see if it works. Or detect it somehow. GwtApplicationConfiguration.isSafari or whatever it is has problems.
