# 📜 GameManager System
> By: Akram Taghavi-Burris | © 2026

In our previous lesson, we discussed key considerations for designing game states and the decisions that should be made before implementation. Now, we will begin implementing those design choices by building a more complete Game Manager and Game State system.

It’s important to note that there are many valid ways to construct a Game Manager and state system, and the best approach depends on the needs and scale of the game. The method used in this tutorial is intended primarily as a teaching tool, helping illustrate concepts such as the **Singleton pattern**, **interfaces**, the **State Patter**, and how to structure systems using **SOLID principles**.


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

### Step 1: Implement the IState Interface

All game states will implement the `IState` interface we discussed:

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

This ensures **every state follows a consistent lifecycle** and can be handled uniformly by the GameManager.

#
### Step 2: Create the Main Menu State

We’ll start with the **MainMenuState**, one of our mutually exclusive states. This state will **display the menu, handle navigation, and transition to gameplay** when the player starts a new session.

```csharp
using UnityEngine;

public class MainMenuState : IState
{
    public string Name => "MainMenu";

    public void Enter()
    {
        Debug.Log($"Entering {Name} State");
        // TODO: Show main menu UI
        // TODO: Play menu music, pause gameplay systems if necessary

    }//end Enter()

    public void Execute()
    {
        // TODO: Handle menu navigation, button selection

    }//end Execute()

    public void Exit()
    {
        Debug.Log($"Exiting {Name} State");
        // TODO: Hide main menu UI, stop menu music

    }//end Exit()
}

```
---

## Adding State Management to the GameManager

Now that we’ve defined what a **state** is and identified the **states our game needs**, we can teach the `GameManager` how to _control_ those states.

To do that, we need three things:

1.  A reference to the **current active state**
2.  A way to **replace** states (for major game phases)
3.  A way to **temporarily interrupt** states and then return to them

# 
    
### Step 1: Tracking the Active State

First, the GameManager needs to know **which state is currently running**.

```csharp
private IState currentState;
```

This reference tells us:

-   Which state should receive `Execute()` calls
    
-   Which state should be exited when a transition occurs
    

At any given moment, **only one state is “active”**, even if others are waiting underneath.

# 

### Step 2: Why We Need a Stack

Earlier, we decided that some states:

-   **Replace** each other (MainMenu → Playing)
    
-   **Stack on top of each other** (Paused over Playing)
    

To support stacked states, we need a structure that can:

-   Remember the previous state
    
-   Restore it later
    
-   Preserve the order of interruptions
    

This is exactly what a **stack** is designed for.

```csharp
private Stack<IState> stateStack = new Stack<IState>();
```
A stack works on a **Last In, First Out (LIFO)** principle:

-   The **most recent state pushed** is the first one removed
    
-   This mirrors how pause menus, inventories, and dialogue work
    

>[!NOTE]
> _If you pause the game, then open inventory, closing inventory should return you to pause—not directly back to gameplay._

#

### Step 3: Running the Active Game State

With our states defined and transitions in place, we now need to decide **where execution actually happens**. In this architecture, the **GameManager owns the game loop**, and states do not update themselves.

> [!TIP]
> A helpful way to think about this system is:
> - **GameManager** → owns the game loop
> - **Active State** → controls behavior for this frame
> - **Stacked States** → stored, suspended, waiting to resume
>   
> Only the state at the top of the stack is ever allowed to execute.

Every frame, the GameManager forwards control to **exactly one state**:
```csharp
void Update()
{
    currentState?.Execute();
}
```
#### What Is Being Executed?

Only the **currently active state**—the state at the top of the stack—receives an `Execute()` call.

-   If the game is in `PlayingState`, then `PlayingState.Execute()` runs.
-   If the player pauses the game, `PausedState` becomes active, and **only** `PausedState.Execute()` runs.
-   Any states beneath the active state are **suspended**, not executing.

This ensures that the game is never “half-playing and half-paused” at the same time.

>[!IMPORTANT]
>By keeping execution centralized in the GameManager:
> - Only **one state runs per frame**
> - Execution order is explicit and predictable
> - State stacking behaves consistently
> - Pausing and overlays work naturally
>  
> States describe **how to behave**, but the GameManager decides **when they are allowed to behave**.

#

### Step 4: Replacing a State (ChangeState)

Some transitions represent **major shifts in game mode**.  
These states should **never coexist**.

Examples:

-   MainMenu → Playing
-   Playing → GameOver
    
For these transitions, we **replace** the current state.

```csharp
public void ChangeState(IState newState)
{
    currentState?.Exit();
    currentState = newState;
    currentState.Enter();
}
```
What happens here, step by step:

1.  The current state cleans itself up (`Exit`)
2.  The reference is replaced
3.  The new state initializes (`Enter`)

Once replaced, the old state is **gone forever**.


### Step 5: Stacking a Temporary State (PushState)

Some states are **interruptions**, not replacements.

Examples:

-   Paused
    
-   Inventory
    
-   Dialogue
    

In these cases, we want to:

-   Suspend the current state
    
-   Remember it
    
-   Restore it later
























