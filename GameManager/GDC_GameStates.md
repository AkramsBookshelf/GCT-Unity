# 🏹 Game Design Challenge: Expand Your Game States
> By: Akram Taghavi-Burris | © 2026

Now that we have the **BootState** and the basic **GameManager** system working, it’s time to expand our game states and start creating the **player experience**. In this challenge, you’ll implement the next core states: **MainMenuState, PlayingState, PausedState, and GameOverState**.


# 

## 🎯 Assignment Goals
1.  Create additional **game states** inheriting from **BaseGameState**.
2.  Implement **basic logic for entering, executing, and exiting** each state.
3.  Add **simple scene and UI transitions** to visualize state changes.
4.  Use the **GameManager stack system** to handle state transitions.
5.  Optionally, add **fun effects** (fade, overlays) to make transitions feel more dynamic.

#

## 📝 Assignment Instructions

### Step 1: MainMenuState Setup

1.  Open or create the **MainMenu scene** in your project.
2.  Add a **Canvas** with a simple menu:
    -   A **Play button** to start the game.
        
3.  In **MainMenuState**:
    -   Ensure it inherits from **BaseGameState**.
    -   Override `Enter()`, `Execute()`, and `Exit()`.
        
4.  Connect the **Play button** to **replace the current state with PlayingState**
    - Create a MainMenu script that calls the **GameManager's** **`ReplaceStates()** method
    - Add the script to the Canvas
    - Set an on-click event for the button

### Step 2: PlayingState Setup
1.  Create a new **PlayingState** class inheriting from **BaseGameState**.
2.  In `Enter()`, **load the level scene additively**.
3.  In `Execute()`, listen for **P key**:
    -   If **not paused**, **push** the PauseState.
    -   If **already paused**, **pop** the PauseState.
        

### Step 3: PausedState Setup
1.  Create a new **PausedState** class.
2.  In `Enter()`, **freeze the game** using **`Time.timeScale = 0f;`**
3.  In `Exit()`, **resume the game** using **`Time.timeScale = 1f;`**
   

### Step 4: GameOverState Setup

1.  Create a **GameOverState** class.
2.  In `Enter()`, load a simple **GameOver scene** additively (optional: create a Canvas with a “Game Over” text).
3.  You don’t need complex logic; just demonstrate a state transition.  

> \[!TIP\]  
> This scene could be minimal—a black background with “Game Over” text is fine for now.
> 

#

---
> #### 🎮 Playtest
> **Periodically** playtest your progress by saving all your scripts and scenes. Switch back to Unity and press **_Play_** to test your implementation. Verify that the object behaves as expected.
> 
---
### 🐞 BUG FIX - Troubleshooting Checklist

-   **State transitions don’t occur**
    -   Ensure states inherit from **BaseGameState**.
    -   Verify **GameManager references** to states are initialized.
        
-   **Game does not pause**
    -   Confirm `Time.timeScale = 0f` in `Enter()` and `Time.timeScale = 1f` in `Exit()`.
    -   Validate key check on Playing state
        
-   **Scenes do not load**
    -   Check that all scenes are in **Build Settings → Scenes In Build**.
    -   Verify **scene names match constants** exactly.
        
-   **Canvas/Buttons not responding**
    -   Confirm **EventSystem** exists in the scene.
    -   Ensure buttons are hooked up to the correct **GameManager calls**.
        


---
> #### 💾 Save & Commit
> 
> -   Save all your scripts and scenes often.
>     
> -   Commit your changes after each step
>     -   _feat: Added MainMenu, Playing, Paused, GameOver states_
>         
> -   **Push** to GitHub
>    - When done working for the period/day
>    - When a major feature/step has been completed
>     
---



# 🏰 Level Up Your States — Optional Enhancements
Now that your core states are working, it’s time to **level up your game state skills**. 

### 🛡️ Knight Rank (Required)
      -   All core states function correctly: Boot, MainMenu, Playing, Paused, GameOver.
      -   Transitions occur as expected
      -   **GameManager stack** reflects the active state.
    
### 🌟 Hero Rank (Optional / Extra Credit): Add visual polish to make the state transitions more engaging:
        -   **MainMenu → PlayingState:** Add a fade-out or animation for the menu.
        -   **Playing → GameOverState:** Add a flash, fade-in, or other effect when transitioning.
        -   **PausedState overlay:** Dim or gray out the current scene to indicate the game is paused.

> [!CAUTION]
>  Extra credit is only earned **after the core functionality works**. Focus on getting your states fully functional before adding flair!
> 
