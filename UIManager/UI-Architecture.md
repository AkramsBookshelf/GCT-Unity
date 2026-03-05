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
## The MVC Pattern

The **Model–View–Controller (MVC)** pattern helps us divide responsibilities across the UI system. Instead of placing all functionality into one large script, each component plays a specific role.

This prevents the creation of **“God Objects”**—scripts that try to handle everything and quickly become difficult to debug or extend.

In our UI architecture, MVC divides the system into three main parts.

#

### The View 
The **View** is the visual representation of the UI.

The View’s responsibilities include:

-   **Accessing the UI Document:** Loads the UI structure defined in UXML.
-   **Reading the UXML layout:** Interprets the hierarchy of UI elements and ensures they appear correctly on screen.
-   **Finding UI elements:** Locates buttons components.
-   **Detecting user interactions:** Listens for clicks, drags, key presses, and other input events, and reports them to the Controller.
    
The View is _"dumb"_ by design. It knows that a button was clicked, but it doesn't know why. It simply hands a **Command Token **to the Controller.

This keeps the View focused purely on **presentation and input detection**.

# 

### The Controller 
The **Controller** acts as the middleman between the View and the rest of the game.

When the View detects a button click, it doesn’t just report _“Button X was pressed”_, it sends a **token**, a unique identifier that represents the player’s intended action. Each button in the UI is essentially a **token mapped to a specific command**, like `StartGame`, `Quit`, or `OpenInventory`.

The Controller’s responsibilities include:

-   **Listening to the View:** Receives interaction events like button clicks or slider adjustments.
-   **Interpreting command tokens:** Converts UI interactions into standardized tokens (e.g., `StartGame`, `Save`, `OpenInventory`) to represent player intent.
-   **Dispatching commands to the Model:** Sends the tokens to the UICommand system, letting the Model execute the actual game logic.
-   **Decoupling UI from game systems:** The Controller doesn’t directly manipulate the Scene Manager, Audio Manager, or other subsystems; it simply translates intent into commands.

Rather than executing game logic directly, the Controller acts as a **translator**, converting UI interactions into standardized tokens that the rest of the system can safely interpret and execute.

# 

## The Model 
In traditional MVC, the Model often serves as a simple data container, like a database or a set of properties. In our UI architecture, however, the **Model functions as a Service Layer**.

>[!NOTE]
> In our architecture, the **Model** specifically represents the **command logic** for all UI-driven actions. We refer to this class as **UICommandHandler** as it actually _handles_ the actions for that command. This also makes for a clear and descriptive class name.
>

Rather than just holding data, our Model is represented by a class that contains the **actual game logic**. It is responsible for executing actions triggered by UI interactions and coordinating with other systems when necessary.

The Model’s responsibilities include:
-   **Executing logic:** When the Controller sends a `StartGame` token, the Model handles the corresponding actions—changing the game state, loading the level scene, initializing systems, etc.
-   **Encapsulating implementation details:** The Model hides the complexity of the game systems from the UI. The Controller and View don’t need to know how a command is executed—they just send the token.
-   **Serving as the “source of truth”:** The Model maintains the authoritative logic and state for all UI-driven actions. Any changes or requests pass through the Model, ensuring consistency across the system.
    
By treating the Model as a Service, we keep the UI a **thin client**, a simple interface that sends requests to a powerful, independent engine. This separation makes the system easier to maintain, extend, and test.
 
 ---
## The Observer Pattern
In our planned UI architecture, the **Controller** acts as a translator, turning UI interactions into **tokens**, while the **Model (UICommandHandler)** handles the actual logic for those tokens. But what happens if there’s **no logic defined for a specific token**?

Hard-wiring the Controller directly to the Model would create a fragile connection: missing logic could break communication or cause errors.

This problem becomes even more critical when we want **other systems to respond** to UI actions. For example:

-   The **Start Game** button might need to hide the menu **and** tell the Scene Manager to switch scenes.
-   If the Controller were directly calling both, responsibilities would be tangled, making the system harder to maintain and extend.

We can solve these problems by implementing the **Observer Pattern**, which provides several key benefits:

1.  **Decoupling:** The Controller doesn’t need to know about any system that reacts to UI actions.
2.  **Flexibility:** New systems can subscribe to tokens without modifying existing code.
3.  **Safety:** Missing logic doesn’t break the flow; tokens with no listeners simply do nothing.
    
#

### Centralized Event Bus

In a typical Observer Pattern, each subject only notifies a fixed set of observers, often requiring the subject to hold references to each one. In a large UI system with dozens of tokens and multiple subsystems, this quickly becomes unmanageable.

By implementing a **centralized Event Bus**, we create a single hub where any system can **broadcast or listen for events**. This provides:

-   **Scalability:** New UI actions or systems can subscribe without modifying existing code.
-   **Flexibility:** Multiple systems can respond to the same event independently (e.g., UICommands runs game logic while AudioManager plays a sound).
-   **Decoupling:** The Controller doesn’t need to know about the subscribers, and subscribers don’t need to know about the Controller.
    
Essentially, the Event Bus extends the Observer Pattern to support a **dynamic, decoupled, and modular architecture**, which is critical for managing complex UI-driven interactions in a game.

In short, the **UI Event Bus** allows us to separate responsibilities, safely handle optional logic, and enable multiple systems to respond to player input in a clean, scalable way.

---

# The Command Pattern: Standardizing Intent
The **Command Pattern** is a behavioral design pattern that **encapsulates a request as an object**, separating the **invoker** (who calls the action) from the **receiver** (who executes it). This allows requests to be **parameterized, queued, logged, or undone** without the invoker needing to know the implementation details.

In our UI architecture, the **Controller** sends a specific **token** to represent the player’s intent. These tokens are **not the actual command logic**, just the **type of command** (e.g., StartGame, PauseGame, QuitGame). To avoid fragile string references, we implement this as an `enum`:

```csharp
UICommandType
```

Each entry in this enum represents a possible player action, such as:

-   `StartGame`
-   `LoadGame`
-   `SaveGame`
-   `Quit`
  
When a button is clicked, the UI sends the corresponding token. The **Model (UICommandHandler)** then interprets the token and performs the appropriate action.

For example:

-   `UICommandType.Quit` might be mapped to the logic that runs `Application.Quit();`
    

Using the Command Pattern in this way provides several advantages:

-   **Prevents fragile string comparisons**
-   **Keeps UI elements simple**
-   **Centralizes game logic**
-   **Makes debugging easier**

In a typical UI setup, a button "knows" too much; it directly calls a specific function. By using the Command Pattern, we **insert a layer of abstraction**: the button no longer says, “Run the Save function in the SaveManager script.” Instead, it says, “I am firing the SaveGame token.”

The UI does not need to know **how** a command works, only **which command** should be executed.

---
## The Adapter & Flyweight Patterns: Bridging UI Design and Code

So far, we have discussed how UI actions are represented as **tokens** using the Command Pattern. However, there is still an important challenge we must solve.

UI layouts in Unity are defined in **UXML files**, which identify elements using **string names**.

For example:
```xml
<Button name\="startButton" />  
<Button name\="quitButton" />
```

However, our architecture is based on **tokens**, not strings.
```csharp
UICommandType.StartGame  
UICommandType.Quit
```

This creates a mismatch between the **UI design layer** and the **code layer**.
-   Designers work with **string identifiers** in UXML.
-   Programmers work with **strongly typed enums** in code.
    
If we tried to rely directly on strings in our code, we would run into several problems:
-   Strings are **fragile** (typos break functionality).
-   Strings cannot be **checked by the compiler**.
-   String comparisons are **slower and harder to debug**.
    
To solve this, we introduce a **translation layer** between the UI and the code.
This is where the **Adapter Pattern** comes in.

#

### The Adapter Pattern: Translating UI Names into Tokens
The **Adapter Pattern** allows two incompatible systems to communicate by inserting a **translator** between them.

In our UI system:
| System         | Representation            |
| -------------- | ------------------------- |
| UXML UI Layout | `"startButton"`           |
| Game Logic     | `UICommandType.StartGame` |

To implement this, we can create a class named **UIMappingRegistry**, which acts as the **adapter** that converts between these two representations.

For example:
```csharp
"startButton"  →  UICommandType.StartGame  
"quitButton"   →  UICommandType.Quit
```

This means the UI can remain **designer-friendly**, while the code remains **type-safe and structured**.

The Controller never needs to worry about string comparisons; it simply asks the registry for the **token associated with a UI element**.

#

### The Flyweight Pattern: Centralizing Shared Mappings
Another issue we want to avoid is redundancy. Without a central mapping system, every UI controller might contain its own lookup logic:
```chsarp
if(buttonName \== "startButton")

// or

switch(buttonName)
````

This would lead to **repeated logic across many UI scripts**.

The **Flyweight Pattern** is used to reduce redundancy by **sharing common data instead of duplicating it**.
By implementing the **UIMappingRegistry**, it stores these mappings **once in a centralized location**, allowing every UI controller to reuse the same data.

This provides several benefits:
-   **Memory efficiency:** Only one mapping table exists.
-   **Consistency:** All UI controllers reference the same definitions.
-   **Maintainability:** Changes to mappings happen in a single place.
---

## From Architecture to Implementation

Now that we have established the **design patterns** guiding our UI architecture, we can begin translating this blueprint into actual code.

So far, we have defined how our system should behave:

-   **MVC** separates UI visuals, interaction handling, and game logic.
-   The **Command Pattern** standardizes player intent through tokens.
-   The **Observer Pattern** allows systems to react to UI events without tight coupling.
    -   The **Event Bus** provides a centralized communication channel for those events.
- **Adapter** translates UXML string identifiers into strongly typed command tokens.   
- **Flyweight** centralizes shared UI mappings so they can be reused across the system.
    
With this communication flow in place, we can now **map these responsibilities to concrete classes and components**. Each class in the system will play a specific role within the architecture we designed.

The following table outlines the core components we will implement and how they fit into the patterns discussed in this lesson.

| Class / Component                              | Type                    | Pattern Role                 | Purpose                                                                                           |
| ---------------------------------------------- | ----------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------- |
| **UICommandType**              | Structural              | Command (Token)              | These enums act as standardized **tokens** representing player intent.                            |
| **UIMappingRegistry**                          | Creational / Structural | Adapter / Flyweight          | Maps raw UXML string identifiers to enums, acting as the **translator between design and logic**. |
| **BaseUIView**                                 | Structural              | MVC (View)                | Provides the structure for finding UIElements and registering their native events.                |
| **MainMenuController** (and other controllers) | Behavioral              | MVC (Controller)                   | Bridges the View to the game logic by catching UI events and broadcasting tokens.                 |
| **UIEvents** (Event Bus)                       | Behavioral              | Observer          | Dispatches signals so senders (UI) do not need to know who receives them.                         |
| **UICommandHandler**                           | Behavioral              | MVC (Model/Service), Command (Invoker / Executor) | Executes the logic for actions such as **Start Game, Quit, and Save**.                            |

With the architecture defined, the next step is to **implement these classes step by step**, starting with the foundational components that allow UI elements to communicate through tokens and events.

Once these building blocks are in place, we will be able to construct a scalable UI system where menus, HUD elements, and settings panels can all operate within the same architecture.

--- 
## 🚩 Checkpoint

Having explored the **architecture and design patterns** behind our UI system, here are some key points to **keep in mind** before moving on to implementation:

-   **Separation of responsibilities (MVC):**  
    The **View detects input**, the **Controller interprets player intent**, and the **Model (UICommandHandler)** executes the actual game logic. Keeping these responsibilities separate prevents UI scripts from becoming overloaded.
    
-   **Tokens represent player intent:**  
    UI interactions are translated into **command tokens** (such as `StartGame` or `Quit`) rather than directly calling functions. This standardizes how the system interprets player actions.
    
-   **Enums improve safety and clarity:**  
    Using an `enum` for command tokens avoids fragile string comparisons, provides **compile-time validation**, and makes the system easier to debug and maintain.
    
-   **Observer pattern enables loose communication:**  
    Instead of directly calling other systems, the **Controller broadcasts tokens through the UI Event Bus**, allowing any interested system to respond independently.
    
-   **The Event Bus scales the Observer pattern:**  
    A centralized **UI Event Bus** allows multiple systems (UICommands, Scene Manager, Audio Manager, etc.) to react to the same event without knowing about each other.
    
-   **Commands separate intent from execution:**  
    The **Command Pattern** ensures that UI elements only express **what the player wants**, while the **UICommandHandler** determines **how that action is performed**.
    
-   **Adapters bridge UI design and code:**  
    The **UIMappingRegistry** translates **UXML string identifiers** into strongly typed command tokens, allowing designers and programmers to work with different representations safely.
    
-   **Flyweight reduces duplicated logic:**  
    By centralizing UI mappings in a shared registry, all controllers reuse the same data, ensuring **consistency and maintainability**.
    
-   **Architecture comes before implementation:**  
    Designing the communication flow first helps prevent tightly coupled systems and makes the UI easier to expand as the game grows.
    

With these principles in mind, we are now ready to **translate this architecture into concrete classes and begin implementing the system step by step.**
