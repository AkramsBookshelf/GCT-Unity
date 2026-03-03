# 📜 Dynamic UI Controller
> By: Akram Taghavi-Burris | © 2026

In our previous implementation, we created a `MainMenuController` that:
-   Queried specific buttons by name
-   Stored them in individual fields
-   Subscribed to each button’s `clicked` event
-   Wrote a dedicated method for each button

## Designing for Scale 

This approach works well for small menus. However, as projects grow, several limitations begin to appear:
-   Every new button requires a new field.
-   Every new button requires a manual subscription code.
-   Every menu requires its own controller with repetitive setup logic.
-   Controllers become tightly structured around specific UI layouts.

#### ❌ This system does not scale efficiently.


To design for scalability, we need a UI controller that: 
-   Automatically discovers buttons
-   Registers them dynamically
-   Routes interactions based on button identity
-   Allows behavior to be defined separately from UI structure
    
> [!TIP]
> This is where abstraction becomes valuable.
>

---
## Base Button Controller 
In the original, `MainMenuController` had the following responsibilities:
-   Finds all **Buttons** inside a UXML document
-   Registers both **mouse** and **keyboard/gamepad** callbacks
-   Defining what method runs when the interacted with.

While the methods that run on each button are different, the querying of the buttons and registering events will follow the same process for any UI document whose buttons we need to control.
Instead of writing repetitive button registration logic in every UI script, we centralize the system here, but introducing a **BaseButtonController**; an **abstract foundation class** that automatically:
This class will: 
-   Finds all **Buttons** inside a UXML document
-   Store buttons in a **dictionary**
-   Registers both **mouse** and **keyboard/gamepad** callbacks
-  Routes all button presses through a single method

# 

### ⚙️ Understanding the Architecture
Implementing the **BaseButtonController** class provides a **loose separation between UI mechanics and behavior**, allowing derived classes to focus on defining interactions without rewriting the discovery logic.

This controller also follows three important design goals:
1.  **Automatic Discovery** – We do not manually drag button references.
2.  **Centralized Event Handling** – All button events pass through one method.
3.  **Extensibility** – Concrete UI controllers define behavior, not the base class.


This is an example of **Template Method Pattern** behavior. The Template Method pattern is a behavioral design pattern used in object-oriented programming. It defines the skeleton of an algorithm in a superclass while allowing subclasses to override specific steps without changing the overall structure of the algorithm.
-   The base class defines the workflow.
-   The child class fills in the specific action.

---

## Command Pattern
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
