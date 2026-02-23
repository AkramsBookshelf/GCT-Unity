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
    -  _Decompress On Load_: Audio is fully decompressed into memory when loaded. Best used for short sound clips such as UI clicks, short impacts, and pickup sounds.
    -  _ Compressed In Memory_: Audio stays compressed in RAM and is decompressed during playback. Best for ambient loops, longer sound effects.
    -  _Streaming_: Audio is streamed from disk and decoded in small chunks during playback. Best to set background music and long ambient tracks.

- **Compression Format**: This determines how the audio is stored at runtime. Options include: 
    - _PCM_ - is uncompressed sound and is best used for short sound effects, and for higher quality audio at the expense of larger file sizes.
    - _ADPCM_ - is a compressed format (~3.5x smaller than PCM) and best used in sounds that have a lot of noise and play in large quantities, such as footsteps, impacts, and weapons.
    - _Vorbis_ - is the highest compression, creating a smaller file size, but it could affect the quality of audio. The quality can be adjusted to a specific compression/quality ratio. best used for medium-length sound effects and music.

- **Sample Rate Setting**: Controls how many audio samples per second are stored.
  -  _Preserve Sample Rate_ - Keeps the original sample rate.
  -  _Optimize Sample Rate_ - Unity analyzes the clip and reduces the sample rate automatically if possible.
  -  _Override Sample Rate_ - Manually lowers the sample rate to reduce file size (may reduce quality).
 
> [!TIP]
> In most cases, **Optimize Sample Rate** is selected, unless quality loss is aparent. 
> 

--- 
