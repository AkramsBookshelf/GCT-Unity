# 📜 GameManager System
> By: Akram Taghavi-Burris | © 2026

In our previous lesson, we discussed key considerations for designing game states and the decisions that should be made before implementation. Now, we will begin implementing those design choices by building a more complete Game Manager and Game State system.

It’s important to note that there are many valid ways to construct a Game Manager and state system, and the best approach depends on the needs and scale of the game. The method used in this tutorial is intended primarily as a teaching tool, helping illustrate concepts such as the **Singleton pattern**, **interfaces**, the **State Pattern**, and how to structure systems using **SOLID principles**.


## ⚒️ Implementing Game States with the State Pattern

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Project Managment | 5 minutes        |   Unity, IDE  |

</details>

# 

>[!NOTE]
> #### Understanding Game Flow Before Implementation
> Before we write any code, let’s **connect back to our earlier design**. In the “Designing Your Game States” stage, we identified the main flow and overlays for our game:
> **Mutually Exclusive States (Replace)**
> - **Boot → MainMenu**: Boot is done forever
> - **MainMenu → Playing**: New game session
> - **Playing → GameOver**: Session ends
> - **GameOver → MainMenu**: Fresh start
>   
> **Stacked States (PushState)**
> - **Paused**: Temporarily freeze gameplay
> - **Inventory**: Keep gameplay underneath intact
> - **Crafting**: Player returns to same position
> - **Settings Menu**: Overlay, not a new phase
>
> 💡 **Reference:** These states define the **skeleton of our game flow**. When we implement them using the State Pattern, we’ll give each state its own behavior, allowing transitions to handle both **replacements** and **overlays** cleanly.


---
### Step 1: Create the IState Interface
After opening your Unity project, go to the **Project** window and create a new script using your **custom script templates**:

1.   Right-click in your Scripts folder and choose your custom script
    -    (e.g., **Create → CSG Templates → MonoBehaviour → MonoBehaviour Basic Script**)
2.   Name the script: **IState**

> [!NOTE]
> While we are starting with a **Monobehavior** script template, our IState is an interface, a special class type that does not inerhert any classes.
>

3. Double-click on the **IState** class in the Unity **Project** window
4. In your IDE, replace the start code with the following
   - _Leave the documentation comments at the very top and update them as needed_

```csharp
public interface IState
{
    // read-only property for debugging/logging
    string Name { get; }

    // called when the state becomes active
    void Enter();

    // called when the state is replaced or popped
    void Exit();

    // called every frame (or periodically)
    void Execute();
}
```

This ensures **every state follows a consistent lifecycle** : 
-   **Enter()** = setup
-   **Execute()** = run
-   **Exit()** = cleanup

This keeps our code organized and makes it easier for a GameManager to control states later.

#

### Step 2 — Create Your First State: BootState

The **BootState** is the _initial_ state of the game.

Think of it as the game’s **single point of entry** — the first thing that runs when your game starts.

This state happens _before_ the player ever sees:

In a real game, the BootState is often responsible for tasks like:
-   Loading the Main Menu scene
-   Loading save data
-   Loading global settings
-   Loading assets/resources
-   Showing a loading screen
-   Initializing core systems
    
Even in small projects, BootState is useful because it keeps your startup logic **separate** from your menu logic.

1. Return to your Unity Project; right-click in your Scripts folder and choose your custom script
2. Name the script: **BootState**
3. Double-click on the **BootState** class in the Unity **Project** window
4. In your IDE, replace the start code with the following

```csharp
using UnityEngine;

public class BootState : IState
{
    public string Name => "Boot";

    public void Enter()
    {
        Debug.Log($"Entering {Name} State");
    }

    public void Execute()
    {
        // Boot logic will go here later
    }

    public void Exit()
    {
        Debug.Log($"Exiting {Name} State");
    }
}

```
> [!IMPORTANT]
> #### Why isn’t this a MonoBehaviour?
> `MonoBehaviour` scripts are designed to live on GameObjects and run automatically using Unity’s lifecycle (`Start()`, `Update()`, etc.).
>
> Game states are not attached to GameObjects. They are created and executed by the **GameManager**, so they only need to implement `IState`.

#

### Step 3 — Create the MainMenu State
1. Repeat **Step 2** above to create a **MainMenuState**
2. The resulting code should match the following: 

```csharp
using UnityEngine;

public class MainMenuState : IState
{
    public string Name => "MainMenu";

    public void Enter()
    {
        Debug.Log($"Entering {Name} State");
    }

    public void Execute()
    {
        // Boot logic will go here later
    }

    public void Exit()
    {
        Debug.Log($"Exiting {Name} State");
    }
}
```

---


