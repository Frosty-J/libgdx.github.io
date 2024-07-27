---
title: Streaming music
---
For any sound that's longer than a few seconds it is preferable to stream it from disk instead of fully loading it into RAM. libGDX provides a Music interface that lets you do that.

To load a Music instance we can do the following:

```java
Music music = Gdx.audio.newMusic(Gdx.files.internal("my-music.mp3"));
```

This loads an MP3 file called `my-music.mp3` from the assets directory.

```java
music.dispose();
```

## Controlling playback

After loading the music, it is possible to play it, as well as pause or stop it. Pausing retains the music's position, whereas stopping it resets its position back to the start.

```java
music.play();
music.pause();
music.stop();
```

## Looping

It is common for video game music to be looped, so that the player isn't left sitting in silence after a few minutes. Looping can be enabled before or during playback.  

```java
music.setLooping(true);
```

This will create a perfect loop on desktop. If it isn't perfect, inspect your audio file in an audio editor such as Audacity. Some audio formats such as MP3 will introduce a gap &ndash; when in doubt, use Ogg.

Other backends may be less fortunate. Possible solutions:

* On Android and iOS, use the [gdx-miniaudio](https://github.com/rednblackgames/gdx-miniaudio) library by Red & Black Games.
* On GWT, consider an alternative audio backend such as [howler.js](https://howlerjs.com/). This is used by the GWT alternative [gdx-teavm](https://github.com/xpenatan/gdx-teavm) as seen in [this commit](https://github.com/xpenatan/gdx-teavm/commit/59b24e57d8d637af70383aedf5864b39d218bca1).
* A simpler, albeit hackier solution for GWT is to loop the music in the file itself for longer than the typical play session and [stream it](#online-streaming). This requires the user to have a reliable internet connection.

## Volume

The music's volume can be set with a range of 0 (silent) to 1 (full volume).

```java
music.setVolume(0.5f); // Half volume
```

## Seeking

It is possible to seek to a specific point in the music.

```java
music.setPosition(12.345f); // Seek 12345 milliseconds in
```

## On completion

An `OnCompletion` listener can be set to receive a callback when the music has finished. In this example, the music is disposed and the screen is changed once the music has finished:

```java
music.setOnCompletionListener(music -> {
    music.dispose();
    myGame.setScreen(myOtherScreen);
});
```

In some cases with the GWT backend, the end of the file can be reached without playback officially ending. In which case, the callback will never trigger.

Using an `OnCompletion` listener to queue multiple files together, such as to play the song intro then loop the main part of it, can introduce a slight gap. Other audio backends such as [gdx-miniaudio](https://github.com/rednblackgames/gdx-miniaudio) may be required to chain them together without a gap.

## Panning

It is possible to pan the music. A value of `-1f` pans all the way left, meaning the music only comes out the left speaker. `0` is centered. `1f` pans all the way right.

```java
music.setPan(0.5f, 0.67f); // Pan half-right, two-thirds volume
```

**Note:** The desktop backends only support panning mono files.

## Querying state

Various attributes of the music object can be queried using getter methods.

```java
boolean playing = music.isPlaying();
boolean looping = music.isLooping();
float volume = music.getVolume();
float position = music.getPosition();
```

* `getPosition()` can be useful for syncing up graphics with the audio position, such as for cutscenes or rhythm games.
* `isPlaying()` is useful on GWT for detecting if the user has paused playback via their system's media controls.

## Online streaming

For web-based games, downloading music files can take a significant length of time. To remedy this, it is possible to stream the music over the internet, just like on other backends it would be streamed from disk.

To do so, exclude your music files from being preloaded. See [Speeding up preload process](/wiki/html5-backend-and-gwt-specifics#speeding-up-preload-process) for details on this. Then play them as normal. Do not use an `AssetManager` for this as that will download the music file in its entirety.

## Use responsibly!

`Music` instances are heavier than `Sound` on some backends, such as Android. Normally, you should not have more than a few playing at the same time. Dispose instances that are no longer needed, to free up resources.

```java
music.dispose();
```
