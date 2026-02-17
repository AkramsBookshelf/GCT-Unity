# 📜UI Document 
When creating UI with Unity’s **UI Toolkit**, everything begins with a **UI Document**. You can think of a UI Document as the UI Toolkit equivalent of a **Canvas** in Unity’s older Canvas-based UI system (uGUI). It acts as the “container” that defines what UI exists, and it gets attached to a **GameObject in the scene** so Unity can render it at runtime.

## UI Document vs Canvas (uGUI)
In the Canvas-based system, the **Canvas and all UI elements are GameObjects** in the scene hierarchy. Buttons, text, images, panels; they all exist as objects with components like `RectTransform`, `Button`, and `TextMeshProUGUI`.

UI Toolkit works differently. Instead of building UI out of GameObjects, UI Toolkit UI is built using:
-   **UXML** → defines the UI structure
-   **USS** → defines the styling
-   **C#** → controls behavior
  
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

# ⚒️ Tutorial: Creating A New UI Document - Main Menu
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| UI Toolkit        | 10 minutes        | Unity, IDE |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Ensure you are on the **UIManager** branch
>   

---

### Step 1: Create A UI Document
1. Open your project in the Unity Editor
2. In the **Project** window create a new folder named **UI**
3. Inside **UI** folder right-click and choose **Create > UI Toolkit > UI Document** 
   - Name it: **MainMenu**
4. Double-click on the **MainMenu** UI Document from the **Project** window
   - This will open the document in the **UI Builder**

# 

### Step 2: UI Document Canvas Setup
1. With the UI Document Selected in the UI Builder Hierarchy, set the **Canvas Size** to **Match the Game View**

> [!NOTE]
> For the **Match the Game View** to render at the appropriate resolution, the **Game View** window must have the correct **Aspect Ratio** set.
>

2. To see the full UI Document in the UI Builder, set the **Viewport** to **Fit Viewport**

# 

### Step 3: Adding a Visual Element
All UI elements exist inside a **Visual Element**, which can be thought of as the _root container_ as it is the base for all ui elements.

1. From the **Library** panel in the **UI Builder** find the **Visual Element**
   - Drag and drop the element into the **Hierarchy**
  
# 

### Step 4: Adding a Game Title
The game title will be displayed as text for this example. The visual element for displaying text is a **Label**

1. From the **Library** panel in the **UI Builder** find the **Label**
   - Drag and drop the element into the **Hierarchy** nested in the **Visual Element**
2. With the **Label** selected in the **Inspector** window
   - Set the **Attributes → Text** property to **Game Title**
  
# 

### Step 5: Create a Button Container
Since we want to ensure the buttons are aligned with each other, we can group these in their own container (i.e., Visual Element). This _button container_ will be inside the _root container_. 

1. From the **Library** panel in the **UI Builder** find the **Visual Element**
   - Drag and drop the element into the **Hierarchy** under the pervious **Label** elment

2. Inside the **Visual Element** which acts as our _button container_ **add 3 buttons**

We want to be able to uniquely distinguish the buttons from eachother. We can do this by assigning an **ID**. 

3. With the first (top) button selected in the **Hierarchy** in the **Inspector** window
   - Set the ID to **playButton**
   - Set the **Attributes → Text** to **Play**

4. Repeat step 3 on the other two buttons:
   - Set the ID to **optionsButton** and text value to **Options**
   - Set the ID to **quitButton** and the text value to **Quit**
  







