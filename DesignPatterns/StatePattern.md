# Game States
In games, characters, objects, and even entire systems don’t just act randomly—they follow **specific behaviors that depend on their current situation**. These behaviors are organized into **states**, which define what an object is doing at any given moment.

For example, a player character can be **Idle**, **Walking**, **Running**, or **Jumping**. An enemy AI might **Patrol**, **Chase**, or **Attack**. At any moment, the object is in **exactly one state**, and that state controls what it can do and how it reacts to inputs or the environment.

Using states in your code has multiple benefits: it **clarifies behavior**, **reduces overlapping logic**, and makes your system **easier to extend or debug**.

---

## Two Ways to Organize States

There are two common approaches to managing states in games: the **Finite State Machine (FSM)** and the **State Pattern**. Both aim to organize behavior cleanly, but they do it in very different ways.

### Finite State Machine (FSM)

An FSM is like a **central switchboard**. You define all the states in one place—often as an `enum`—and a single controller decides how the object behaves in each state.

This works well when states are **simple and limited**. For instance, a player might switch between Idle, Walk, and Run depending on key presses, and the logic can be written in one `Update` function.

**Pros:**

-   Easy to implement
    
-   Centralized control makes debugging straightforward
    
-   Efficient for objects with a small number of states
    

**Cons:**

-   Becomes cluttered as the number of states grows
    
-   Less flexible when objects have unique, independent behavior
    

#### Example: Player FSM
```csharp
public enum PlayerState { Idle, Walk, Run }

public class Player : MonoBehaviour
{
    public PlayerState currentState = PlayerState.Idle;

    void Update()
    {
        switch (currentState)
        {
            case PlayerState.Idle:
                // Play idle animation
                break;
            case PlayerState.Walk:
                // Move player slowly
                break;
            case PlayerState.Run:
                // Move player quickly
                break;
        }//end switch(currentState)

    }//ennd Update()

}//end Player
```

Here, all the behavior lives in one place. Adding a new state requires modifying the central logic, which can be fine for a simple player but messy for more complex systems.

---

### State Pattern

The State Pattern takes a different approach: **each state is its own object**, responsible for its own behavior. Instead of a central controller deciding what to do, the state object itself knows how to act and when to transition to another state.

This approach shines when objects have **many states or complex behaviors**. It’s more modular, easier to extend, and keeps each state **self-contained**, avoiding a monolithic controller.

**Pros:**

-   Modular: each state can evolve independently
    
-   Scales well for complex objects or systems
    
-   Reduces the risk of a central controller becoming too large
    

**Cons:**

-   More complex to set up
    
-   More classes to manage, which may confuse beginners

#### Example: Player State Pattern

```csharp
public interface IState
{
    // read-only property
    string Name { get; }   
    
    // called when the state becomes active
    void Enter();       
    
    // called when the state is popped or replaced
    void Exit();     
    
    // called every frame (or periodically)
    void Execute();       
    
}//end IState
```

---
## 🛡️ Checkpoint: Choosing the Right Approach

-   **FSM** → Simple, predictable behavior, small set of states
    
-   **State Pattern** → Complex behavior, many states, modular and maintainable
    

Think of it like this: **FSM is a switchboard**, **State Pattern is a team of specialists**. Both get the job done, but the **scale and complexity** of your system determine which is best.
