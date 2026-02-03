## Designing Your Game States

Before writing any code, it’s important to **think about the states your game actually needs** and how they relate to each other. Not all states are the same—some represent **exclusive modes**, while others are **temporary overlays** that sit on top of whatever the player is currently doing.

### Scenario: A Simple Adventure Game

Imagine we’re designing a basic adventure game. The player can:

1.  Start the game (Boot)
2.  Navigate a Main Menu
3.  Play the game (Playing)
4.  Pause the game, open Inventory, or interact with a Crafting table
5.  End the game and see a Game Over screen

From this scenario, we can see two categories of states:

#

### Mutually Exclusive States

These states **replace each other completely**. Only one of these can be active at a time. For example:
| State    | Replaces | Reason                                |
| -------- | -------- | ------------------------------------- |
| Boot     | None     | Runs once at startup                  |
| MainMenu | Boot     | Player chooses to start a new session |
| Playing  | MainMenu | The game session begins               |
| GameOver | Playing  | Session ends                          |
| MainMenu | GameOver | Player can start fresh                |

In this flow, the game **cannot be in MainMenu and Playing at the same time**. Each transition is like **closing one chapter and opening another**.

#

### Stacked States (PushState)

Other states are **temporary overlays**. They don’t replace the underlying state—they **pause it, show something on top, and return control when done**. For example:
| State        | Reason                                                           |
| ------------ | ---------------------------------------------------------------- |
| Paused       | Player wants to pause gameplay                                   |
| Inventory    | Player opens inventory without leaving the game                  |
| Crafting     | Player interacts with crafting without leaving the current scene |
| Dialogue     | Temporary interactions with NPCs                                 |
| SettingsMenu | Adjust options while the game continues underneath               |

These overlays are **pushed onto a stack**. When they’re done, they’re **popped off**, and the previous state resumes exactly where it left off.

---

## 🛡️ Checkpoint

By separating **mutually exclusive states** from **stacked overlays**, we can design a system that:

-   Clearly defines the **main flow of the game** (Boot → MainMenu → Playing → GameOver)
    
-   Allows **temporary interruptions** (Paused, Inventory, Dialogue) without losing the underlying state
    
-   Makes it easier to **predict and manage transitions**, keeping gameplay smooth and modular
