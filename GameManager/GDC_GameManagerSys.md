# 🏹 Game Design Challenge: Game Manager System

> By: Akram Taghavi-Burris | © 2026

We’ve already learned how to set up a basic **GameManager singleton**, define an **IState** interface, create a **BaseGameState** abstract class, and implement simple states like **BootState** and **MainMenuState**. We’ve also applied the **state pattern**, using a stack-based system to manage and transition between states dynamically.

Now, imagine we’re on the job: the design team has handed us a **production brief** outlining the core behaviors of the game’s main loop.

Our task is to **build out the full GameManager system** based on this brief. The goal is **not to implement final gameplay, UI, or polish**, but to create a **robust state management system** capable of handling state transitions, stacking rules, and temporary testing interactions.

---
## 📋 Feature Brief: Game Management System

**Project:** _Camp Craft_  
**To:** Programming Team
**From:** Game Designer

#

### 🎯 Design Intent

The design team would like to implement a **Game Management System** that centrally controls **game states**. These states are **not scenes** — they represent phases or modes of the game, controlling **both backend and visual behaviors**. The system should allow **smooth transitions**, support **state stacking**, and enforce **consistent global behavior**.

Each state is **fully responsible for its own lifecycle**: loading and unloading resources, managing state-specific logic, and running behaviors relevant only while that state is active.

#

### 🔹 Core Concepts
1.  **States vs Scenes**
    -   States define **what happens while active**, not just which scene is loaded.
    -   A state may load/unload scenes, control UI, manage audio, or handle other state-specific behaviors.
        
2.  **State Lifecycle Responsibility**
    -   **Enter()** → Initialize the state, load any scenes or resources, start state-specific actions.
    -   **Execute()** → Runs every frame while the state is active, handling ongoing behaviors like animations, audio transitions, or temporary UI.
    -   **Exit()** → Clean up the state, unload scenes, stop audio, and remove temporary objects.
        
3.  **Boot State & Boot Scene**
    -   Entry point of the game.        
    -   Loads the **Main Menu Scene** in the background while displaying a loading animation.
    -   Loading animation should have a minimum play time regardless of how fast the **Main Menu Scene** loads
    -   Transitions to **Main Menu State** when the scene is fully loaded.
        
4.  **Main Menu State & Scene**
    - **Main Menu Scene** includes main menu UI and input (Play button, etc.).
    -   Clicking Play triggers the **GameManager** to transition to **Playing State**, unloading the Main Menu Scene in the process.
    -   **Main Menu State** should unload the **Main Menu Scene** on Exit 
        
5.  **Playing State and Game Scenes**
    -   Represents core gameplay.
    -   Loads appropriate scenes for the current level (game scene).
    -   **Game Over State** will be triggered from **Playing State** when _game conditions_ are met
    -   Unloads the level (game scene) on Exit. 
        
6.  **Game Over State and Game Over Scene**
    -   Loads the **Game Over Scene**
        
7.  **Pause State**
    -   Stackable overlay state on top of a base state, like Playing.
    -   Freezes gameplay by modifying the time scale.
    -   Can be toggled on and off while leaving the underlying state intact.
  
--- 

## 🔎 Architectural Analysis: Building the GameManager System

Now that we understand the designer’s intent, we need to determine how to structure the GameManager and states to fulfill the brief. Here’s how we, as system developers, can approach it:

### 1. GameManager as the Central Controller
-   The **GameManager** is the central hub of the game, responsible for managing **all game states**.
-   Game elements like the **Main Menu UI** or Triggers **need to communicate with the GameManager** to initiate state changes.
-   Because it must be **globally accessible** for all systems to communicate with it, the GameManager is a natural candidate for a **singleton**.

### 2. Game States
-   Allow **stacking states** (e.g., PauseState over PlayingState), **because temporary overlay states need to coexist with underlying states**.
-   Implement all game states using a **state pattern**, rather than a simple FSM, **because some states need to stack and a switch-based FSM would be hard to maintain**.
-   Each state defines **Enter(), Execute(), and Exit()**, **because each state is responsible for its own lifecycle and behaviors**
-   Because all states have a specific and consistent lifecycle, an **IState** interface can help enforce these behaviors. 
-   All states will need to reference the **GameManager**, implement **IState**, and their **shared utilities**, inheriting from an **abstract BaseGameState**, will help ensure we follow the **DRY** principle. 
-   Because the GameManager fully controls the states and they do not need to exist in the scene, they **do not need to inherit Monobehaviour**
-   The GameManager can **instantiate states on Awake**, **because centralized creation ensures all states are available and managed consistently**.

### 3. Managing the State Stack
-   Because some states are **stackable** (PauseState) and others are **exclusive** (Boot, Main Menu, Playing, Game Over), the GameManager will maintain a **stack of states**.
-   **Exclusive states** will **ReplaceStates()** from the entire stack when called.
-   **Stackable states** will need to **push** and **pop** dynamically to the stack without affecting the underlying base state.
-   The **current active state** is always the **top of the stack** (The _peak_ method shows the top item in the stack).

### 4. Scene Management
-  **Boot State**: Loads a **loading animation** while loading the **Main Menu Scene asynchronously**.
    - Will need to be **using Unity.SceneManagment**
- Since **Boot Scene** is a single point of entry, we don't need to clutter the scene with the loading animation; this can be a **prefab** that is dynamically added to the scene. 
    - Because the **Boot State is not a Monobehaviour**, we can not use a serialized field to access the **loading prefab**. Instead, we will need to load it from the **Resources** folder dynamically.
-   The **Boot Scene** contains a camera, which must be **de-prioritized and untagged** to prevent conflicts when other scenes are loaded.
-   States like **Main Menu**, **Playing**, and **Game Over** are responsible for **loading and/or unloading their respective scenes** on Enter and/or Exit.

### 5. Handling Pause
-   Pause cannot be handled by individual states (like PlayingState), because the paused state is **stacked on top** and the underlying state is not executing.
-   The **GameManager must listen for the pause input globally** and push or pop the PauseState accordingly.
-   The PauseState itself will manage **time scale changes** on Enter and Exit.

### 6. Temporary Testing Artifacts
- To test pause control before the Input System is set up: 
    - Use **Key Input** listeners. Ensure **Active Input Handling** is set to _Both_ under **Player Settings** 
-   To test transitions before UI and gameplay are complete, we need:
    -   A **temporary Main Menu scene** with a simple Play button script to trigger the Playing State.
    -   A **temporary trigger object** (e.g., portal) in the gameplay scene to test the transition to Game Over State.
-   These temporary assets should live in a **_TempTesting folder** in the project to remind us to remove them later.

# 

## 📝 ASSIGNMENT: Implment the GameManager System

Based on the designer’s brief and your architectural analysis, your task is to **implement the GameManager system**. Focus on **state management and transitions**; final UI, gameplay, and polish are **temporary placeholders** for now.

### Key Deliverables

-   Implement all required states (`BootState`, `MainMenuState`, `PlayingState`, `PauseState`, `GameOverState`) with **Enter(), Execute(), Exit()**.
-   Ensure **state stacking works**:
    -   Exclusive states replace the stack.
    -   Stackable states (Pause) push and pop without affecting the base state.
    -   The **current active state** is the top of the stack.
-   Implement **scene/resource loading and unloading** within the state lifecycle.
-   The **GameManager listens globally for pause input** to handle Push/Pop of PauseState.
-   Use **temporary assets** to test state transitions:
    -   Main Menu with a Play button to trigger PlayingState.
    -   Trigger object in the gameplay scene to test GameOverState.
    -   Place temporary objects in a **\_TempTesting folder** for easy removal later.

 # 
 
### 🎮 Playtest
-   Periodically save scripts and scenes. Press **_Play_** in Unity to verify state transitions, stack behavior, and scene loading/unloading.
    

### 🐞 Troubleshooting Checklist
-   **State transitions don’t occur** → Check inheritance from BaseGameState and GameManager references.
-   **Game does not pause** → Confirm time scale changes in PauseState Enter/Exit; ensure global input handling.
-   **Scenes do not load** → Verify scenes are in Build Settings, and names match exactly.
-   **UI not responding** → Check EventSystem exists, and buttons call GameManager correctly.
    
### 💾 Save & Commit
- Ensure that the **GameManager** branch is active 
-   Save scripts and scenes often.
-   Commit after each completed step (e.g., `_feat: Added MainMenu, Playing, Paused, GameOver states_`).
-   Push to GitHub after major features or at the end of the day.

#

### 🏰 Level Up Your States — Optional Enhancements
-   **Knight Rank (Required)**: All core states function correctly, transitions occur as expected, and the GameManager stack reflects the active state.
-   **Hero Rank (Extra Credit)**: Add visual polish to state transitions (fade-out, overlay effects, etc.) once core functionality is working.
  

> [!CAUTION]
>  Extra credit is only earned **after the core functionality works**. Focus on getting your states fully functional before adding flair!
> 
