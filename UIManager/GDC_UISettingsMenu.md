# 🏹 Game Design Challenge: Extending the UI System

> By: Akram Taghavi-Burris | © 2026

We’ve already learned how to set up a **robust UI system** with buttons, using **MVC, Command, and Observer patterns**, an **Event Bus**, and a **UIMappingRegistry** to map UXML buttons to command tokens. Students have implemented a **Main Menu Controller**, handled **StartGame** and **QuitGame** commands, and centralized logic in the **UICommandHandler**.

Now, imagine we’re on the job: the design team wants the game to support **player-adjustable settings**, like audio volume, graphics options, and toggles for fullscreen or VSync.

Our task is to **extend the current UI system** to support **sliders and toggles**, using the same design patterns, while keeping the architecture decoupled, scalable, and maintainable.

---

## 📋 Feature Brief: Settings UI System

**Project:** _Camp Craft_  
**To:** Programming Team  
**From:** Game Designer

### 🎯 Design Intent
The design team would like to extend the current UI system to handle **UI sliders and toggle inputs** that control the volume settings. The UX Designer has already provided the layout for the **Options Menu** where these inputs are housed.

While we are initially implementing **Master Volume** (slider) and **Mute All** (toggle), the system should be designed so that **future sliders and toggles** can be added and behave consistently.

Much like the buttons, these new elements should:
-   Use **tokens** to represent slider or toggle intent, similar to button commands.
-   Use the **Event Bus** so multiple systems can respond independently to changes.
-   Maintain a **clean separation of concerns** between View, Controller, and Model.
-   Support **centralized mapping** between UI element names and their logic, minimizing duplication.

#

### 🔹 Core Concepts

1.  **Slider vs Toggle Behavior**
    -   **UI Sliders** pass a `float` value, typically from **0-100** or a defined range.
      -   **UI Toggles** pass a `boolean` value (`true`/`false`).

> [!TIP]
>  Consider creating **two methods with the same name**, one taking a `float`, one taking a `bool ', to be triggered when these UI elements _values_ are changed.
>

        
2.  **Tokens for UI Settings**
    -   Similar to how buttons use **`UICommandType`**, sliders and toggles should use a **token** to represent the intent (the type of **setting** being changed).
    -  Token names should be **explicit but simple**, avoid names that are too tied to the UI or too ambiguous.

| Good Examples | Too UI-Specific    | Too Ambiguous    |
| ------------- | ------------------ | ---------------- |
| `MusicVolume` | `MusicSlider`      | `Volume`         |
| `SFXVolume`   | `SFXSlider`        | `AudioSetting`   |
| `Fullscreen`  | `FullscreenToggle` | `DisplaySetting` |
    
  
 3. **Mapping and Registration**
    -   Tokens will need to be mapped to their corresponding **UXML `name` property** string values.
     -   The **`BaseUIView`** should create additional **dictionaries** for sliders and toggles and register the appropriate callbacks for each element type.
  
> [!IMPROTANT]
> Be sure to check the **`name` property** of the UI elements in the **OptionsMenu UXML document** when creating your mappings.
>

      
4.  **Event Bus Integration**
    -   Menu controllers should **invoke settings actions** through the **`UIEvents` system**.
    -   This enables multiple systems (for example, audio or UI feedback systems) to **respond independently** to setting changes.
        
        
5.  **Audio Handling**
    -   To manipulate the game volume through the options menu, implement these **controller methods**:
  
```csharp
public static void MasterVolumeChange(float val) 
{
    AudioListener.volume = val;
    Debug.Log($"[Audio] Master Volume is now: {val}");
}

public static void MuteAll(bool val) 
{
    AudioListener.pause = val;
    Debug.Log($"[Audio] AudioListener Paused: {val}");
}
```



