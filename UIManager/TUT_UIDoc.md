# 📜UI Document 
When creating UI with Unity’s **UI Toolkit**, everything begins with a **UI Document**. You can think of a UI Document as the UI Toolkit equivalent of a **Canvas** in Unity’s older Canvas-based UI system (uGUI). It acts as the “container” that defines what UI exists, and it gets attached to a **GameObject in the scene** so Unity can render it at runtime.

## UI Document vs Canvas (uGUI)
In the Canvas-based system, the **Canvas and all UI elements are GameObjects** in the scene hierarchy. Buttons, text, images, panels; they all exist as objects with components like `RectTransform`, `Button`, and `TextMeshProUGUI`.

UI Toolkit works differently. Instead of building UI out of GameObjects, UI Toolkit UI is built using:
-   **UXML** → defines the UI structure
-   **USS** → defines the styling
-   **C#** → controls behavior
-   
The **UI Document component** is responsible for displaying the **UXML** layout when the game runs.

# 

### What is UXML?

UXML is Unity’s version of XML, designed specifically forthe  UI Toolkit, drawing inspiration from modern web UI, which is built using **HTML + CSS**. In many ways:
-   **UXML is similar to HTML**
-   **USS is similar to CSS**
    
The key difference is that **HTML has predefined tags** (`<div>`, `<button>`, `<h1>`, etc.), while **standard XML does not**; XML tags can be anything, as long as the structure is valid and the program reading the file understands what those tags mean.

UXML sits somewhere in the middle: it is **XML**, but Unity provides a set of **predefined tags** like:
-   `<VisualElement>`
-   `<Label>`
-   `<Button>`
    
These tags represent UI Toolkit elements and are recognized directly by Unity.

---

## UI Builder vs Writing UXML
Unity provides the **UI Builder**, which allows you to build UI visually through drag-and-drop.

This can be useful for:
-   Quickly experimenting with layout
-   Previewing element structure
-   Learning how UI Toolkit works
    
However, UI Builder can also be cumbersome because:
-   It relies heavily on inspector-style workflows
-   It may generate **inline styles**, which can clutter your UI setup
-   It often encourages styling too early
    

For this course, we’ll use the UI Builder only briefly, and we’ll focus primarily on writing UXML directly. This makes the UI:
-   Cleaner to read
-   Easier to version control
-   Faster to build once you understand the structure
    
> [!NOTE]
> A good starting point for learning UI Toolkit structure and concepts is Unity’s documentation:
> [https://docs.unity3d.com/2022.3/Documentation/Manual/UIElements.html](https://docs.unity3d.com/2022.3/Documentation/Manual/UIElements.html)
>

---


## Basic Main Menu
Before creating our first UI Document, we need a clear target. We’ll start with a **basic main menu**, containing:

-   A **game title**
-   Three buttons:
    -   **Play**
    -   **Options**
    -   **Quit**
        

This menu will be:
-   Centered on screen
-   Vertically stacked
-   Evenly spaced
    
This is a simple and reusable layout that can serve as a **starting template** for future projects and menus.

---

