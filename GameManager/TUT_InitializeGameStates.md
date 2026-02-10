# ⚒️ Tutorial: Initialize Game States in GameManager
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Game Development | 20 minutes        | Unity, IDE |

</details>

In this tutorial, we will modify the **GameManager** to automatically create instances of our game states and validates which state is active. This ensures your game has a **consistent starting point** and provides **debug feedback** in the editor.

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed **[Defining Game States](TUT_TUT_GameStates.md)** tutorial  
> - Ensure you are on the **GameManager** branch

---
### Step 1:  Add Public State References
We want the **GameManager** to hold references to all game states. This allows us to **push, replace, or access states** easily from anywhere.

1.  Inside **GameManager**, add the following properties: 

```csharp
// Public references to game states
public IState BootState { get; private set; }
public IState MainMenuState { get; private set; }

````

> [!TIP]  
> Using **`private set`** makes the states **read-only for other classes**, while the GameManager itself can **initialize or modify them internally**.
>

#

### Step 2: Initialize States in Awake()
When the GameManager is created, we want to **instantiate each state**. This ensures that all states are ready before the game starts.

1. Add the following to the **`Awake()`** method:
```csharp
// Initialize Game States
BootState = new BootState();
MainMenuState = new MainMenuState();

```

> [!NOTE]  
> Instantiating states in `Awake()` guarantees **all references are ready before Start() runs**.
>


#

### Step 3:  Start With the BootState

Now that the states are initialized, we want the game to **begin in a known state**, usually the **BootState**.

1.  Add the following to the **`Start()`** method:
```csharp
    // Start is called once before the first Update
    private void Start()
    {
        PushState(BootState);
        
    } //end Start()
```

> [!TIP]  
> This **pushes the BootState onto the stack** and automatically calls its **`Enter()`** method, starting the game flow.
>

#

### Step 4:  Validate the Active State

To help debug which state is active, we can **track the top of the state stack in the Inspector**. Since this is for **editor-only debugging**, wrap it in `#if UNITY_EDITOR`.

1. Add a **serialized field** to record the active (top) game state
```csharp
#if UNITY_EDITOR
[Header("Debug Info")]
[SerializeField] private string _activeTopState;
#endif
```

2. Create a method to **update this field whenever the state changes**:

```csharp
#if UNITY_EDITOR
private void ValidateStateChange()
{
    _activeTopState = CurrentState?.GetType().Name ?? "Empty";
}
#endif

```

3. Call **`ValidateStateChange()`** inside **`PushState()`**:

```csharp
public void PushState(IState newState)
{
    _stateStack.Push(newState);
    newState.Enter();

    #if UNITY_EDITOR
    ValidateStateChange();
    #endif
}
```

> [!TIP]
> Wrapping debug code in **`#if UNITY_EDITOR`** ensures it **only exists in the editor** and is **stripped out of builds**, keeping runtime performance clean.
>

> #### 💾 Save & Commit
> - Save your script
> - Commit your changes with the message:
>    - *feat: GameManager game state initialization*
> - **Push** changes to GitHub
>

#

### Step 5:  🎮 Playtest Game States
Now that the **GameManager** and basic states are implemented, let’s make sure everything is **connected and running correctly**.
1.  **Open the BootScene** in Unity.
2.  Make sure the **GameManager prefab** is in the scene.
3.  Press **Play** to run the scene.
4.  Observe the following:
    -   The **BootState is active first**, confirmed by the console showing the `Enter()` debug log.
    -   The **GameManager inspector** displays the **Active Top State** as **BootState**.
    -   Next the **BootState exits** (`Exit()` debug log), and the **MainMenuState enters** (`Enter()` debug log).
    -   The **MainMenuState** is now the **Active Top State** in the inspector.      
    -   No errors or warnings appear in the **Console** during the state transitions.
        
> [!TIP]  
> The console logs and inspector validation give immediate feedback that your state system is working correctly, even though no visual changes are present yet.
> 
# 

### 🐞 BUG FIX - Troubleshooting Checklist
Something not working as expected? Check the following:
-   **Console logs don’t show state transitions**
    -   Verify the **BootState** and **MainMenuState** classes inherit from **BaseGameState**.
    -   Ensure the **GameManager prefab** is present in the scene.
    -   Check that the **PushState()** or **ReplaceStates()** methods are being called.
        
-   **CurrentState is null**
    -   Make sure the **state stack** in GameManager is initialized (**`new Stack<IState>()`**).
    -   Ensure that **BootState** state is **pushed** to the stack in **Start()**.
        
-   **States do not execute**
    -   Confirm that **Update()** in GameManager calls **`CurrentState?.Execute()`**.
    -   Check that your classes properly **override the Execute() method**.
      
-   Use the **Console** to inspect debug messages and errors for clues.
---


# 🎉 New Achievement: Game States Initialized

Your GameManager is now ready to **control the full lifecycle of the game**. Students can now **focus on implementing individual states**, knowing they are properly tracked and validated.

```csharp
using System.Collections.Generic;
using UnityEngine;
 

public class GameManager : Singleton<GameManager>
{
    // Public references to game states
    public IState BootState { get; private set; }
    public IState MainMenuState { get; private set; }   
    
    //New stack for game states
    private Stack<IState> _stateStack = new();

    // Current state value
    public IState CurrentState => _stateStack.Count > 0 ? _stateStack.Peek() : null;
    
    #if UNITY_EDITOR
    [Header("Debug Info")]
    [SerializeField] private string _activeTopState;
    #endif
    
    // Awake is called once on initialization (before Start)
    protected override void Awake()
    {
        //Implement the Awake function of Singleton
        base.Awake();
        
        // Initialize Game States
        BootState = new BootState();
        MainMenuState = new MainMenuState();

    } //end Awake()
 
 
    // Start is called once before the first Update
    private void Start()
    {
        PushState(BootState);
        
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
        
        //Enter the new state
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
        
    }

 
    #if UNITY_EDITOR 
    private void ValidateStateChange()
    {
        _activeTopState = CurrentState?.GetType().Name ?? "Empty";
    }
    #endif
    
 
}//end GameManager
```

## 🛡️ Checkpoint

Key takeaways from this lesson:

-   The **GameManager** now holds **public references** to all core game states, making them easy to access or transition between.
-   **States are initialized in `Awake()`**, ensuring they are ready before the game starts.
-   The game **begins in BootState** by pushing it onto the stack in `Start()`, automatically calling its `Enter()` method.
-   **Debugging the active state** is possible using the `_activeTopState` field in the editor, which tracks the current top of the state stack.
-   Using `#if UNITY_EDITOR` for the debug field and validation ensures **editor-only debug info**, keeping builds clean.
-   **Game states are not MonoBehaviours**; they are **not attached to GameObjects**. The GameManager **controls when they are created, entered, executed, and exited**.
-   With this structure, the GameManager is fully prepared to **manage game state transitions**, while keeping **debugging and validation simple**.





