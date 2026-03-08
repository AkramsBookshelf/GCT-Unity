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
    -  Token names should be **explicit but simple**, avoid names that are too **UI-specific** or **ambiguous**.

| Good Examples | Too UI-Specific    | Too Ambiguous    |
| ------------- | ------------------ | ---------------- |
| `MusicVolume` | `MusicSlider`      | `Volume`         |
| `SFXVolume`   | `SFXSlider`        | `AudioSetting`   |
| `Fullscreen`  | `FullscreenToggle` | `DisplaySetting` |
    
  
 3. **Mapping and Registration**
    -   Tokens will need to be mapped to their corresponding **UXML `name` property** string values.
    -   The **`BaseUIView`** should create additional **dictionaries** for sliders and toggles and register the appropriate callbacks for each element type.
    
  
> [!IMPORTANT]
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

---

## 🔎 Architectural Analysis: Extending the UI System

Now that we understand the designer’s intent, we need to determine **how the existing system can be extended** to support sliders and toggles.

Before implementing anything new, we should first **review the architecture we already have** and consider how the new UI elements can integrate with it. As system developers, our goal is not to rebuild the UI system from scratch, but to **expand it while maintaining its current design patterns and structure**.

In particular, consider the following questions:

-   What parts of the **current UI system** already support this type of interaction?
-   Which classes will need to be **modified or extended**?
-   What **new components or tokens** might be needed to represent slider and toggle input?
-   How will these inputs interact with the **Event Bus** and existing handlers?

The following table outlines the core components we have already implemented: 

| Class / Component                              | Type                    | Pattern Role                 | Purpose                                                                                           |
| ---------------------------------------------- | ----------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------- |
| **UICommandType**              | Structural              | Command (Token)              | These enums act as standardized **tokens** representing player intent.                            |
| **UIMappingRegistry**                          | Creational / Structural | Adapter / Flyweight          | Maps raw UXML string identifiers to enums, acting as the **translator between design and logic**. |
| **BaseUIView**                                 | Structural              | MVC (View)                | Provides the structure for finding UIElements and registering their native events.                |
| **MainMenuController** (and other controllers) | Behavioral              | MVC (Controller)                   | Bridges the View to the game logic by catching UI events and broadcasting tokens.                 |
| **UIEvents** (Event Bus)                       | Behavioral              | Observer          | Dispatches signals so senders (UI) do not need to know who receives them.                         |
| **UICommandHandler**                           | Behavioral              | MVC (Model/Service), Command (Invoker / Executor) | Executes the logic for actions such as **Start Game, Quit, and Save**.                            |

# 

## 📝 ASSIGNMENT: Extend the UI System
Using the **design intent**, **core concepts**, and **existing architecture**, your task is to extend the UI system so that the **Options Menu sliders and toggles function correctly**.

The UX designer has already provided the **Options Menu layout**, including a **slider** and a **toggle** related to audio settings. Your responsibility is to determine **how these inputs should be integrated into the existing UI architecture**.

### Key Deliverables
#### GitHub Issue
To simulate a professional development workflow, you will use **GitHub Issues** for documenting your design decisions before coding.
Follow the steps below: 

1.  **Create a new Issue** in your repository titled:  `Implementing UI Settings`
        
2.  **In the Issue description**, briefly state the goal of the task (extending the UI system to handle sliders and toggles).
        
3.  **As a comment on the Issue**, provide your **Architecture Documentation**:
    -   **Updated [Architecture Table](#-architectural-analysis-extending-the-ui-system)** _(above)_
        -   Include **new classes, enums, or systems** introduced
        -   Mark **existing classes that were modified**
        -   Indicate the **pattern role** of each component
                
    -   **Class Modifications Explanation**
        -   Which existing classes do you modify?
        -   What changes were made
        -   Why were these changes necessary to support slider and toggle behavior
                
    -   **Architectural Justification**
        -   Demonstrate how your system preserves:
            -   **Token-based intent**
            -   **MVC separation**
            -   **Event-driven communication**
                    
    4.  This comment will serve as your **design record**, showing that you **planned the system architecture** before implementation, as would be done in a professional team.
        
    > \[!TIP\]  
    > Think of this as a **“design proposal”**: your teammates (or instructors) should be able to understand your architectural choices just from this comment.
    >

#### Implementation & Playtesting
Once your architecture is documented:
1.  **Implement the new system** according to your plan.
2.  **Playtest** all sliders and toggles to ensure proper behavior.
3.  Use the same GitHub Issue to **report progress, and document fixes**.
4.  **Close the Issue only after** your implementation works as intended and playtesting confirms the system behaves correctly.

#

#### Implementation & Playtesting
- Once your architecture is documented:
1.  **Implement the new system** according to your plan.
2.  **Playtest** all sliders and toggles to ensure proper behavior.
3.  Use the same GitHub Issue to **report progress, and document fixes**.
4.  **Close the Issue only after** your implementation works as intended and playtesting confirms the system behaves correctly.

#

### 🐞 Troubleshooting Checklist: UI Settings Implementation

-   **Sliders/Toggles don’t trigger any action** →  
    Check that the **BaseUIView** registered the UI element callbacks correctly and that the element’s **name property matches your mapping**.
    
-   **UISettingsType token not firing** →  
    Confirm that each slider or toggle has a **corresponding token** in `UISettingsType` and that it is **mapped properly** in your registry or handler.
    
-   **Event Bus not broadcasting** →  
    Verify that your **UISettingsHandler** invokes `UIEvents` correctly, and that any listeners are **subscribed before events are fired**.
    
-   **Audio changes don’t apply** →  
    Ensure you are **passing the correct value type**:
    -   Sliders → `float`
    -   Toggles → `bool`  
        Also, check that `MasterVolumeChange(float val)` and `MuteAll(bool val)` are called at the right place in your handler or controller.
        
-   **Existing buttons stopped working** →  
    Check that modifications to **BaseUIView**, **UIEvents**, or the **UIMappingRegistry** didn’t break button registration. Ensure that new dictionaries for sliders/toggles don’t interfere with existing mappings.
    
-   **Duplicate events or multiple triggers** →  
    Confirm that **each UI element registers only once**, and that **callback subscriptions aren’t duplicated** in `BaseUIView` or controllers.
    
-   **Incorrect UI token mapping** →  
    Double-check that the **UXML element name** exactly matches the **mapping** to your `UISettingsType` token. Watch for typos, spacing, or case mismatches.
    
-   **Handler methods not executed** →  
    Make sure that the **UISettingsHandler** has the proper **public methods** for each token, and that the **Event Bus correctly references these methods**.
    
-   **Changes not visible during Playtest** →  
    Check **scene references** and ensure the Options Menu is active during playtesting. Verify **AudioListener** is present and not paused unintentionally.

### 💾 Save & Commit
- Ensure that the **GameManager** branch is active 
-   Save scripts and scenes often.
-   Commit after each completed step (e.g., `_feat: Added MainMenu, Playing, Paused, GameOver states_`).
-   Push to GitHub after major features or at the end of the day.

  
  
