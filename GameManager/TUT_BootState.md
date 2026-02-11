# 🧭 **Adventure: BootState in Camp Craft**  
By: Akram Taghavi-Burris | © 2026

Now that we have created the **GameManager** and the basic state system, it’s time to configure our **BootState** for Camp Craft. The BootState is the **first state that runs when the game starts**, and it sets the stage for the player’s adventure.

In Camp Craft, when the game launches, we want the following to happen:
1.  The **BootState activates** as the first state of the game.
2.  A **loading visual** appears so the player knows the game is starting.
3.  The **MainMenu scene loads in the background**, preparing the menus for interaction.
4.  Once the **minimum display time** has elapsed and the scene is mostly loaded, the **game automatically transitions** to the MainMenuState.
5.  Any **temporary loading visuals** are cleaned up so the scene is ready for the player to explore and craft.
    
> [!TIP]  
> This scenario gives us a **practical example** of how a BootState works in a real game. It also ensures the player has **feedback while loading**, keeping the startup experience smooth and professional.
>

 ## Loading Assets in Non-MonoBehaviour States
One important thing to remember is that **Game States are not MonoBehaviours**.  
That means they are **not attached to any GameObject**, and they **cannot reference scene objects directly** through the Inspector.

So in Camp Craft, when our BootState needs to display a **loading visual**, we can’t just drag a prefab into a serialized field like we normally would in Unity.

To solve this and be able to **load a prefab through code**, we our loading prefab  will need to live inside Unity’s special:
**_Assets/Resources/ folder_**

Unity treats the Resources folder differently from normal folders. Anything inside it can be loaded at runtime using a string path, using the **`Resources.Load`** method.

> [!NOTE]
> In professional projects, many teams avoid **heavy use of Resources** and prefer systems like **Addressables**.
> However, for simple game architecture and prototyping, Resources is a simple and effective solution.
> 

---


# ⚒️ Enhancing the BootState
<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Game Development | 20-30 minutes        |   Unity, IDE, GameManager, BootState |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed **[Initalizing Game States](TUT_InitializeGameStates.md)** tutorial  
> - Ensure you are on the **GameManager** branch
>   
---

### Step 1: Set Up the Boot Scene
1.  Open your **BootScene** in the Unity Editor.
2.  Add the **GameManager prefab** to the scene if it isn’t already there.
    -   This ensures the **GameManager exists at runtime** to manage states.
3.  Make sure your **BootScene** and **MainMenuScene** are included in the **Build Settings**:
    -   Go to **File > Build Profiles → Scenes List**
    -   Add **BootScene** and **MainMenuScene** if missing.
    -   Ensure that the **BootScene** is the first scene in the list.
        
#

### Step 2: Create a Loading Prefab
To visualize the BootState, we’ll use a **simple rotating cube** as our _loading prefab_.
1.  Create a new **Cube** in the scene.
2.  Rename the cube in the inspector to **LoadingCube**.
3.  Customize it (color, size, etc.) to represent a loading indicator.
4.  Add a class component to make it rotate (e.g., RotateTransform class)
5.  Drag the Cube into **Assets/Resources/Prefabs/**.
    -   The **Resources folder** allows us to **load assets at runtime by path** using `Resources.Load<GameObject>()`.
6. Remove the prefab from the scene
7. **Save** the scene
        
> [!NOTE]  
> The prefab is **not placed in the scene** at design time;  it is **instantiated dynamically** by the BootState when the game runs.
>

# 

### Step 3: Modifying the BootState

Now that our **BootScene** is set up with the **GameManager** and the **loading prefab**, we can modify the **BootState** class itself. 

Currently, the state is just scfolding, but now we can define the tasks for the bootstate, including spawning the loading visual, loading the MainMenu scene in the background, and controlling when the game transitions to the next state.

1. Open **BootState.cs** in your IDE.

#

### Step 4: Using SceneManagement
Since the **BootState** will load the **Main Menu** scene before switching states, we need to include the namespace to **`UnityEngine.SceneManagement`** to use the Unity **SceneManager** methods. 

1. At the top of the class, include the following: 
```csharp
using UnityEngine;
using UnityEngine.SceneManagement;  // Required for SceneManager
```
#

### Step 5: Declare Fields and Define Constants

1. Declare the fields for referencing the loading prefab

```csharp
// Reference to the loading prefab GAME OBJECT
private GameObject _loadingPrefab;

// Reference to the instantiated prefab in the scene
private GameObject _prefabInstance;

```

2. Set the path to the prefab
```csharp

// Path to the prefab in the Resources folder
private const string LOADING_PREFAB_PATH = "Prefabs/LoadingCube";
```
>[!Note]
>The **Resources folder** allows us to load assets dynamically at runtime using `Resources.Load<GameObject>()`.  
> Our loading prefab lives here so the BootState can instantiate it **without being attached to a GameObject in the scene**.  
> The **`LOADING_PREFAB_PATH`** must match the **exact subfolder and file name** of the prefab inside the **Resources folder**.  
> For example, if the prefab is located at **_Assets/Resources/Prefabs/LoadingCube.prefab_**, the path is **_Prefabs/LoadingCube_**.
>

3. Set the minimum time for the loading prefab/animation to display

```csharp
// Minimum time the loading screen should display
private const float MIN_DISPLAY_TIME = 2.0f;
```

4. Set the name of the **main menu scene**
   
```csharp
// Name of the MainMenu scene to load
private const string MENU_SCENE_NAME = "MainMenu";
```
> [!IMPORTANT]  
> Because our **game states are not MonoBehaviours**, we **cannot use serialized fields** to set the scene name in the Inspector.  
> This means the BootState must **explicitly know the name of the scene it will load**, which is why we define **`MENU_SCENE_NAME`** as a **constant**.  
> Once established, this value **should not be changed**, and it should be **documented** in your game design documents.
> 

5. Create a field to keep track of the scene progress.
   
```csharp
// Reference to the scene loading operation
private AsyncOperation _loadingOperation;
```
>[!NOTE]
> The `_loadingOperation` field **does not hold the scene itself**, but instead **tracks the progress of the scene loading process**. When you load a scene asynchronously with `SceneManager.LoadSceneAsync()`, Unity returns an **`AsyncOperation` object**. This object allows you to:
> -   **Check how far the scene has loaded** via the `progress` property (0 to 0.9 before activation).
> -   **Control when the scene becomes active** using `allowSceneActivation`.
> -   Optionally, listen for completion using `completed` callbacks.
>   

6. Create a field to keep track of the time passed while in BootState
   
```csharp
// Time elapsed since entering the BootState
private float _elapsedTime = 0f;
```

> [!NOTE]  
> The **Resources folder** allows us to load assets dynamically at runtime with `Resources.Load<GameObject>()`.
>

#

### Step 6: Create SpawnLoadingPrefab()
We need a method that is responsible for creating the **loading visual** that the player sees during the BootState. In Camp Craft, this will be our rotating cube prefab.

1. Create a method named **`SpawnLoadingPrefab()`**
```csharp
private void SpawnLoadingPrefab()
{
    
} //end SpwanLoadingPrefab()

```

2. Load the Prefab From the Resources Folder

```csharp
// Locate the loading prefab GAME OBJECT
_loadingPrefab = Resources.Load<GameObject>(LOADING_PREFAB_PATH);

```

3. Check If the Prefab Was Found
   - If it is not null, then instate the prefab
```csharp

/// If prefab GAME OBJECT located 
if (_loadingPrefab != null)
{
    // Instantiate an INSTANCE of the prefab into the scene
    _prefabInstance = GameObject.Instantiate(_loadingPrefab);

}

```

4. Create a placeholder if the prefab can not be found
```csharp
else
        {
            Debug.LogWarning("LoadingCube prefab missing. Spawning a placeholder cube instead.");
            
            // Create an INSTANCE of a primitive cube so the player isn't staring at a blank screen
            _prefabInstance = GameObject.CreatePrimitive(PrimitiveType.Cube);
            _prefabInstance.transform.position = Vector3.zero;
            
        }//end if(prefab != null)


```

5. The final **SpawnLoadingPrefab()** should appear as below:

```csharp
private void SpawnLoadingPrefab()
{
    // Locate the loading prefab GAME OBJECT
    _loadingPrefab = Resources.Load<GameObject>(LOADING_PREFAB_PATH);

    // If prefab GAME OBJECT located
    if (_loadingPrefab != null)
    {
        // Instantiate an INSTANCE of the prefab into the scene
        _prefabInstance = GameObject.Instantiate(_loadingPrefab);
    }
    else
    {
        Debug.LogWarning("LoadingCube prefab missing. Spawning a placeholder cube instead.");

        // Create an INSTANCE of a primitive cube so the player isn't staring at a blank screen
        _prefabInstance = GameObject.CreatePrimitive(PrimitiveType.Cube);
        _prefabInstance.transform.position = Vector3.zero;
    } //end if(prefab != null)

} //end SpawnLoadingPrefab()

```

#

### Step 7:  Implement Enter() Logic
Modify the **`Enter()`** method by: 

1. Adding a call to the **`SpawnLoadingPrefab()`** method
   
```csharp

public override void Enter()
    {
        Debug.Log($"Entering {Name} State");
        
        // Setup loading prefab
        SpawnLoadingPrefab();
        
```
2. Set the operation for **`_loadingOperation`** by:
    - Loading the Main Menu scene _asynchronously_ in the background
    - Setting the scene to load _additively_
      
```csharp
// Load MainMenu scene additively in the background
_loadingOperation = SceneManager.LoadSceneAsync(MENU_SCENE_NAME, LoadSceneMode.Additive);
```
> [!TIP]
> **`LoadSceneMode.Additive`** means BootState stays active while the MainMenu scene loads.
> 

3. Prevent the Scene From Activating Automatically
By default, Unity will switch to the scene as soon as it finishes loading.
However, we want to control that ourselves:
 
```csharp
    // Prevents the scene from switching automatically
    _loadingOperation.allowSceneActivation = false;

} //end Enter()

```
4. The final **`Enter()`** method should appear as below: 
```csharp

   public override void Enter()
    {
        Debug.Log($"Entering {Name} State");
        
        // Setup loading prefab
        SpawnLoadingPrefab();
        
        
        // Load MainMenu scene additively in the background
        _loadingOperation = SceneManager.LoadSceneAsync(MENU_SCENE_NAME, LoadSceneMode.Additive);
        
        // Prevents the scene from switching automatically
        _loadingOperation.allowSceneActivation = false;

    } //end Enter()
```

# 

### Step 8: Implement `Execute()` Logic

The **`Execute()`** method runs **every frame** while the BootState is active. 

1. Remove any previous debugging/test logic in the **` Execute()`** method.

1.  **Track elapsed time**
    -   Increment the `_elapsedTime` variable using the time since the last frame.
      
```csharp
public override void Execute()
    {
        _elapsedTime += Time.deltaTime;
```

2.  **Check if loading is complete and minimum display time has passed**
    -   Ensure the **`_loadingOperation`** is not null.
    -   Confirm the scene has loaded **at least 90%**.
    -   Verify that **`_elapsedTime`** is greater than or equal to **`MIN_DISPLAY_TIME`**.
3.  **Transition to the MainMenuState when ready**
    - If check is true (_see previous step_)
       - Call **`ReplaceStates()`**  on the **GameManager**
          - to exit **BootState**, clean up visuals, and activate the **MainMenuState**.
  
```csharp

        // Checks if the scene is not null, and has loaded more than 90%,
        // and the elapsed time is greater than the Minimum display time
        // If true, replace the boot state with the main menu state
        if (_loadingOperation != null && _loadingOperation.progress >= 0.9f && _elapsedTime >= MIN_DISPLAY_TIME)
        {
            // This triggers our Exit(), where we clean up and flip the switch
            _gm.ReplaceStates(_gm.MainMenuState);
        }

    }//end Execute()
```

> [!TIP]  
> This ensures the player sees a loading visual for a minimum duration while the scene loads in the background. The transition only happens when both conditions are satisfied, preventing abrupt scene switches.
>

4. The final **`Execute()`** method should appear as below:

```csharp
    public override void Execute()
    {
        _elapsedTime += Time.deltaTime;
        
         // Checks if the scene is not null, and has loaded more than 90%,
         // and the elapsed time is greater than the Minimum display time
         // If true, replace the boot state with the main menu state
        if (_loadingOperation != null && _loadingOperation.progress >= 0.9f && _elapsedTime >= MIN_DISPLAY_TIME)
        {
            // This triggers our Exit(), where we clean up and flip the switch
            _gm.ReplaceStates(_gm.MainMenuState);
        }
        
    }//end Execute()
```


#

### Step 9: Implement `Exit()` Logic

The **`Exit()`** method runs **once when the BootState is leaving**. It handles cleaning up and finalizing the transition to the next state. Update **`Exit()`** method: 

1.  **Remove loading visuals**
    -   Check if the `_prefabInstance` exists.
    -   Destroy the instance so it no longer appears in the scene.
        
```csharp
public override void Exit()
    {
        Debug.Log($"Exiting {Name} State");

        // Remove loading visuals
        if (_prefabInstance != null) GameObject.Destroy(_prefabInstance);

```
    
2.  **Activate the loaded scene**
    -   Check if `_loadingOperation` exists.
    -   Set `allowSceneActivation = true` to reveal the MainMenu scene that was loaded additively in the background.
        
```csharp
        // Reveal the additive scene
        if (_loadingOperation != null) _loadingOperation.allowSceneActivation = true;

    }//end Exit()
```

3. The final **`Exit()`** method should appear as below:
```csharp
    public override void Exit()
    {
        Debug.Log($"Exiting {Name} State");
        
        // Remove loading visuals
        if (_prefabInstance != null) GameObject.Destroy(_prefabInstance);
        
        // Reveal the additive scene
        if (_loadingOperation != null) _loadingOperation.allowSceneActivation = true;

    }//end Exit()

```

> #### 💾 Save & Commit
> - Save all your scripts
> - Commit your changes with the message:
>    - *feat: Added Bootstate logic*
> - **Push** changes to GitHub
>

# 

### Step 10 — 🎮 Playtest Game States

Now that our **BootState** logic is implemented, it’s time to see our **GameManager** system in action.

1.  **Open the BootScene** in Unity.
2.  Ensure the **GameManager prefab** is in the scene.
3.  Press **Play** to run the scene.
4.  Observe the following:
    -   The **loading cube** appears, giving feedback that the game is starting.
    -   The **MainMenu scene loads in the background** while BootState is active.
    -   After the **minimum display time** and scene load, the BootState **transitions automatically** to MainMenuState.
    -   The **loading cube is destroyed**, and the MainMenu is now active.
  
#

### 🐞 BUG FIX - Troubleshooting Checklist 
Experiencing issues? Try the following:

- **Loading cube does not appear**  
    - Check that **`LOADING_PREFAB_PATH`** points to the correct prefab.  
    - Make sure the prefab exists in **Assets/Resources/Prefabs/**.

- **MainMenu scene does not load**  
    - Ensure the scene is included in **Build Settings → Scenes In Build**.  
    - Verify that **`MENU_SCENE_NAME`** matches the **exact spelling** of the scene name.

- **Scene activates too early**  
    - Confirm that **`_loadingOperation.allowSceneActivation = false`** in **`Enter()`**.

- Use the **Console** to check for warnings or errors during the transition.


---

# 🎉 New Achievement: BootState Launched

You’ve successfully set up the **BootState** for Camp Craft. This state now handles a simple loading sequence with a visual indicator and prepares the **MainMenu scene** in the background. With this, your game has a **working entry point** and demonstrates how game states manage transitions and initialization logic.

```csharp
using UnityEngine;
using UnityEngine.SceneManagement; // Required for SceneManager

public class BootState : BaseGameState
{
    public override string Name => "Boot";
        
    // Reference to the loading prefab GAME OBJECT
    private GameObject _loadingPrefab;
    
    //Reference to the loading prefab INSTANCE in the scene
    private GameObject _prefabInstance;
    
    private const string LOADING_PREFAB_PATH = "Prefabs/LoadingCube";
    private const float MIN_DISPLAY_TIME = 2.0f;
    
    // Main Menu scene to load
    private const string MENU_SCENE_NAME = "MainMenu";
    
    // Reference to the scene loading operation
    private AsyncOperation _loadingOperation;
    
    // Time that has elapsed since the Boot State started
    private float _elapsedTime = 0f; 
    

    public override void Enter()
    {
        Debug.Log($"Entering {Name} State");
        
        // Setup loading prefab
        SpawnLoadingPrefab();
        
        
        // Load MainMenu scene additively in the background
        _loadingOperation = SceneManager.LoadSceneAsync(MENU_SCENE_NAME, LoadSceneMode.Additive);
        
        // Prevents the scene from switching automatically
        _loadingOperation.allowSceneActivation = false;

    } //end Enter()

    public override void Execute()
    {
        _elapsedTime += Time.deltaTime;

        // Spin cube...
        
         // Checks if the scene is not null, and has loaded more than 90%, 
         // and the elapsed time is greater than the Minimum display time
         // If true, replace the boot state with the main menu state
        if (_loadingOperation != null && _loadingOperation.progress >= 0.9f && _elapsedTime >= MIN_DISPLAY_TIME)
        {
            // This triggers our Exit(), where we clean up and flip the switch
            _gm.ReplaceStates(_gm.MainMenuState);
        }
        
    }//end Execute()
    

    public override void Exit()
    {
        Debug.Log($"Exiting {Name} State");
        
        // Remove loading visuals
        if (_prefabInstance != null) GameObject.Destroy(_prefabInstance);
        
        // Reveal the additive scene
        if (_loadingOperation != null) _loadingOperation.allowSceneActivation = true;

    }//end Exit()


    private void SpawnLoadingPrefab()
    {
        // Locate the loading prefab GAME OBJECT
        _loadingPrefab = Resources.Load<GameObject>(LOADING_PREFAB_PATH);
    
        // If prefab GAME OBJECT located
        if (_loadingPrefab != null)
        {
            // Instantiate an INSTANCE of the prefab into the scene
            _prefabInstance = GameObject.Instantiate(_loadingPrefab);
        }
        else
        {
            Debug.LogWarning("LoadingCube prefab missing. Spawning a placeholder cube instead.");
            
            // Create an INSTANCE of a primitive cube so the player isn't staring at a blank screen
            _prefabInstance = GameObject.CreatePrimitive(PrimitiveType.Cube);
            _prefabInstance.transform.position = Vector3.zero;
            
        }//end if(prefab != null)
        
    }//end SpawnLoadingPrefab()
 
 
}//end BootState

```

When ready, you can **expand BootState** with more complex startup tasks, or use it as a template for creating other game states, like **PlayingState** or **PausedState**.

## 🚩 Checkpoint
Key takeaways from this lesson:
-   The **BootState** handles the **first actions when the game starts**, giving the player feedback while the game loads.
-   **`Enter()`** sets up the state: it **spawns loading visuals** and **begins loading the MainMenu scene asynchronously**.
-   **Loading visuals** can be dynamically spawned from the **Resources folder** to provide immediate feedback.
-   **Asynchronous scene loading** with **LoadSceneMode.Additive** allows BootState to run while preparing the MainMenu scene.
-   **`_loadingOperation.allowSceneActivation = false`** gives you control over exactly when the next state becomes active.
-   **`Execute()`** monitors progress and elapsed time, triggering the transition automatically when conditions are met.
-   **`Exit()`** cleans up temporary objects and completes the scene activation, keeping your game clean and organized.
-   **Game states are not MonoBehaviours**; the **GameManager** creates, updates, and switches states.
-   Following this structure ensures your states are **modular, testable, and easy to extend** for future gameplay features.







