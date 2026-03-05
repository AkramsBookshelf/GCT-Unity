# 📜 UI Architecture & Design Patterns: Component-Level Blueprint
> By: Akram Taghavi-Burris | © 2026

UI is an integral part of any modern game. From health bars to ammo counters, dialogue boxes and inventory screens, crafting menus to pop-up notifications, and main menus. Each of these elements looks different, behaves differently, and interacts with the player in unique ways, but many of them also share common behaviors: they need to appear or disappear, respond to clicks or key presses, and update dynamically based on the game state.

Without a clear plan, managing all these UI elements can quickly become chaotic. Scripts get tangled, buttons stop working, and small changes can break unrelated systems. That’s why, before writing a single line of code, we need a blueprint: a structured approach that defines how UI elements communicate, react, and execute logic.

In this lesson, we’ll focus on individual UI components and their architecture, laying the foundation for the next step: a comprehensive UI Manager that orchestrates everything.

---

## Design Patterns 
When designing a UI system for a game, we face a few recurring challenges:

1.  **Many elements, many behaviors:** Buttons, sliders, menus, and HUD elements all behave differently but often share some common logic, like showing, hiding, or updating dynamically.
    
2.  **Cross-system communication:** A UI element might need to trigger actions in unrelated systems, such as the Audio Manager, Scene Loader, or Save System. Hard-coding references quickly becomes fragile.
    
3.  **Predictable player intent:** We need a way to handle user actions (like Start, Quit, or Save) consistently without scattering logic across scripts.
    
4.  **Flexible execution:** Some UI elements, like Settings sliders, need different internal behavior depending on what they control (volume vs. resolution vs. graphics quality).
    

To address these challenges, we carefully chose four design patterns that complement each other:
-   **MVC (Model–View–Controller):** Separates visuals, input handling, and game logic so each part can evolve independently.
-   **Observer:** Let's systems listen to UI events without creating direct dependencies, keeping communication loose and scalable.
-   **Command:** Encapsulates player actions as tokens, providing a predictable and centralized way to execute logic.
-   **Strategy:** Allows different behaviors to be plugged into the same UI element, keeping components flexible and reusable.
  
By combining these patterns, we create a **decoupled, maintainable UI architecture** where elements can grow in complexity without breaking the system.

--- 
## The MVC Pattern: Who Does What?
The **Model–View–Controller (MVC)** pattern helps us divide responsibilities across the UI system. Instead of placing all functionality into one large script, each component plays a specific role.

This prevents the creation of **“God Objects”**—scripts that try to handle everything and quickly become difficult to debug or extend.

In our UI architecture, MVC divides the system into three main parts.

#

### The View 
The **View** is the visual representation of the UI.

It is responsible for:
-   Accessing the **UI Document**
-   Reading the **UXML layout**
-   Finding UI elements like buttons and sliders
-   Detecting user interactions
    
For example, if a button is clicked, the View simply reports that the button was pressed.

Importantly, the View **does not know why the button exists** or what should happen next. It only knows that the interaction occurred.

This keeps the View focused purely on **presentation and input detection**.

# 

### The Controller – _MainMenuController_

The **Controller** acts as the middleman between the View and the rest of the game.

When the View detects a button click, it sends that information to the Controller. The Controller then examines the **Token** (the identifier assigned to that UI element) and determines what action the player intends to perform.

The Controller’s responsibilities include:

-   Listening to the View
    
-   Interpreting button tokens
    
-   Dispatching events to other systems
    

Instead of executing game logic directly, the Controller **communicates the player’s intent** to the rest of the system.





























| Class / Component                     | Category                 | Pattern Role              | Purpose |
|--------------------------------------|--------------------------|---------------------------|---------|
| UICommandType / UISettingType       | Structural               | Command (The Token)       | These enums act as standardized **tokens** that represent an intent. |
| UIMappingRegistry                   | Creational / Structural  | Flyweight / Adapter       | Maps raw UXML strings to enums. Acts as the **translator between design and logic**. |
| BaseUIView                          | Structural               | View (Base)               | Provides the structure for finding UIElements and registering their native events. |
| MainMenuController (Specific Controllers) | Behavioral        | Controller                | Bridges the View to the game logic by catching UI events and firing bus events. |
| UIEvents (The Bus)                  | Behavioral               | Observer / Mediator       | Dispatches signals so the sender (UI) does not need to know who the receiver (Logic) is. |
| UICommands (Static Class)           | Behavioral               | Command (The Invoker)     | Executes the actual logic for discrete actions such as **Start, Quit, and Save**. |
| UISettingsHandler                   | Behavioral               | Command / Strategy        | Executes the logic for state changes such as **volume, toggles, and graphics settings**. |
| PlayerPrefs / SaveSystem            | Structural               | Model                     | Holds the actual data state that the settings handler modifies. |
