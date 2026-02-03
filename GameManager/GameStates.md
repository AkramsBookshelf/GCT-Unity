# 📖 Tutorial: Implementing Game States with the State Pattern

Modern games are rarely linear sequences; they have **modes** that define what the game is doing at a given moment: booting up, displaying the main menu, running gameplay, pausing, or showing overlays like inventory or dialogue.

Using a **simple switch or FSM** can work for very small projects, but as soon as you want to:

-   Run **enter/exit logic** when switching states
    
-   Keep some states **stacked** while others are **mutually exclusive**
    
-   Easily **add or change states without breaking everything**
    

…the **State Pattern** becomes invaluable.

In this tutorial, we’ll implement a **Game States** using the **State Pattern**.

---


## 📜 Understanding Why We Use the State Pattern

A typical FSM often looks like a switch statement:

```csharp
switch(currentState)
{
    case GameState.MainMenu:
        // logic
        break;
    case GameState.Playing:
        // logic
        break;
}
```

This works, but it **doesn’t easily allow Enter/Exit logic per state**, and it **doesn’t scale** when states overlap (like Paused over Playing).

By using the **State Pattern**, each state is its **own class**, responsible for:

-   **Enter()** → called when the state becomes active
    
-   **Exit()** → called when the state is replaced or removed
    
-   **Execute()** → called each frame for ongoing logic
    

This design allows **clean transitions**, **stacked overlays**, and makes it easier to **extend or modify states** without touching unrelated code.

# 

## 📜  Defining State Behavior

All states will implement the `IState` interface:

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

}//end IState
``

This interface ensures **every state has a consistent lifecycle**, making the GameManager easier to manage.




































