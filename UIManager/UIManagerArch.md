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

---
## Design Patterns & Strategies for the UI Runtime System

Building a robust UI Manager comes with its own set of challenges, many of which are rooted in the difference between a **UI panel’s definition** and its **runtime instance**:

1.  **Dynamic UI panel management:** Panels can open, close, or overlap at any time. To manage this reliably, we need a system that knows **what panels exist** and how to instantiate them correctly.
    
2. **Asset-instance separation:** Traditionally, a UI prefab might be treated as the “asset” itself, but in our system, we separate **definition** from **runtime instance**. A **UI Asset Definition** contains only the **data about the panel**—its unique ID, configuration, layout rules, and behavior metadata—but **not the visual GameObject** that appears on screen.
    
3. **Asset-instance tracking:** When a **runtime instance** (prefab) is instantiated, the system needs to track the actual **GameObject instance** while still referencing the **original UI Asset Definition**. This ensures the UIManager knows the panel’s configuration, animations, and settings, even though the visual object is dynamically created at runtime.
 
4.  **Scene independence:** UI panels may appear across multiple scenes. We don’t want each scene to hard-code its own copies of every menu, because that leads to duplication and inconsistency.
    
5.  **Caching & performance:** Instantiating and destroying UI prefabs repeatedly is expensive. If we separate the **definition** from the **runtime instance**, we can cache panels, preload them, or reuse them efficiently.
    
6.  **Designer-programmer workflow:** Designers need to tweak layout, text, and behavior without touching code, while programmers need a reliable way to reference panels by ID or type.

To solve these challenges, we use a combination of design patterns and practical strategies:

- **Data-Driven Design** by separating UI definitions from code	Designers can configure panels without changing code; allows fields and methods on assets.

-  **Registry Pattern:** Stores all UI Asset Definitions and allows for a central directory.
  
- **Singleton:** ensures only one `UIManager` exists and orchestrates all UI panels. Provides global, consistent access to UI functionality

- **Stack:** A stack is a **Last In, First Out (LIFO)** collection, which will make tracking UI assets more efficient.

By combining these patterns and strategies, we create a **decoupled, maintainable runtime system** for our UI panels. The architecture now clearly separates **panel definitions from their runtime instances**, tracks which panels are active, and allows for efficient caching and scene-independent access.

With this foundation in place, we can adopt a **data-driven approach** to define our UI panels, using **ScriptableObjects** as the blueprint for every menu and panel in the game.

---

## Data Driven Approach
Our goal is to treat UI panels as **managed assets** rather than hard-coded objects.

Instead of writing code like:
```csharp
Instantiate(mainMenuPrefab);    
Instantiate(settingsMenuPrefab);
```
we want a **central mapping of UI panels** that we can reference by a **unique identifier (ID)**:

```csharp
UIManager.OpenUI("main_menu");
```

This approach separates **UI Asset Definition from UI logic**, allowing:
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

## Registry Pattern: Blueprints vs. Buildings

In software development, the **Registry Pattern** is a design pattern used to **store and access shared objects or data from a central location**. Instead of each system keeping its own references, a registry provides a single place to **look up objects by a unique key**, reducing dependencies and keeping the code organized. It’s particularly useful when multiple parts of a program need access to the same resources, ensuring consistency and maintainability.

In our UI system, we are dealing with **two very different realities**:

1.  **Blueprints** – the **UI Asset Definitions** stored in the project. These contain all the **data about a panel**: its unique ID, layout rules, behavior metadata, and configuration. They exist independently of any scene and are never directly visible to the player.
    
2.  **Buildings** – the **runtime instances** of those panels. These are the actual GameObjects that appear in the scene and that the player interacts with.
    

Confusing these two can lead to problems: manipulating project data as if it were live objects, losing track of runtime panels, or unintentionally modifying assets at runtime.

### UI Asset Registry: Collection of Blueprints
To manage UI definitions efficiently, we implement a **UIAssetRegistry**. This acts as a **central directory of all UI Asset Definitions**, allowing the system to:

-   Register each panel with a **unique ID**
-   Look up any asset by its ID, rather than keeping scattered references
-   Minimize dependencies and keep the system organized
    
Think of the registry as the city planner’s **master map of blueprints**. It doesn’t create buildings itself; it simply tells the UIManager **what panels can exist**, along with rules about how they should behave. 

The registry only stores **definitions and metadata**, not live GameObjects. Storing active panels in the registry would create problems:

-   **Scene reload bugs:** Prefab instances are destroyed on scene changes, leaving broken references.
    
-   **Multiple instances:** Some panels, like tooltips, can appear multiple times at once.
    
-   **Data pollution:** Modifying assets at runtime could overwrite project data.
    

In short, the registry answers the question: **“What can we build?”**

#

### The Stack Entry: The Physical Building

Once a panel is opened in the game, a **real GameObject exists in the scene**. This instance is separate from the blueprint and doesn’t inherently know which asset it came from.

This is where the **UIStackEntry** comes in. Each stack entry is a **C# class living entirely in memory**, linking the **runtime instance** to its original **UIAssetData**. It allows the UIManager to:

-   Track which panels are currently open
-   Determine which panel is on top and should receive input
-   Close or hide panels even when the request comes from the prefab instance itself
    
For example, when a user clicks the **Close button** on a settings menu, the button only knows about its own GameObject. The stack entry lets the manager **look up the associated asset data**, retrieve its ID, and remove it safely from the stack.

Think of the stack entry as a **runtime record of what exists in the game**, bridging the gap between **blueprint (registry)** and **physical building (scene instance)**. It’s the essential piece that allows the UIManager to handle panels dynamically, efficiently, and reliably.

---

## The UIManager: Central Orchestrator

The **UIManager** is the central system that coordinates all UI panels. It sits between **UI definitions** (the registry) and **runtime instances** (the stack), ensuring that panels open, close, and interact in a **predictable, maintainable, and efficient** way.

#

### Singleton: One Source of Truth

We use the **Singleton pattern** to ensure there is **only one UIManager** controlling all panels. This provides:

-   A single authoritative controller for UI behavior
-   Global access from any system in the game
-   Consistent rules for opening, closing, and ordering panels
    
The Singleton ensures that no conflicting or duplicated logic arises, making the system **predictable and easy to maintain**.

#

### Stack: Modeling Runtime Panel Behavior

UI panels naturally behave like a **Last-In, First-Out (LIFO)** collection: the most recently opened panel is usually the first to close.

The **Stack pattern** allows the UIManager to:
-   Track which panels are currently open
-   Determine which panel is on top and should receive input
-   Efficiently manage caching, reusing, or removing panels

Together, **Singleton** and **Stack** give the UIManager a **clear structure**: a single orchestrator that can manage panels dynamically, maintain focus, and keep runtime behavior aligned with the panel definitions.
---
## From Architecture to Implementation

Now that we have established the **design patterns** and runtime strategies guiding our UI system, we can start mapping the architecture into the components we will **actually construct**.

So far, we have defined how the system behaves:

-   **Data-Driven Design:** Separates panel definitions from runtime logic
-   **Registry Pattern:** Central directory of UI asset definitions
-   **Singleton:** One authoritative UIManager orchestrating all panels
-   **Stack:** Tracks runtime instances and manages panel order

Together, these patterns provide a **predictable, maintainable, and scalable foundation** for runtime panel management.

The next step is to translate these ideas into **concrete responsibilities**. Each class or component in our system will fulfill a distinct role, while adhering to the patterns and strategies we’ve discussed.

The following table outlines the core components we will implement and how they fit into the patterns discussed in this lesson.

| Class / Component                              | Type                    | Pattern Role             | Purpose                                                                                                                                                         |
| ---------------------------------------------- | ----------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `UIManager`                                    | Behavioral              | Singleton / Orchestrator | Central controller for opening, closing, caching, and ordering UI panels; coordinates runtime behavior and enforces system-wide rules                           |
| `UIAssetRegistry`                              | Creational / Structural | Registry                 | Stores all UI asset definitions (blueprints); allows panels to be referenced by unique ID, independent of scene or runtime instance                             |
| `UIStackEntry`                                 | Structural / Runtime    | Stack Element            | Represents a single runtime instance of a panel, linking the **GameObject** to its **UIAssetData**, enabling the UIManager to track open panels and input focus |
| `UIAssetData`                                  | Data                    | Data-Driven / Blueprint  | Holds all static information about a UI panel: unique ID, prefab reference, layout rules, caching options, and metadata                                         |

By combining these components with the **Singleton, Stack, and Registry patterns**, we are now ready to **translate our architecture into a concrete implementation**, building a system where UI panels can open, close, and interact predictably, no matter how complex the game becomes.

---

## 🚩 Checkpoint: UI Manager Architecture

Having explored the **runtime UI system, design patterns, and data-driven strategies**, here are some key points to **keep in mind** before moving on to implementation:

-   **UI definitions vs. runtime instances:**  
    Separate **UI Asset Definitions** (blueprints) from **runtime GameObjects** (buildings). This ensures that **panel configuration, caching, and behavior** are managed independently of live objects.
    
-   **Singleton ensures a single orchestrator:**  
    The **UIManager** is the **central controller** of all panels, providing **global access** and preventing conflicting logic.
    
-   **Stack models panel behavior:**  
    Panels follow a **Last-In, First-Out (LIFO)** order: the most recently opened panel is the first to close. The **UIStackEntry** links each runtime instance to its asset definition, allowing the manager to maintain focus and track active panels.
    
-   **Registry centralizes definitions:**  
    The **UIAssetRegistry** stores all panel blueprints and metadata, providing a **single source of truth** that can be queried by ID. This reduces scattered references and prevents runtime errors.
    
-   **Data-driven design empowers designers:**  
    **ScriptableObjects** allow designers to configure panels, layout rules, caching, and metadata **without touching code**, while programmers can reference panels dynamically via ID.
    
-   **Dynamic panel management:**  
    The system must handle **opening, closing, ordering, and overlapping panels** consistently, regardless of scene or gameplay state.
    
-   **Caching improves performance:**  
    Panels can be **disabled and reused** instead of destroyed and re-instantiated, reducing CPU and memory overhead.
    
-   **Scene independence:**  
    UI panels exist **across scenes**, so the system avoids duplicating assets or hard-coding panels in multiple locations.
    
-   **Clear responsibilities:**  
    Keep the **registry for definitions**, **stack entries for runtime tracking**, and the **UIManager for orchestration**, ensuring the system is predictable, maintainable, and scalable.



