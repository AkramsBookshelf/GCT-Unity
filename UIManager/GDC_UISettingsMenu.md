# 🏹 Game Design Challenge: Extending the UI System

> By: Akram Taghavi-Burris | © 2026

We’ve already learned how to set up a **robust UI system** with buttons, using **MVC, Command, and Observer patterns**, an **Event Bus**, and a **UIMappingRegistry** to map UXML buttons to command tokens. Students have implemented a **Main Menu Controller**, handled **StartGame** and **QuitGame** commands, and centralized logic in the **UICommandHandler**.

Now, imagine we’re on the job: the design team wants the game to support **player-adjustable settings**, like audio volume, graphics options, and toggles for fullscreen or VSync.

Our task is to **extend the current UI system** to support **sliders and toggles**, using the same design patterns, while keeping the architecture decoupled, scalable, and maintainable.

---

## 📋 Feature Brief: Settings UI System

**Project:** _Camp Craft_  
**To:** Programming Team  
**From:** Game Designer

### 🎯 Design Intent
The design team would like to extend the current UI system to handle **UI sliders and toggle inputs** that control the volume settings. The UX Designer has already provided the layout for the **Options Menu** where these inputs are housed.

While we are initially implementing **Master Volume** (slider) and **Mute All** (toggle), the system should be designed so that **future sliders and toggles** can be added and behave consistently.

Much like the buttons, these new elements should:
-   Use **tokens** to represent slider or toggle intent, similar to button commands.
-   Use the **Event Bus** so multiple systems can respond independently to changes.
-   Maintain a **clean separation of concerns** between View, Controller, and Model.
-   Support **centralized mapping** between UI element names and their logic, minimizing duplication.

#


