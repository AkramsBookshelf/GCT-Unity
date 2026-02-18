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

> [!NOTE]
> Before starting this tutorial, ensure you have :
>  - Completed **[Probuilder Tutorial](ProBuilder.md)**
>  - That you are on the **Animation** branch in GitHub Desktop.
>

### Step 1 — Prepare the Gate GameObject
1.  Open the **ParkGame-Unity** project
2.  In the **Hierarchy**, locate the gate separating **Area One** from **Area Two**.
3.  Rename the GameObject to:
    -   **Gate_Interactable**
        
> [!TIP]  
> Clear and descriptive names help avoid confusion when adding animations or scripting behaviors.
> 

#

### Step 2 — Open the Animation Window
1.  In the top menu, go to:
    -   **Window > Animation > Animation**
        
2.  The **Animation window** will open.
3.  With **Gate_Interactable** selected, in the **Animation** window press **Create** to create a new animation.
4.  Save the animation in a new folder named:
    -   `Animation`
        
5.  Name the animation:
    -   **ANM_GateOpen**
        
#

## Step 3 — Add Properties for Rotation
1.  In the Animation window, click **Add Property**.
2.  Select **Gate-Left → Transform → Rotation**
3.  Repeat for **Gate-Right → Transform → Rotation**

> [!NOTE]
> Any property added to the animation track will have a first key frame auto-created based on its current settings.
> 
    
# 

## Step 4 — Record the Open Animation
1.  Press the **Record button** in the Animation window.
2.  Move the **playhead** to frame **10**.
3.  Select **Gate-Left** → in the **Inspector**, set **Y rotation = -90**.
4.  Select **Gate-Right** → in the **Inspector**, set **Y rotation = 270**.
5.  Turn **off** the Record button.

> [!TIP]  
> Recording automatically creates keyframes at the playhead position when the property values for that track are changed.
> 

# 

## Step 5 — Create the Close Animation
1.  In the **Project window**, duplicate **ANM_GateOpen** → rename it **ANM_GateClose**.
2.  In the Animation window:
    -   Draw a selection box around the **first frames** and drag them to frame **15**.
    -   Select the frames at **frame 10** → move them to **frame 0**.
    -   Move frames from frame 15 back to frame 10.
        
> [!NOTE]  
> This effectively **flips the animation**, making the close animation play in reverse.
>

# 

## Step 6 — Add an Animator Component
1.  Select **Gate_Interactable** in the **Hierarchy**.
2.  In the **Inspector**, click **Add Component → Animator**.

# 

## Step 7 — Open the Animator Window
1.  Go to **Window > Animation > Animator**.
2.  The **Animator window** will open.
    
> [!TIP]  
> The Animator defines **states** for the object (Closed, Opening, Open) and controls **transitions** between them based on conditions.
>

# 


## Step 8 — Create Animator States
1.  Right-click in the Animator → **Create State → Empty** → rename: **Closed**
2.  Right-click **Entry** → **Make Transition → Closed**
    -   This sets the gate to start in the closed state.
3.  With **Closed** selected, in the Inspector:
    -   Set **Motion** = `ANM_GateOpen`
    -   Set **Speed = 0** → keeps the animation from playing automatically
        
#

## Step 9 — Create Animator Parameters
1.  In the Animator window, go to the **Parameters tab**.
2.  Click **+ → Bool** → name: `ShouldOpen`
3.  Click **+ → Bool** → name: `IsLocked`
4.  Set both default values to **True**

> [!TIP]  
> Boolean parameters should be phrased as questions (true/false) to make transitions easy to understand.
>

# 

## Step 10 — Add Opening and Open States
1.  Right-click → **Create State → Empty** → rename: **Opening**
2.  Right-click → **Create State → Empty** → rename: **Open**
3.  Right-click **Closed → Make Transition → Opening**
4.  Select **Opening**, in Inspector:
    
    -   Motion = `ANM_GateOpen`
        
    -   Speed = 1
        
5.  Select the **transition arrow (Closed → Opening)**:
    
    -   Uncheck **Has Exit Time**
        
    -   Add conditions:
        
        -   `IsLocked = False`
            
        -   `ShouldOpen = True`
            
6.  Right-click **Opening → Make Transition → Open**
    
7.  Select **Open**, in Inspector:
    
    -   Motion = `ANM_GateClose`
        
    -   Speed = 1
        
8.  Select **transition arrow (Opening → Open)**:
    
    -   Uncheck **Has Exit Time**
        
    -   Condition: `ShouldOpen = False`
        
9.  Right-click **Open → Make Transition → Closed**
    
10.  Select the transition arrow → **Check Exit Time** = True
    
    -   Set Exit Time = 1 → ensures the animation finishes before returning to Closed
        

> \[!TIP\]  
> Setting exit time ensures animations complete fully before transitioning to another state.

## Step 11 — Test the Gate Animation

1.  Press **Play** in Unity.
    
2.  In the **Scene view**, locate **Gate\_Interactable**.
    
3.  In the Animator window:
    
    -   Set **IsLocked = False** → gate opens automatically.
        
    -   Set **ShouldOpen = False** → gate closes automatically.
        
4.  Exit Play mode.
    

> \[!NOTE\]  
> This verifies that the **Animator state machine and conditions work correctly**.

## Step 12 — Create a Prefab Variant

1.  Drag **Gate\_Interactable** from Hierarchy → into **Prefabs folder**.
    
2.  When prompted:
    
    -   Select **Create Prefab Variant**
        
3.  Rename the variant back to **Gate\_Interactable**
    

> \[!TIP\]  
> A **prefab variant** inherits the original prefab’s properties but allows **scene-specific adjustments**. This is why we create a variant: we can reuse it while keeping unique animations or parameters for each instance.

## Step 13 — Add Additional Gates

1.  Drag another **Gate\_Interactable** prefab into the scene.
    
2.  Place it as the **second gate** to interact with in your park.
    

> \[!TIP\]  
> Using prefabs and variants allows you to **reuse the same gate logic and animation** without duplicating setup work.

## Step 14 — Save & Commit

1.  Save the scene: **File > Save** or **Ctrl + S**
    
2.  Close Unity.
    
3.  In **GitHub Desktop**: stage your changes
    
4.  Commit with message:
    
    -   `feat: added Gate Animator and open/close animations`
        
5.  Push changes to the **SceneBuilding** branch

