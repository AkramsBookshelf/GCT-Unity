# 🎮 GameManager Class
> By: Akram Taghavi-Burris | © 2026

Now that we have a reusable **[Singleton Base Class](TUT_SingletonBase.md)**, we can begin work on the **GameManager**.

The GameManager is the **central hub of the game** — it controls the overall **flow and rules of the game**. In our system, it will manage the **game states** and determine what happens at each moment. Essentially, it decides:
-   **Which state is currently active**
-   **When a state starts or ends**
-   **When the state executes its behavior**
    

> [!IMPORTANT]  
> While our GameManager will control the states, we will **implement the full logic of each state later** — after we create the **state classes** themselves.
> 

For now, we are focused on building the **foundation of the system**:
-   A **Singleton GameManager** that exists globally
-   A **stack of states** to track active and previous states
-   Methods to **push, pop, and replace** states
-   Running the **current state each frame**

---

# ⚒️ Tutorial: Creating the GameManager
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Game Development | 30 minutes        | Unity, IDE |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed **[Singleton Base Class](TUT_SingletonBase.md)** tutorial  
> - Ensure you are on the **GameManager** branch

---

### Step 1: Create the GameManager Script

1. Open your project in the Unity Editor
2. In the **Project** window create a new folder named **GameManager**
2. Inside **GameManager** folder create a **new script** file using your **script template**
   - Name it: **GameManager**
5. Open it in your IDE

---

### Step 2: Add Required Namespaces
1. At the top of the script, add:

```csharp
using System.Collections.Generic;
using UnityEngine;

```
[!IMPORTANT]
We must include: `using System.Collections.Generic;`
Because the Stack type is part of the C# Collections library.

#
### Step 3: Inherit from the Singleton Base Class
Instead of hard coding the singleton logic inside the **GameManager** class, it will inherit the **Singleton** base class.

1. Modify the class delceration as follows: 

```csharp
public class GameManager : Singleton<GameManager>
{

}//end GameManager
```

Inheriting the **Singleton** class provides us with: 

-   A global instance: **`GameManager.Instance`**
-   Singleton validation in **`Awake()`**
-   Optional persistence across scenes (from the base class)

# 

### Step 4: Create a Stack to Store Game States
Our **GameManager** will manage states through a **stack**. A stack is useful because:

-   It always keeps a “top” state active
-   It allows temporary states like **Pause**, **Menus**, **Popups**, etc.
-   It supports returning to the previous state easily

1. Define our stack: 
```csharp
// New stack for game states
private Stack<IState> _stateStack = new();

```

> [!NOTE]  
> We are using **`IState`** because we want our states to share a common interface:
> -   `Enter()`
> -   `Execute()`
> -   `Exit()`
> 
> We will implement the interface and concrete state classes in the next lessons.
>

# 

### Step 5: Add a Property for the Current State
The GameManager needs to **keep track of the current state**, which is the top-most element in the state stack. We can get this value using the **`Peek()` method**, a built-in method of C#’s **`Stack<T>`** class.  
**`Peek()`** returns the top item **without removing it** from the stack.

To avoid any _runtime errors_ if our state stack is empty, we can check if the **`_stateStack.Count > 0`** before calling **`Peek()`**.

1. Create a property to return the current state: 
```csharp
// Current state value
public IState CurrentState => _stateStack.Count > 0 ? _stateStack.Peek() : null;

```

> [!TIP]
> A **ternary operator** is used here as a shorthand for **`if-else`** allowing us to combine the stack check and the **`Peek()`** into a single line.
>

#

### Step 6: Override Awake()
The **GameManager** will eventually initialize game states in **`Awake()`**.  
However, because it inherits from **Singleton**, which already has its own **`Awake()`** method, we need to **override** it while making sure the **Singleton** base logic still runs.

1.  Create an **override** for **`Awake()`** in the GameManager class.
2.  Call **`base.Awake()`** at the start of your method to preserve the Singleton behavior and ensure only one instance exists.

```csharp
// Awake is called once on initialization (before Start)
protected override void Awake()
{
    // Implement the Awake function of Singleton
    base.Awake();

} //end Awake()

```

> [!WARNING]  
> Forgetting to call `base.Awake()` can lead to multiple GameManagers being created or the singleton not persisting between scenes.
>

#

### Step 7: Executing States
Our game states will **not follow the standard Unity lifecycle**. Instead, the **GameManager** will control when a state **enters**, **exits**, and **executes**.
By having the GameManager manage execution, we give ourselves the flexibility to **change how and when states run** in the future—such as running certain states at specific intervals or under special conditions.
For now, to keep things simple, all states will execute **once per frame** inside the GameManager’s **`Update()`** method.

1. Add the following to the **`Update()`**

```csharp
// Update is called once per frame
private void Update()
{
    // Execute current state (on top of the stack)
    CurrentState?.Execute();

} //end Update()


```

#


### Step 8: Push States to Stack
When a new state becomes active, we need to:
-   Add it to the stack
-   Call its **`Enter()`** method

C#’s **`Stack<T>`** class provides a **`Push()`** method that adds an item to the top of the stack.
Instead of calling `Push()` directly, we wrap it in a **`PushState()`** method. This lets the GameManager handle all state-related logic in one place, like entering the new state and performing any validation.

1. Create the **`PushState()`** method

```csharp
/// <summary>
/// Adds a new state to the top of the state stack and makes it the active state.
/// This automatically calls Enter() on the new state.
/// </summary>
/// <param name="newState">The new state to activate.</param>
public void PushState(IState newState)
{
    // Add state to stack
    _stateStack.Push(newState);

    // Enter the new state
    newState.Enter();

    // Only runs when a state is added
    ValidateStateChange();

} //end PushState

```
#

### Step 9: Pop States from the Stack
When a state ends, we need to:
-   Exit the current state by calling its **`Exit()`** method
-   Remove it from the stack
    
Just as with pushing states into a stack, C#’s **`Stack<T>`** class has a **`Pop()`** method that removes the top item from the stack.
We wrap this in a **`PopState()`** method so the GameManager can handle **both exiting the state and removing it**, keeping all state logic in one place.

1. Create the **`PopState()`** method

```csharp
/// <summary>
/// Removes the current state from the stack.
/// This automatically calls Exit() on the state being removed.
/// </summary>
public void PopState()
{
    if (CurrentState != null)
    {
        // Exit the state we are leaving
        CurrentState.Exit();

        // Remove it from the stack
        _stateStack.Pop();
    }

} //end PopState()
```

#
### Step 10: Replace All States
Some states in our game are **exclusive**, meaning they cannot be active at the same time as certain other states. For example, _MainMenu_ and _Playing_ should never be stacked together.
To activate an exclusive state safely, we will create a **`ReplaceStates()`** method that:
-  **Pop all existing states** from the stack one by one by calling `PopState()` repeatedly until the stack is empty.
-  **Push the new state** on top of the stack using `PushState()`.

 This ensures that exclusive states never overlap with incompatible states, keeping the stack clean and the game flow consistent.

 1. Create the **`ReplaceState()`** method

```csharp
/// <summary>
/// Clears all states from the stack and replaces them with a new state.
/// This is useful when switching to exclusive game states (e.g., MainMenu → Playing).
/// </summary>
/// <param name="newState">The state that should become the only active state.</param>
public void ReplaceStates(IState newState)
{
    while (_stateStack.Count > 0)
    {
        // Remove all stacked states
        PopState();
    }

    // Add new state to stack
    PushState(newState);

}//end ReplaceStates

```
#

### Step 11: Create a GameManager Prefab
1. Return to the Unity editor and open the **Boot** scene
2. In the **Hierarchy** window, right-click and **Create Empty**
3. Name the empty object **GameManager**
4. Add the **GameManager** script to the object 
5. Place the object inside the **MANAGERS** Hierarchy folder 
6. Convert the **GameManager** object to a **prefab** (i.e., drag and drop it into the_Assets/Prefabs_ folder in the **Project** window)
7. Save the scene.

> #### 💾 Save & Commit
> - Save your script
> - Commit your changes with the message:
>    - *feat: GameManager created*
> - **Push** changes to GitHub
>

---

# 🎉 New Achievement: Singleton Base Class Created!

You now have a working **GameManager Singleton** with a **state stack** system.

```csharp
using System.Collections.Generic;
using UnityEngine;

public class GameManager : Singleton<GameManager>
{
    // New stack for game states
    private Stack<IState> _stateStack = new();

    // Current state value
    public IState CurrentState => _stateStack.Count > 0 ? _stateStack.Peek() : null;

    // Awake is called once on initialization (before Start)
    protected override void Awake()
    {
        // Implement the Awake function of Singleton
        base.Awake();

    } //end Awake()

    // Start is called once before the first Update
    private void Start()
    {

    } //end Start()

    // Update is called once per frame
    private void Update()
    {
        // Execute current state (on top of the stack)
        CurrentState?.Execute();

    } //end Update()

    /// <summary>
    /// Adds a new state to the top of the state stack and makes it the active state.
    /// This automatically calls Enter() on the new state.
    /// </summary>
    /// <param name="newState">The new state to activate.</param>
    public void PushState(IState newState)
    {
        // Add state to stack
        _stateStack.Push(newState);

        // Enter the new state
        newState.Enter();

        // Only runs when a state is added
        ValidateStateChange();

    }//end PushState

    /// <summary>
    /// Removes the current state from the stack.
    /// This automatically calls Exit() on the state being removed.
    /// </summary>
    public void PopState()
    {
        if (CurrentState != null)
        {
            // Exit the state we are leaving
            CurrentState.Exit();

            // Remove it from the stack
            _stateStack.Pop();
        }

    }//end PopState()

    /// <summary>
    /// Clears all states from the stack and replaces them with a new state.
    /// This is useful when switching major game modes (ex: MainMenu → Gameplay).
    /// </summary>
    /// <param name="newState">The state that should become the only active state.</param>
    public void ReplaceStates(IState newState)
    {
        while (_stateStack.Count > 0)
        {
            // Remove all stacked states
            PopState();
        }

        // Add new state to stack
        PushState(newState);

    }//end ReplaceStates

}//end GameManager


```
## 🛡️ Checkpoint

Key takeaways from this lesson:

-   The **GameManager** is a central hub that controls the **flow of the game**.
    
-   Using a **stack of states** lets the GameManager track the **current and previous states** efficiently.
    
-   The **CurrentState** property returns the **active state** at the top of the stack using the `Peek()` method.
    
-   **PushState()** adds a new state to the stack and calls its **Enter()** method.
    
-   **PopState()** removes the top state and calls its **Exit()** method.
    
-   **ReplaceStates()** clears all current states and adds a new one, useful for **exclusive states** like MainMenu or Playing.
    
-   By controlling state execution in **Update()**, the GameManager determines **when states run**, giving flexibility to expand later.













