# 📜 Dynamic UI Controller
> By: Akram Taghavi-Burris | © 2026

In our previous implementation, we created a `MainMenuController` that:
-   Queried specific buttons by name
-   Stored them in individual fields
-   Subscribed to each button’s `clicked` event
-   Wrote a dedicated method for each button
    
This approach works well for small menus. However, as projects grow, several limitations begin to appear:
-   Every new button requires a new field.
-   Every new button requires a manual subscription code.
-   Every menu requires its own controller with repetitive setup logic.
-   Controllers become tightly structured around specific UI layouts.

#### ❌ This system does not scale efficiently.

## Designing for Scale 
To design for scalability, we need a UI controller that: 
-   Automatically discovers buttons
-   Registers them dynamically
-   Routes interactions based on button identity
-   Allows behavior to be defined separately from UI structure
    
> [!TIP]
> This is where abstraction becomes valuable.
>

### Base Button Controller 
Thinking about our UI system, we can identify two responsibilities:
1.  **Finding and registering UI elements**
2.  **Defining what happens when they are interacted with**
    
In the original `MainMenuController`, both responsibilities were handled in one class. By introducing a base class (`BaseButtonController`), we **centralize the common logic for querying buttons and registering events**, reducing repetition across multiple menus. This also provides a **loose separation between UI mechanics and behavior**, allowing derived classes to focus on defining interactions without rewriting the discovery logic.

### Command Pattern
Once the base class handles discovery and event registration, we still need a way to define what happens when a button is clicked. This is where the **Command Pattern** comes in.

The **Command Pattern** is a design principle that encapsulates a request or action as an object, allowing you to decouple the caller (button) from the implementation (game logic).

In our UI system:
-   The **button** only announces that it was clicked.
-   The **base controller** only receives the event and routes it.
-   The **derived class** defines **commands** that execute the desired behavior for each button.

For example, we can store commands in a dictionary:

```csharp
private Dictionary<string, System.Action> _buttonActions;
```

In this instance: 
-   The **key** is the button’s name.
-   The **value** is an action (method) that executes when the button is clicked.
    
When the event fires:

```csharp
OnClickButton(string buttonName)
```

The system looks up the corresponding action and invokes it.
This approach has several benefits:
-   **Decoupling**: The UI element does not need to know about gameplay systems.
-   **Scalability**: Adding a new button is as simple as adding a new entry in the dictionary.
-   **Reusability**: Any menu can inherit from the base controller without duplicating query logic.
-   **Maintainability**: Behavior changes can be made in the dictionary without touching the base class.
    
In essence, combining a **base controller** with the **Command Pattern** creates a **flexible, data-driven UI architecture** that scales as menus grow more complex.
