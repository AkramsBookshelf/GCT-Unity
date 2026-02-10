# 📜 GameManager System
> By: Akram Taghavi-Burris | © 2026

Every game, regardless of complexity, requires a central system to **coordinate what is happening at any given moment**. This chapter focuses on the **GameManager System**, a structured framework responsible for managing the game’s states, coordinating transitions, and ensuring gameplay flows smoothly from start to finish.

Although the **GameManager** is implemented as a single class, it functions as a **system** when considered together with the states it manages. This system is responsible for:

-   Managing transitions between core game modes (Boot, Main Menu, Playing, Game Over)
    
-   Handling temporary overlay states (Pause, Inventory, Dialogue) without disrupting the underlying flow
    
-   Coordinating rules, interactions, and other subsystems, such as **UI** and **audio**
    
-   Providing a clear, maintainable structure that integrates seamlessly with other game systems
    

Framing the GameManager as a system emphasizes that it is not merely a single object, but a **cohesive structure of logic and responsibilities**. From a game systems perspective, it illustrates how different parts of a game communicate and interact to create a consistent, predictable gameplay experience.

## Roadmap for Building the GameManager System

This chapter progresses through the development of a **modular and flexible GameManager System** in the following steps:
-   **[Designing Game States](DesigningGameStates.md)** – Establish a clear structure for states to ensure the system is modular and adaptable.
-   **[Creating a Singleton Base Class](TUT_SingletonBase.md)** – Provide global access while maintaining safety and modularity.
-   **[Creating the GameManager](TUT_GameManager.md)** – Establish the core system to coordinate states and transitions.
-   **[Defining Game States](TUT_GameStates.md)** – Establish a clear lifecycle contract using an interface and provide shared functionality through a base class
-   **[Initalizing States](TUT_InitializeGameStates.md)** – Update the GameManager to create each game state.
-   **[Enhancing the Boot State](TUT_BootState.md)** – Configure the Boot state for a specific initialization scenario.
-   **Game Design Challenge: Implement Additional States** – Extend the system to demonstrate flexibility and reinforce understanding of the architecture.
