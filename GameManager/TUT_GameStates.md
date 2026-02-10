# 🧭 Adventure: Camp Craft Game States
> By: Akram Taghavi-Burris | © 2026

Now that we have a good understanding of how to design game states, we can start thinking about the specific states we will need for our sample game, _Camp Craft_. In this game, the player needs to **explore the world, collect resources, craft items, and interact with NPCs**, all while navigating menus and handling game progression.

After considering the **core gameplay systems** and what players typically **expect from a game experience**, we’ve determined the following **set of game states** for Camp Craft:

| **Exclusive States**                             | **Stacked States**                                                 |
| ------------------------------------------------ | ------------------------------------------------------------------ |
| Boot – Initializes the game                      | Paused – Temporarily freezes gameplay                              |
| MainMenu – Player chooses to start a new session | Inventory – Player manages items without leaving the game          |
| Playing – Player explores the game world         | Crafting – Player crafts items while gameplay continues underneath |
| GameOver – Player finishes or fails the session  | –                                                                  |

#

### Structuring Game States
Now that we understand our game flow, we’ll organize the structure of our states into **three layers**:
1.  **IState Interface** – Defines the lifecycle every state must follow
2.  **BaseGameState Abstract Class** – Provides default behavior and shared logic
3.  **Concrete States** – Implement actual game-specific logic (e.g., BootState, MainMenuState)

This approach keeps code **organized, reusable, and scalable**, allowing easy addition of new states in the future.

---

# ⚒️ Tutorial: IState Interface

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Game Architecture | 5 minutes         |   Unity, IDE  |

</details>


> [!NOTE]
> Before starting this tutorial:
> - Review the **[Interface Lesson](../DesignPatterns/Interface.md)**
> - Make sure you completed **[GameManager](TUT_GameManager.md)** tutorial
> - Ensure you are on the **GameManager** branch

> [!NOTE]
> Before starting this tutorial, you may want to 
>

### Step 1: Create the IState Interface
1. Open your project in the Unity Editor
2. In the **Project** window, right-click on the **Scripts** folder
3. Create a new sub-folder named **Interfaces**
4. Create a **new script** file using your **script template**
    - Name it: **IState**
5. Open it in your IDE

> [!WARNING]
> While we are starting with a **Monobehavior** script template, our IState is an interface, a special class type that does not inherit from any classes.
>

### Step 2: Declare the Interface
1. **Edit the class declaration** by declaring the **Interface** instad of a **Monobeahaviuor** class. 

```csharp
public interface IState
{
```

### Step 3: Define the Interface requirements
1. Remove all template methods in the class  
2. Add the following methods to ensure that **every state follows a consistent lifecycle** : 
    -   **Enter()** = setup
    -   **Execute()** = run
    -   **Exit()** = cleanup
   
```csharp 

    // called when the state becomes active
    void Enter();

    // called when the state is replaced or popped
    void Exit();

    // called every frame (or periodically)
    void Execute();
}
```
> [!NOTE]
> In an interface, all members are implicitly public and do not require access modifiers like `public`, as they define a visible contract for other classes to follow.
> However, when you implement that interface in a class, you must explicitly mark those members as `public` to fulfill that contract.
> 

> #### 💾 Save & Commit
> - Save your script
> - Commit your changes with the message:
>    - *feat: IState Interface*
> - No need to _push_ changes just yet
>

# 🎉 New Achievement: IState Interface
We now have a **versatile interface** for managing states in our game. Though the **IState interface** is not limited to game states,  it’s a **flexible contract** for any system that needs the E**nter → Execute → Exit** behavior. For example, it could be used for **AI behaviors**, **animations**, or **temporary systems**.

```csharp

public interface IState
{

    // called when the state becomes active
    void Enter();       
    
    // called when the state is popped or replaced
    void Exit();     
    
    // called every frame (or periodically)
    void Execute();       
    
}//end IState

```


#
---

# ⚒️ Tutorial: Abstract BaseGameState Class

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Project Managment | 5 minutes         |   Unity, IDE  |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed **[IState Interface](#%EF%B8%8F-tutorial-istate-interface))** tutorial
> - Ensure you are on the **GameManager** branch

### Step 1: Create the BaseGameState Script

1.  In your **Project** window, navigate to the **GameManager** folder (or create a new **States** folder if you prefer organization).
2.  Create a **new C# script** using your template.
    -   Name it: **BaseGameState**
3.  Open it in your IDE.

#
### Step 2: Implement the IState Interface
1. Modify the class declaration to be an **abstract** class
2. The class must implement **IState**

```csharp
public abstract class BaseGameState : IState
{

} //end BaseGameState

```
> [!IMPORTANT]  
> **Why isn’t this a MonoBehaviour?**  
> `MonoBehaviour` scripts are designed to live on GameObjects and run automatically using Unity’s lifecycle (`Start()`, `Update()`, etc.).
> 
> Game states are **not attached to GameObjects**. They are created and executed by the **GameManager**, so they only need to implement **IState**.
> 

> [!NOTE]  
> **Abstract classes cannot be instantiated directly**. They exist to provide a **common base** for other classes to inherit from. Using an abstract class allows us to **define default behavior** for our game states, while still requiring derived states to implement or override the logic they need.

#

### Step 3: Add GameManager Reference
Each state often needs access to the **GameManager** to push or pop other states.

1.  Add a **protected** **`_gm`** field

```csharp
// Reference to Game Manager
protected GameManager _gm;

```

> \[!NOTE\]  
> Making `_gm` **protected** allows child states to use the GameManager directly, but keeps it **hidden from other classes**.

2. Initialize the **`_gm`** field in the constructor:

```csharp
protected BaseGameState()
{
    _gm = GameManager.Instance;
}

```

>[!NOTE]
> A **constructor** is a special method that runs **when a class instance is created**. In our **`BaseGameState`**, the constructor is used to **get a reference to the GameManager** immediately upon creation. Since this class is not a MonoBehaviour, Unity lifecycle methods like **`Awake()`** and **`Start()`** don’t apply here.

#

### Step 4: Add a Name Property

Adding a **Name property** helps for **debugging** or **logging state transitions**:

```csharp

public virtual string Name { get; protected set; } = "Base Game State";

```

> [!TIP]  
> Derived states can **override** this **virtual** property to return a descriptive name, like `"MainMenu"` or `"Playing"`.
>

#

### Step 5: Implement IState Methods
The **IState interface** requires three methods:
-   **Enter()** – called when the state becomes active
-   **Execute()** – called every frame while the state is active
-   **Exit()** – called when the state is removed
    
1.  Add default (empty) implementations:

```csharp
public virtual void Enter() {}

public virtual void Execute() {}

public virtual void Exit() {}

```

> [!NOTE]  
> Leaving these methods virtual allows **child states to override only what they need**, keeping code clean and maintainable.
>

# 



---

# ⚒️ Tutorial: State Setup

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Project Managment | 5 minutes         |   Unity, IDE  |

</details>

### Step 2 — Create Your First State: BootState

The **BootState** is the _initial_ state of the game.

Think of it as the game’s **single point of entry** — the first thing that runs when your game starts.

In a real game, the BootState is often responsible for tasks like:
-   Loading the Main Menu scene
-   Loading save data
-   Loading global settings
-   Loading assets/resources
-   Showing a loading screen
-   Initializing core systems
    
Even in small projects, BootState is useful because it keeps your startup logic **separate** from your menu logic.

1. Return to the Unity Editor; right-click in your Scripts folder and choose your custom script
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
        // Menu logic will go here later
    }

    public void Exit()
    {
        Debug.Log($"Exiting {Name} State");
    }
}
```

---

# 🎉 New Achievement: State Scaffolds Created!

You’ve successfully created the **IState interface**, along with **BootState** and **MainMenuState** classes. These states follow a consistent lifecycle and form the **foundation of your game’s state system**. You now have a structure you can safely expand with real functionality.

When ready, you can **add new states** or **fill in the behavior** for each state, following the same pattern we’ve established.

## 🛡️ Checkpoint

Key takeaways from this lesson:

-   **IState interface** defines a standard lifecycle (`Enter()`, `Execute()`, `Exit()`) for all game states.
-   **BootState** is the initial point of entry for the game and sets up startup logic.
-   **MainMenuState** is a skeleton for the game’s main menu; future logic will be added to handle menu interactions.
-   **States are not MonoBehaviours**; they are controlled and executed by the GameManager (coming in the next lesson).
-   **Creating new states** follows the same pattern: implement `IState` and define the three lifecycle methods.
-   This structure keeps your code **modular, organized, and easy to expand** as your game grows.


