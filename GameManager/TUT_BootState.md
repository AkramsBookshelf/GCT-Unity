🧭 **Adventure: BootState in Camp Craft**  
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

Now that our **BootScene** is set up with the **GameManager** and the **loading prefab**, we can modify the **BootState** class itself. Currently, the state is just scfolding, but now we can define the tasks for the bootstate, including spawning the loading visual, loading the MainMenu scene in the background, and controlling when the game transitions to the next state.

1. Open **BootState.cs** in your IDE.

### Step 4: Using SceneManagement
Since the **BootState** will load the **Main Menu** scene before switching states, we need to include the namespace to **`UnityEngine.SceneManagement`** to use the Unity **SceneManager** methods. 

1. At the top of the class, include the following: 
```csharp
using UnityEngine;
using UnityEngine.SceneManagement;  // Required for SceneManager
```
#

### Step 5: Add Prefab and Scene References
```csharp
// Reference to the loading prefab GAME OBJECT
private GameObject _loadingPrefab;

// Reference to the instantiated prefab in the scene
private GameObject _prefabInstance;

// Path to the prefab in the Resources folder
private const string LOADING_PREFAB_PATH = "Prefabs/LoadingCube";

// Minimum time the loading screen should display
private const float MIN_DISPLAY_TIME = 2.0f;

// Name of the MainMenu scene to load
private const string MENU_SCENE_NAME = "MainMenu";

// Reference to the scene loading operation
private AsyncOperation _loadingOperation;

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

### Step 6:  Implement Enter() Logic
The **`Enter()`** method runs once when the BootState becomes active.
Modify the method by: 

1. Spawn the Loading Prefab
```csharp

public override void Enter()
    {
        Debug.Log($"Entering {Name} State");
        
        // Setup loading prefab
        SpawnLoadingPrefab();
        
```
2. Load the MainMenu Scene Additively
Now we want to begin loading the main menu scene in the background, while BootState continues running:

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



















### Step 3 — Fallback Visuals
It's not uncommon for art assets to not be fully completed or implemented in a project at the same time that the developer may need to test certain aspects of the game. If the loading prefab from the previous step hadn't been created or not placed in the Resources folder we will run into issues, that are techincally not realted to our actual game manager behaviors in general. 

To avoid these unfor seen events we can create some simple visuals via code. 


`_prefabInstance = GameObject.CreatePrimitive(PrimitiveType.Cube); _prefabInstance.transform.position = Vector3.zero;`

> \[!TIP\]  
> This ensures the player isn’t staring at a blank screen while the game loads, even if the prefab is missing. Always **plan for fallback visuals** during early state testing.
