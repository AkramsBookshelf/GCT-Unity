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

## UI Stack and Registry: Blueprints vs. Buildings

In our UI system, we need to manage panels in two very different ways: **what exists in the project** and **what exists in the game at runtime**. These two realities are distinct, and confusing them can create serious problems. That’s why we have **both a UIAssetRegistry and a UIStackEntry system**, each serving a different purpose.

#

### The Registry: The Blueprint

Think of the **UIAssetRegistry** as a **set of blueprints for every UI panel in your game**. It lives in your project folder as a ScriptableObject, separate from any scene. It knows that the ID `"main_menu"` corresponds to a specific prefab and stores information like whether it should be cached or how it should be displayed.

Because the registry exists **at the project level**, it persists across scenes and even when the game isn’t running. Designers can configure assets directly in the editor without touching code, and programmers can retrieve any UI panel by its unique ID:

```csharp
UIAssetData mainMenuAsset \= UIAssetRegistry.Get("main\_menu");
```

The registry doesn’t hold any live instances of panels in the scene. It’s only concerned with **definitions and metadata**, the blueprint, not the building. Trying to store active GameObjects in the registry would lead to a host of problems:

-   **Scene reload bugs:** Prefab instances are destroyed when a scene reloads, leaving broken references in the registry.
    
-   **Multiple instances:** Some UI elements, like tooltips or confirmation dialogs, can appear multiple times simultaneously. A single registry entry can’t track all of them.
    
-   **Data pollution:** Modifying ScriptableObjects at runtime could accidentally overwrite project assets, breaking your configuration.
    

In short, the registry answers the question: **“What can we build?”**

#
### The Stack Entry: The Physical Building

Once a panel is opened in the game, a **real instance of the prefab exists in the scene**. This instance is completely separate from the blueprint; it doesn’t inherently know which asset it came from.

This is where the **UIStackEntry** comes in. Each stack entry is a simple C# object that lives in memory while the game is running. It **links the instantiated GameObject to its original UIAssetData**:

By storing both the asset data and the prefab instance, the stack entry allows the UIManager to:
-   Track which panels are currently open
-   Determine which panel is on top and should receive input
-   Close or hide panels even when the request comes from the prefab instance itself
    
Without this association, the UIManager wouldn’t know which blueprint an instance belongs to. For example, when the user clicks a **Close button** on a settings menu, the button only knows about its own GameObject. The stack entry lets the manager **look up the associated asset data**, find its ID, and correctly remove it from the stack.

You can think of the stack entry as a **runtime record of what exists in the game**, bridging the gap between the blueprint (registry) and the physical panel in the scene.

---
## The UIManager: The Runtime Orchestrator

Now that we understand the **blueprint** (UIAssetRegistry) and the **physical building record** (UIStackEntry), we need a **system to orchestrate everything**, which is the role of the **UIManager**.

The UIManager acts as the **director of the UI**, handling:

-   **Opening panels:** Instantiate prefabs from the registry and push them onto the stack.
-   **Closing panels:** Pop panels off the stack and deactivate or destroy them.
-   **Ordering panels:** Ensure the newest panel appears on top of older ones.
-   **Managing input focus:** Only the topmost panel should respond to player interactions.
-   **Caching panels:** Optionally keep panels in memory so they can be reopened quickly without re-instantiation.
    
Without the UIManager, we would be left with **blueprints and runtime records**, but no system to **control how panels appear, disappear, and interact with each other**.

#

### How the UIManager Uses the Registry and Stack

The UIManager is the bridge between **asset definitions** and **live instances**:

1.  **Opening a panel**
    -   A request is made to open a UI panel using the UI asset **ID** 
    -   The manager asks the **registry** (the collection of blueprints): “Which asset corresponds to this ID?”
    -   The registry returns the **UIAssetData** (the single blueprint).
    -   The manager **instantiates the prefab** in the scene.
    -   A **UIStackEntry** (building) is created, linking the blueprint to the instance.
    -   The entry is **pushed onto the stack**, making it the active panel.
        
2.  **Closing a panel**
    -   The request might come from the panel itself (e.g., a close button on the prefab).
    -   Since the prefab doesn’t know about its ID or asset, the manager **looks up the stack entry by instance**.
    -   Using the entry, the manager knows which asset it came from and can safely remove it from the stack, update the focus, and deactivate or destroy the GameObject.
    
3.  **Caching panels**
    -   If the asset is configured to be cached, the manager simply disables the GameObject instead of destroying it.
    -   Reopening the panel reactivates the cached instance, reducing the overhead of repeated instantiation.
        

By keeping responsibilities clear **registry for definitions**, **stack entries for runtime tracking**, and **UIManager for orchestration**, our system remains **predictable, efficient, and scalable**.

### Why a Stack Works

The stack structure perfectly models typical UI behavior:

-   **Push:** Open a panel → it goes on top of existing panels.
    
-   **Pop:** Close the top panel → reveal the previous panel underneath.
    
-   **Peek:** Quickly check which panel is active without modifying the stack.
    

This ensures that **menus appear and disappear in a predictable order**. Players can’t accidentally interact with a background menu while a popup is open, and programmers don’t have to manually track which panels are visible.











