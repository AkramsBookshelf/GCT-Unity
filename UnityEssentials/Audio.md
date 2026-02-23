## Audio Import Settings

When you import an audio file into Unity, the **Audio Clip Import Settings** determine how that sound is stored in memory, compressed, and played at runtime.

These settings directly affect:
-   **Memory usage**
-   **CPU performance**
-   **Load times**
-   **Audio quality**
    

Choosing the correct settings depends on **how the sound will be used** in your game.

### Inspector Properties (General Settings)

When you select an audio file in the Project window, Unity displays its import settings in the Inspector.

- **Force To Mono**: converts a multi-channel (stereo) audio clip into a single-channel (mono) clip before packaging.
  -   Reduces file size.
  -   Useful for 3D positional sound effects (footsteps, impacts).
  -   Not recommended for music that relies on stereo width.
    
- **Normalize**: If Force To Mono is enabled, Normalize adjusts the volume so the mixed-down audio maintains consistent loudness.

- **Load In Background**: Loads the audio clip asynchronously instead of blocking the main thread when the scene starts.
  -   Prevents frame hitches during scene load. 
  -   Useful for large audio files.
  -   Unity delays playback requests until loading is complete.
    
- **Ambisonic**: Used only for Ambisonic-encoded audio, which represents a 360° sound field.
  -   Designed for XR / VR / 360 video experiences.
  -   Not needed for standard games.
    
#### Platform-Specific Settings
Unity allows you to configure audio differently for PC, Mobile, WebGL, etc. These settings are critical for performance optimization.

- **Load Type**: Load Type determines **when and how** audio is decompressed and loaded into memory.
  
- **Decompress On Load**: Audio is fully decompressed into memory when loaded.
  -   Best for **short sound effects**.
  -   Uses significantly more memory.
  -   Avoid for large files like music.

> [!TIP]
> Decompress On Load is best used for short sound clips such as UI clicks, short impacts, pickup sounds.

## Compressed In Memory

Audio stays compressed in RAM and is decompressed during playback.

-   Saves memory.
    
-   Slight CPU cost during playback.
    
-   Good balance for medium-length sounds.
    

👉 Use this for: ambient loops, longer sound effects.

## Streaming

Audio is streamed from disk and decoded in small chunks during playback.

-   Uses very little memory.
    
-   Slight overhead (~200KB).
    
-   Best for large files.
    

👉 Use this for: **background music** and long ambient tracks.

# Compression Format

This determines how the audio is stored at runtime.

## PCM (Uncompressed)

-   Highest audio quality.
    
-   Largest file size.
    
-   Very low CPU usage.
    

👉 Best for very short sound effects where quality matters and file size is small.
