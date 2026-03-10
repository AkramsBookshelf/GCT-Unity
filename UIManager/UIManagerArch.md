# 📜 UI Manager Architecture

> By: Akram Taghavi-Burris | © 2026

In the previous lesson, we designed the **communication architecture for our UI system**. Using patterns like **MVC, Command, and Observer**, we established how UI elements detect player input, translate that input into **command tokens**, and broadcast those tokens to systems that respond.

However, there is still a critical problem we need to solve: **how to load, manage, and organize UI panels at runtime**.

Menus appear and disappear.  
Settings panels open on top of pause menus.  
Dialogue windows may temporarily interrupt gameplay.

Without a structured runtime system, UI panels can quickly become disorganized:
-   Panels overlap incorrectly
-   Multiple menus respond to input at the same time
-   UI objects are repeatedly instantiated and destroyed, causing unnecessary performance overhead

In this lesson, we will focus on **creating a runtime UI system** that is **scene-independent, predictable, and maintainable**. This system separates **UI definitions** from **runtime instances**, making it easy to manage, cache, and extend UI panels without bugs.
#

## Key Components of the Runtime System

To build a robust, scene-independent UI system, we need four essential pieces:

1.  **UI Asset Definition** – a blueprint describing each panel 
2.  **Registry** – a centralized list of all UI assets, linking IDs to prefabs
3.  **Stack System** – tracks which menus are active and what’s on top
4.  **UI Manager** – the runtime controller responsible for opening, closing, caching, and ordering panels
    
These components work together to ensure that **UI panels behave predictably**, menus open and close in the right order, and designer-defined assets can be used without hardcoding references.

--- 
## 1. UI Asset Definition: The Blueprint

Our goal is to treat UI panels as **managed assets** rather than hard-coded objects.

Instead of writing code like:
```csharp
Instantiate(mainMenuPrefab);    
Instantiate(settingsMenuPrefab);
```

we want a **central mapping of UI panels** that we can reference by a **unique identifier (ID)**:

UIManager.OpenUI("main\_menu");

This approach separates **UI definitions from UI logic**, allowing:
-   Designers to configure UI panels as assets
-   Programmers to build dynamic systems that **load, display, and manage them consistently**

    #

### Why We Only Need Data

At this stage, we are **setting up the runtime system**, not the actual instantiated panels.

We do **not need live GameObjects yet** — all we need is:
-   A **unique ID** for each panel
-   A reference to the **prefab** that will be instantiated
-   Metadata such as **caching** rules

> [!NOTE]
To optimize performance, we should **cache** common menus so we are not instantiating them each time the player requests them.
> 

This is enough to **plan, instantiate, and manage UI panels dynamically at runtime**.
Trying to store live GameObjects here would create problems:
-   **Scene reload bugs:** GameObjects in a scene are destroyed on reload, but we want static definitions that persist
-   **Multiple instances problem:** Some UI panels might appear more than once (tooltips, popups), so storing a single instance won’t work
-   **Data pollution:** Modifying live objects in a static asset can accidentally overwrite designer settings
#

### Why a ScriptableObject Makes Sense

A **ScriptableObject** is a Unity asset that stores **data, not runtime objects**, and exists **independently of any scene**.

-   Lives in the **Project folder**, not in the scene
-   Persists across scenes and editor sessions
-   Can store **panel IDs, prefab references, and metadata**
-   Can also include **methods that operate on the data**, such as helper functions to retrieve formatted names, validate IDs, or set default values
-   Allows **designers to create and configure panels without writing code**
    
In other words, the ScriptableObject is a **blueprint for your panels**, providing both **static data** and **helper logic** while remaining completely decoupled from the runtime scene.

> [!TIP]
> Using ScriptableObjects ensures **static data is separated from runtime state**, providing a flexible and maintainable foundation for the UI system.
>

---

## UI Asset Registry: The Central Lookup

Once each panel is defined, we need a **centralized place to store and access these assets**.

This is the **UIAssetRegistry**, another ScriptableObject that acts like a **phonebook or blueprint library**.
-   Maps panel **IDs to UIAssetData**
-   Persists across **all scenes and editor sessions**
-   Provides **fast lookup** at runtime
    

>[!IMPORTANT]
> The Registry only stores **static data**, not instantiated GameObjects.
>

# 

### Why the Registry Cannot Hold Instances

Trying to store live GameObjects in the Registry would cause serious issues:

1.  **Scene Reload Bugs:** Reloading a scene destroys the GameObject, leaving a "Missing Reference" in the Registry
    
2.  **Multiple Instances:** Tooltips or popups may appear multiple times; the Registry cannot track multiple active instances
    
3.  **Data Pollution:** Modifying ScriptableObject fields at runtime can permanently overwrite asset data in the editor

--- 

## UI Stack: Tracking Active Panels

In a dynamic UI system, panels can appear and disappear at any time. **Some panels appear on top of others** (like Settings over Main Menu), while **popups or tooltips** may exist simultaneously.

To manage this effectively, we use a **stack** to track **all active UI panels**. A stack ensures that:

-   **Push → Open a new panel**: The newest panel is on top
    
-   **Pop → Close the top panel**: The previous panel regains focus
    
-   **Peek → Check the top panel**: Determine which panel receives input
-   

### The Stack Entry: The Physical Building (Runtime State)

While the **UIAssetData** ScriptableObject serves as the **blueprint** for a panel, we also need a **runtime reference** to the actual GameObject in the scene. That’s the purpose of the **UIStackEntry**.

Your `UIStackEntry` is a standard C# class that lives entirely in RAM while the game is running. It links:

-   **AssetData:** The **UIAssetData blueprint** defining the panel
    
-   **Instance:** The **instantiated GameObject**, the physical clone of the prefab in the scene
    

> Think of it this way:
> 
> -   The **Registry/ScriptableObject** is the **architectural blueprint**, persistent and scene-independent.
>     
> -   The **Stack Entry** is the **physical building**, created and controlled at runtime.
>     

As soon as you close the game or reload a scene (without `DontDestroyOnLoad`), the **GameObject ceases to exist**, but the blueprint remains intact in your project folder.
