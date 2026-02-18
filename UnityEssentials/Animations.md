# 📜 Unity Animation

> By: Akram Taghavi-Burris | © 2026

In many games, **interactive objects** such as doors, gates, or levers often need animations to **open, close, or react to player actions**. Unity provides a robust **Animation** and **Animator** system to control object states with precision and flexibility.

In this tutorial, we will animate a **park gate** that separates two areas. We’ll create **open and close animations**, set up an **Animator state machine**, and configure **parameters** to control gate behavior. Finally, we’ll turn the gate into a **prefab variant** so it can be reused across the scene.

> [!TIP]  
> Think of the Animator as a **state machine** for your object—it controls what animation plays, when it plays, and under what conditions.
>

## Best Practices for Animation
-   **Name your objects clearly** (`Gate_Interactable`, `Gate-Left`, `Gate-Right`) for easy reference.
-   **Keep animation clips and prefabs organized** in dedicated folders (`Animation`, `Prefabs`).
-   **Use Boolean parameters in the Animator** for true/false conditions, which simplifies transitions.

---

# ⚒️ Tutorial: Animate the Gate

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Animation     | 30 minutes       |   GitHub Desktop, Unity , Gate Prefab   |

