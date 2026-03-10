# 📜 UI RManager Architecture

> By: Akram Taghavi-Burris | © 2026

In the previous lesson, we designed the **communication architecture for our UI system**. Using patterns like **MVC, Command, and Observer**, we established how UI elements detect player input, translate that input into **command tokens**, and broadcast those tokens to systems that respond.

However, there is still a critical problem we need to solve.

Even with a clean communication architecture, the system still needs a way to **load, manage, and organize UI panels at runtime**.

Menus appear and disappear.  
Settings panels open on top of pause menus.  
Dialogue windows may temporarily interrupt gameplay.

Without a structured runtime system, UI panels can quickly become disorganized:
-   Panels overlap incorrectly.
-   Multiple menus respond to input at the same time.
-   UI objects are repeatedly instantiated and destroyed, causing unnecessary performance overhead.
    
--- 
## UI Managment 
Our goal is to treat UI panels as **managed assets rather than hard-coded objects**.

Instead of writing code like:
```csharp
Instantiate(mainMenuPrefab);  
Instantiate(settingsMenuPrefab);
```
We want a centeral location

We want a system where UI panels can be referenced simply by an **ID**.

For example:

UIManager.OpenUI("main\_menu");

This allows us to separate **UI definitions from UI logic**.

Designers can create and configure UI panels as assets, while programmers build systems that **load and manage them dynamically**.

To accomplish this, our runtime system will provide:

• **A UI Asset Definition** – a ScriptableObject that describes each UI panel  
• **A Registry** – a centralized list of all UI assets  
• **A Stack System** – to track active menus and determine which UI is on top  
• **A UI Manager** – responsible for opening, closing, caching, and ordering UI panels

Together, these components create a **structured UI lifecycle**, ensuring that menus behave consistently across the game.
