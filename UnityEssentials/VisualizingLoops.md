# 📜 Visualizing Loops
 By: Akram Taghavi-Burris | © 2026

Game loops are the heartbeat of gameplay, driving player actions, game reactions, and feedback in a continuous cycle. While we can describe loops in words, **visualizing them helps designers and developers understand the flow of gameplay, identify potential issues, and plan interactions clearly**.

## Using Flowcharts to Represent Loops

Flowcharts are a practical way to map both **main gameplay loops** and **object-specific interaction loops**. They use standardized symbols to represent actions, decisions, inputs, outputs, and repeated processes, making complex interactions easy to follow.

| Symbol                 | Meaning                             |
| ---------------------- | ----------------------------------- |
| Oval                   | Start or End of a process           |
| Rectangle              | Action or process step              |
| Parallelogram          | Player input or game feedback       |
| Diamond                | Decision point (Yes/No, True/False) |
| Trapezoid Top / Bottom | Start / End of a loop               |
| Arrow                  | Flow direction                      |

By translating loops into flowcharts, we can **see how core mechanics, player decisions, and game feedback interconnect**, providing a clear blueprint for both documentation and implementation.

---
## Game Brief
In our **Game Design Concept Document**, we introduced the basic idea of our game loops, outlining how players move through the park, collect trash, and progress. However, those descriptions were high-level and conceptual. The **true visualization and documentation of these loops** happens in the **Game Brief**, where we expand on the concept and map out the details of gameplay, player interactions, and objects/entities.

A **Game Brief** is a comprehensive project-level document that covers every aspect of the game, including:

-   **Overview:** The overall vision, theme, and pitch for the project.
-   **Core Gameplay:** Objectives, core mechanics, player controls, and game loops.
-   **Game Objects / Entities:** List of primary objects or characters and how they behave.
-   **Levels / Progression:** Level structure, challenges, and milestones.
-   **UI / UX:** HUD elements, menu flows, and interaction guidelines.
-   **Art Brief:** Style, mood, color palette, key assets, and animation requirements.
-   **Audio Brief:** Music, sound effects, and voice-over considerations.
-   **Technical Brief:** Engine, platform constraints, coding standards, and dependencies.
-   **Team-Wide Standards:** Version control workflow, asset naming conventions, and project organization.
-   **Deliverables / Scope:** Minimum viable product, stretch goals, and out-of-scope features.
-   **Timeline / Schedule:** Detailed project timeline with milestones and task assignments.
    
For this lesson, we will focus specifically on the **Core Gameplay Brief**, a subset of the full Game Brief that documents the **heart of the game**. This section details the **core mechanics, game loops, player actions, and object interactions**, providing a structured view of gameplay that can guide prototyping and implementation in Unity. By examining the gameplay brief for our Park Cleanup prototype, we can see how high-level concepts translate into **concrete loops and player interactions**.

# 
> #### 📗Textbook Project - Gameplay Brief
>
 ### 📄 Overview
 - **Title:** Park Clean-Up
 - **Genre / Style:** Casual Puzzle / Exploration
 - **Platform(s):** Tablet, Chromebook, PC
 - **Target Audience:** Ages 8–9 (3rd grade), motivated by creativity, achievement, and immersive exploration.

 #### ⚙️ Core Gameplay
 **Game Objective:**  

 Players explore a park to collect trash and recyclables, deposit them in the correct bins, and unlock gates to progress to new areas. The goal is to clean all areas of the park while learning about environmental stewardship.

 **Core Mechanics:**
 -   **Walking/Movement:** Navigate the park using keyboard and mouse.
 -   **Picking Up Trash:** Approach trash items to collect them.
 -   **Depositing Trash:** Place collected items into the trash bin.
 -   **Mission Complete:** Mission completed when the required amount of trash has been collected at the trash bin.
 -   **Unlocking Gates:** Gate will unlock on mission complete. 

 **Player Controls:**

 -   **Keyboard/Mouse:** WASD / Arrow Keys to move, Space to jump

 ### Gameplay Loop (High-Level)
1.  **Decision:** Player chooses where to go and what trash to collect.
2.  **Action:** Player picks up trash or interacts with a gate/bin.
3.  **Game Reaction:** Game updates the current collected count, deposited amount, and/or triggers animation/sound feedback.
4.  **Feedback:** Player sees progress updates, audio cues, or visual indicators.
5.  **Repeat:** Player continues until all areas are cleaned and all gates unlocked.

#### Main Gameplay Loop
![main gameplay loop](imgs/loops/gct-parkGame-MainLoop.png)

| Object    | Attributes                          | Behaviors / Interactions                                |
| --------- | ----------------------------------- | ------------------------------------------------------- |
| Player    | Position, inventory capacity        | Moves, collects trash, deposits items, triggers gates   |
| Trash     | Type (regular/recyclable), position | Can be collected, removed from scene                    |
| Trash Bin | Position, type (recycle/regular)    | Receives trash, updates count, triggers feedback        |
| Gate      | Position, locked/unlocked           | Opens when area objectives complete, triggers animation |


#### Detailed Gameplay Loop
![detailed gameplay loop](imgs/loops/gct-parkGame-DetailedLoop.png)


#### Levels / Progression
-   **Areas:** Park is divided into zones with increasing trash quantity and complexity.
-   **Progression:** Clean one zone to unlock the next gate.
-   **Challenge Curve:** Early zones are small with fewer trash items; later zones introduce more objects, limited inventory capacity, and time-based challenges.
-   **Milestones:** Completing a zone triggers visual feedback, sounds, and opens new areas.

# 
---

## 🔎 Examining Our Game Loop
In the Game Brief above, the **Main Gameplay Loop** provides a high-level breakdown of expected player actions and interactions. The **Detailed Gameplay Loop** goes further by defining all possible outcomes and object interactions.

For example:
-   What happens if the player approaches the gate before collecting any trash?
-   Who determines whether the gate should open?
-   Where is mission progress stored?
    
The detailed loop forces us to answer these questions clearly.

---

##  Object Responsibility and Life Cycle
The detailed loop also breaks down the flow by game object. One common mistake beginner developers make is placing _all_ logic on the Player object. While centralizing everything may seem convenient at first, it quickly becomes unmanageable and difficult to scale.

Instead, we must think logically about **ownership**.

Each game object should be responsible for its own:
-   Life cycle
-   Behaviors
-   Feedback
-   Internal state
    

For example:
-   If a trash bag should destroy itself when collected, the destruction logic belongs on the Trash Bag.
-   If the trash bag plays a collection sound, that sound belongs on the Trash Bag.
-   If a gate plays an opening animation, that animation logic belongs on the Gate.
    
Each object manages itself. This keeps responsibilities clean and modular.

>[!NOTE]
> In software development, there is a set of design guidelines known as the SOLID principles. The **S** in SOLID stands for the _Single Responsibility Principle_, which states that any object, class, or method should have one responsibility. By keeping responsibilities separate, we maintain cleaner, more modular, and maintainable code.
>

---

### Level Responsibility 
Not all behaviors belong to a specific game object. 

In our Park Clean-up game, we need to keep track of: 
-   How much trash has been collected
-   How much trash is required to complete the level
-   Whether the mission is complete

These values are not tied to the Player or other objects in the game; instead, they describe the **state of the level itself**.
The solution here, as illustrated in the Gameplay Brief above, is to implement a **Level Manager** that is responsible for keeping track of the level variables and conditions. The Level Manager does not need to be a visible object in the scene. In Unity, its logic is typically attached to an empty GameObject that exists purely to manage level state. In larger games, this same practice is often expanded into full game managers, quest managers, or even mission systems. 

For our prototype, a simple Level Manager is sufficient.

# 

### Designing the Level Manager Variables
To track mission progress, the Level Manager needs to store information as **variables**. A **variable** is a named container that stores data, which can change over time.

Common variable types include:
-   **Integer (int)** — whole numbers
-   **Float** — decimal numbers
-   **Boolean (bool)** — true or false
-   **String** — text
    
For our park game, we need to keep track of the following data: 
-   The amount of trash currently collected. Since trash is counted in whole units, an integer is the appropriate data type.
-   The required amount of trash needed to complete the mission, again, an integer makes the most sense.
-   A check to determine if the mission has been completed or not, this is either true or false, making a Boolean the perfect solution for this. 

# 

#### Variable Naming 
When naming our vairables we should always use names that are explicit and remove any ambiguity. 
For our Park Clean-up game, we've decided to go with: 
- currentTrashCount: this explicitly states that it is the value (_count_) of the _current_ collected _trash_
- requiredTrashCount: this explicitly states it is the _required_ value (_count_) of trash needed
- IsMissionComplete: Boolean variables should read like a question that clearly resolves to either true or false.

You may notice that in naming our variables: 
-   `currentTrashCount` and `requiredTrashCount` use **camelCase**
-   `IsMissionComplete` uses **PascalCase**

This is intentional. In Unity and C# conventions:
-   **Private variables** commonly use camelCase. These are variables that only the object that declares them has access to.
-   **Public properties or methods** use PascalCase. These allow for other objects to access these values or behaviors.

In a well-structured Level Manager:
-   The trash bag and trash bin do **not** directly access `currentTrashCount` and `requiredTrashCount`.
-   They call methods like `AddTrash()` or `CheckMissionComplete()`.
-   The Gate checks a public property like `IsMissionComplete`.
    
This protects internal data while exposing only what other objects need. This concept is called **encapsulation**.




