# 📜 UI Dynamics
> By: Akram Taghavi-Burris | © 2026

The Unity UI Toolkit is a powerful system for creating flexible, scalable UI interfaces. While much of your UI can be built statically with UXML and USS, dynamic updates through C# code (or Visual Scripting) are essential for interactive elements and game systems that require real-time changes.


#

## Static vs. Dynamic Content

UI elements can be categorized as either **static** (unchanging) or **dynamic** (changing during gameplay). Identifying which elements fit into each category is essential for designing clear, efficient interfaces.

# 

### **Static Elements**

Static elements are those that remain constant throughout the game. These might include:
-   Title screens
-   Navigation buttons
-   Background images

Since static elements don’t require frequent updates, they can be built entirely in UXML and USS, improving performance and reducing code complexity.

# 

### **Dynamic Elements**

Dynamic elements are those that update during gameplay. Examples include:
-   Player health
-   Score counters
-   Inventory icons
-   Pop-up notifications

Because these elements must respond to game events, they are typically updated via C# code or Visual Scripting.

 #

### **Dynamic UI Planning**

When designing dynamic UI, it’s important to consider:

-   **What Needs to Change:**  
    Will you be updating text values, swapping images, or toggling visibility? For instance:
    
    -   A player’s health might be shown as a percentage in a **text label** (`Player Health: 75%`).
    -   Alternatively, the health could be visualized as three heart icons that disappear one by one. This would require a visual element with a **background-image** and perhaps altering the element's **display** (visibility) value.
-   **How Often It Will Change:**  
    Frequent updates (such as a timer or ammo count) should be carefully optimized to avoid performance issues. Only update UI elements when values change, rather than every frame.
    
    -   Values that get reset may need to be updated in the `Start` method.
    -   Values that update by frame, like a countdown time, should be called in the `Update`
    -   Values that happen at specific instances, such as collecting an item, should trigger a `CustomEvent`.
-   **Visual Communication:**  
    Choose the format that best conveys information to the player. Numeric data might work well in some cases, while visual indicators like icons or animations may be more intuitive for fast-paced gameplay.
    
-   **Guideline:** Start by designing elements as static whenever possible. Only introduce dynamic content when an element requires updates based on player actions or game events. This approach ensures better performance and simplifies your UI logic.
    
---

## Manipulating UI Content

The Unity UI Toolkit is built on a flexible, hierarchy-based structure where every UI element is a **VisualElement**, the fundamental building block of the system. Understanding this foundation is key to manipulating UI effectively with code.

#
 
### UI VisualElements

At the core of the UI Toolkit is the **VisualElement** class, which acts as a base type for all UI elements. Whether you're working with labels, buttons, images, or containers, each is a specialized type derived from **VisualElement**.

-   **Labels** — Display text content.
-   **Buttons** — Provide interactive elements with click behavior.
-   **Images** — Display visual assets like icons or backgrounds.
-   **Containers** — Group and organize child elements.

Since all UI elements share this common base, they inherit core properties like size, position, and style, making it easier to manipulate elements consistently.

### Querying Elements

To modify existing UI elements dynamically, you’ll need to **query** them, that is, locate them within the visual hierarchy.

-   **What it does:** Identifies specific UI elements based on their name or type.
-   **Why it's important:** Ensures your code can access and modify UI elements reliably, even in complex layouts.

For example, querying is often used to:

-   Find labels to update text values.
-   Locate containers to add or remove child elements.
-   Identify buttons to assign interactive behaviors.

#

### Common UI Properties

When updating UI dynamically, some properties are modified more frequently than others:

-   **`text`** — Used for labels, buttons, and other text-based elements.
-   **`style`** — Controls visual appearance, such as color, size, or background images.
-   **`display`** — Toggles the visibility of an element, useful for hiding menus, pop-ups, or notifications.

By understanding which properties to target, you can make efficient updates without overcomplicating your code.

---

## Handling Events

Events in the UI Toolkit are designed to respond to player interactions such as clicks, hovers, or text input.

-   **What it does:** Links game logic to UI elements by responding to user actions.
-   **Why it's important:** Allows your UI to react to player input, providing essential functionality for buttons, menus, and interactive displays.

Instead of relying on Unity's traditional `OnClick()` method, UI Toolkit events are based on a **callback system**, where specific actions are triggered when an event occurs.

Common event types include:

-   **ClickEvent** — Triggered when a button or clickable element is pressed.
-   **ChangeEvent** — Used for updating sliders, toggles, or dropdown selections.
-   **FocusEvent/BlurEvent** — Manages focus behavior for input fields.

By effectively combining **querying**, **property updates**, and **event handling**, you can create dynamic, responsive interfaces that adapt to the player’s actions.
