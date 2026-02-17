# 🏹 Game Design Challenge: Game Manager System

> By: Akram Taghavi-Burris | © 2026

Now that we have the baseline **GameManager** in place—with its singleton setup, state stack, and BootState—we need to implement the **core behaviors that define the main game loop**. This phase is about **defining the system architecture and intended behavior**, not creating final UI or gameplay. All UI elements, triggers, and scenes are **temporary artifacts for testing**.

## 🎯 Assignment Goals

[](https://github.com/AkramsBookshelf/GCT-Unity/blob/main/GameManager/GDC_GameStates.md#-assignment-goals)

1.  Expand the GameManager system to support all core states: MainMenuState, PlayingState, PausedState, and GameOverState.
2.  Create additional **game states** inheriting from **BaseGameState**.
3.  Implement **basic logic for entering, executing, and exiting** each state.
4.  Use the **GameManager stack system** to handle state transitions (e.g., push, pop, and replace operations)
5.  Provide a foundation for **testing state transitions with temporary objects** (e.g., simple scenes, triggers, and UI interactions).
6.  Optionally, add **fun effects** (fade, overlays) to make transitions feel more dynamic.
