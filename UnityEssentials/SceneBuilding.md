# ⚒️ Tutorial: Scene Buildings

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Scene Building     | 30 minutes       |   GitHub Desktop, Unity     |

</details>

> [!NOTE]
> Before starting this tutorial, ensure you have completed **[Importing Assets](ImportingAssets.md)** and that your project is synced with the **SceneBuilding** in GitHub Desktop.
> 

---

## Best Practices for Scene Building
In this tutorial, we will be building out our scene; in so doing, we will be implementing the following best practices: 
-   **Hierarchy Organization** – Always keep the hierarchy clean; it’s easier to select, hide, or move large groups of objects.
-   **Empty GameObjects as Containers** – Use empty objects to logically group related items (e.g., all foliage under `Foliage`) rather than having hundreds of loose objects.
-   **Prefabs for Reuse** – Any object or group you might reuse should become a prefab. This allows consistency and faster updates.
-   **Use Snap Tools** – Grid snapping (`V` key) ensures your environment pieces align perfectly.

---

### Step 1 — Organize the Hierarchy for Environment Assets

1.  In the **Hierarchy** window, right-click → **Create Hierarchy Folder**.
    - Name the folder: **Environment**.
    - Set the folder color to **Green**.
    

> [!NOTE]  
> We will place all environment-related assets in this folder as we build out our scene. Organizing your hierarchy at the start makes it easier to find objects, manage complex scenes, and prevent clutter.
>

#

### Step 2 — Build the Park Ground

1.  Using the **Low-Poly Park** package in your **3rdParty** folder, locate the **ground tile prefabs**.
    
2.  Snap the tiles together to create **two large park areas**.
    
    -   Use the **“V” key** to snap tiles to the grid to avoid gaps.
        
3.  Create a new empty GameObject in the **Hierarchy** → name it `ParkGround`.
    
    -   Set its position to `(0,0,0)`.
        
4.  Drag all the ground tiles into the **ParkGround** object in the hierarchy.
    

> [!TIP]  
> Using an empty GameObject like **ParkGround** acts as a **container**. It keeps related objects together, which makes it easier to move, scale, or rotate all the tiles at once without breaking their positions relative to each other.
>


### Step 3 — Create a ParkGround Prefab

1.  In the **Project** window, create a new folder
    - Name it **Prefabs**.
    
2.  Drag the **ParkGround** object from the hierarchy into the **Prefabs** folder.
    

> [!NOTE]  
> Prefabs are **reusable templates** of GameObjects. Once you create a prefab, you can place multiple copies in your scene, and any changes you make to the prefab will automatically update all copies. This is essential for large scenes with repeating elements like tiles, props, or structures.
>


### Step 4 — Organize Other Environment Elements

1.  In the **Environment** folder (hierarchy), create **subfolders** to organize additional scene elements:
    
    -   `Props` – benches, signs, lamps, fountains, etc.
        
    -   `Structures` – buildings, walls, bridges
        
    -   `Playground` – slides, swings, play structures
        
    -   `Foliage` – trees, bushes, grass
        
2.  Drag assets from the **3rdParty** folder into the appropriate hierarchy folder.
    

> [!TIP]  
> Grouping objects in empty GameObjects (folders) helps:
> -   Keep the hierarchy tidy
> -   Make moving or scaling groups of objects easier
> -   Improve performance by allowing you to enable/disable sections of the scene
> -   Create prefabs for repeated elements quickly
>     

3.  Arrange objects to your liking, keeping logical groupings in mind. For example:
    -   Place all benches under a `Benches` empty object inside `Props`.
    -   Group all trees under a `Trees` empty object inside `Foliage`.
        
2.  Create **prefabs** for any grouped objects you want to reuse. For example: 
    - Clusters of trees
    - Rock groupings

> [!NOTE]  
> Prefabs are especially useful for environmental elements you may want to duplicate or adjust later. For example, if you create a small cluster of playground equipment, turning it into a prefab allows you to quickly place identical playground areas in another part of the scene.
>

#


### Step 5 — Save & Commit

1.  Save your scene: **File > Save** or press **Ctrl + S**.
    
2.  Close the Unity editor.
    
3.  Switch back to **GitHub Desktop** → stage your changes.
    
4.  Commit with the message:
    -`feat: initial scene build with environment elements.`

5.  Push your changes to the remote branch: `SceneBuilding`.
