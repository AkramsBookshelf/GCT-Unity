# 📚 Game System
> By: Akram Taghavi-Burris | © 2026
>

With our park world set up, it’s time to step back and look at the **game as a system**. Before we dive into scripting or building interactions in Unity, we need to understand the components, relationships, and dynamics that make our game function.

## Games as Systems
At its core, a game is a **system of interconnected elements** that interact to create meaningful gameplay. These elements include:
-   **Objects:** The building blocks of the game world, such as players, items, trash bags, bins, and gates.
-   **Properties:** Attributes of objects, like the number of trash bags collected or a gate’s locked state.
-   **Behaviors:** Actions objects can perform or trigger, such as picking up an item, opening a gate, or respawning.
-   **Relationships:** Interactions between objects, like the player depositing a trash bag in a bin or a gate opening only after a section is cleaned.

By viewing a game as a system, we can better understand how individual elements interact and produce emergent behaviors, strategies, and experiences that go beyond isolated actions. This systems perspective is especially valuable in Unity, where each object in the game has its own lifecycle and interactions, yet fits into the larger loop of gameplay.

--- 

## ⚙️ Game Mechanics
Within this system, **game mechanics** define the full framework of rules, structures, and interactions that guide how the game works. They answer the question: **“How does the game function?”**

Game mechanics include:
-   The rules for collecting and carrying trash bags.
-   Limits on player actions (e.g., how many bags can be carried at once).
-   Gate locks that prevent progression until all trash in a section is cleaned.
    
Game mechanics define **what can happen** in the game and how the system responds, setting the stage for the player’s actions. They form the backbone of the interactive experience, but they do not yet focus on the specific, repeated actions the player performs.

#
### Core Mechanics
**Core mechanics** are the repeated, fundamental actions that players perform to achieve their goals. They sit at the center of the larger system, emerging naturally from the interaction of game mechanics, rules, and environment. They answer the question: **“What is the player actually doing in the game?”**

For _Park Clean-Up_, the core mechanics include:
-   **Walking through the park:** Exploring the environment to locate trash.
-   **Picking up trash:** Collecting litter scattered around the park.
-   **Depositing trash into bins:** Completing a mini-task that contributes to overall progress.
-   **Unlocking gates to progress:** Accessing new areas by completing section objectives.
    
While the system and rules guide these actions, the **core mechanics define the player experience**—what the player engages with repeatedly.

---
## Game Loops and Feedback
The core mechanics of a game are realized through continuous **loops of interaction** commonly referred to as **game loops**. Each cycle of player action, system response, and feedback reinforces these mechanics and drives engagement. Loops are essential for keeping gameplay engaging, coherent, and responsive.

### High-Level Loop 
**High-level interaction loop for Park Clean-Up:**
1.  **Decision:** The player chooses where to explore or which trash to pick up.
2.  **Action:** The player collects a trash bag and moves it to a bin.
3.  **Game Reaction:** The game updates the trash count and checks if the section is complete.
4.  **Feedback:** Visual and audio cues signal progress, such as sparkles on the bin, brighter colors in the park, or a satisfying sound.
5.  **Repeat:** The player continues until the section is cleaned and the gate unlocks.

#

### Feedback Loop
This loop is supported by **feedback mechanisms**, which can be:
-   **Positive feedback:** Reinforces successful behaviors (e.g., collecting all recyclables yields bonus points).
-   **Negative feedback:** Maintains balance and gently guides players (e.g., incomplete trash sections prevent gate access).

#

### Object-Specific Loops 
Smaller, **object-specific loops** run in parallel, managing behaviors of individual game objects:
-   **Trash bag:** Spawns, can be picked up, then disappears once deposited.
-   **Trash bin:** Updates the count of collected bags and tracks section completion.
-   **Gate:** Checks section completion status and opens only when conditions are met.

By mapping these loops, we can see how the player’s actions interact with the game system and how feedback reinforces engagement.

---

## Unity Lifecycle Integration
In Unity, game loops and object interaction loops are closely tied to the **script lifecycle**, which dictates when objects respond to input and update their state:

-   **Initialization Phase:** `Awake()` and `Start()` set up objects, initialize variables, and establish references before gameplay begins.
-   **Main Gameplay Loop:** `Update()` runs every frame for general behavior and input, while `FixedUpdate()` is called at fixed intervals for physics calculations.
-   **Final Adjustments:** `LateUpdate()` adjusts visual elements like camera positioning or animation after other updates.
-   **Cleanup Phase:** `OnDestroy()` cleans up objects and releases resources when they are no longer needed.
    

Each game object follows its own lifecycle, essentially forming a **micro-loop** within the broader gameplay loop. For example:
-   The player picks up a trash bag (`Update()` detects input, collision triggers collection).
-   The trash bag notifies the bin that it has been collected.
-   The bin updates the section progress.
-   The gate checks if the section is complete and opens if conditions are met.
    
By understanding both **macro-loops** (core game loop) and **micro-loops** (object-specific lifecycle), we can design gameplay that is predictable, modular, and easy to iterate on.



