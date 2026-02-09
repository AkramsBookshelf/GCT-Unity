# ⚒️ Tutorial: Using  Probuilder

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Scene Building     | 30 minutes       |   GitHub Desktop, Unity     |

</details>

> [!NOTE]
> Before starting this tutorial, ensure you have completed **[Scene Building](SCeneBuilding.md)** and that your project is synced with the **SceneBuilding** in GitHub Desktop.
> 

---

## Best Practices for ProBuilder Wall Creation

This tutorial will guide you through **creating and editing walls** using ProBuilder while maintaining best practices:

-   **Use Precise Dimensions** – Set pivot points and sizes for consistency.
    
-   **Work in Edit Mode Carefully** – Ensure the correct faces are selected before extrusion.
    
-   **Hierarchy Organization** – Parent multiple objects to keep the scene tidy.
    
-   **Prefab Usage** – Convert complex or reusable objects into prefabs to speed up scene building.

---

### Step 1 — Create a Basic Cube
1.  Click and **hold on the CC Icon** in the main toolbar of the scene view.
2.  In the pop-up dialog, select **Create Cube**.
3.  Click and drag in the scene view to create a cube. **Do not worry about exact dimensions yet.**
    

> [!NOTE]  
> You can adjust the size later, so initial placement is just for convenience.

#

### Step 2 — Set Cube Shape Settings
1.  With the cube in **creation mode**, open the **Shape Settings** window in the scene view.
2.  Set the following settings:
    -   **Pivot:** Center
    -   **Size:** X = 0.125, Y = 0.125, Z = 1
3.  Press **Q** to exit creation mode and return to pan mode.
   
> [!TIP]  
> If you accidentally clicked away or mistyped settings, select the cube in the **Hierarchy** → in the **Inspector**, click **Edit Shape** under the ProBuilder Shape component.

1.  Rename the cube to **Wall** in the Inspector.
    
#

### Step 3 — Enter ProBuilder Edit Mode
1.  Click and **hold on the object icon** in the toolbar → select **ProBuilder** from the pop-up.
2.  The **ProBuilder Modify Toolbar** appears at the top of the scene window.
3.  Choose **Face Selection Mode** for this tutorial.
    

### Step 4 — Extrude the Wall Faces
1.  Select the **long face of the wall**.
2.  Hold **Shift** and select the back side of the same long edge.
3.  Right-click in the scene view → **Extrude Faces**.
4.  In the Extrude Settings dialog, set the distance: **0.625** and press confirm.
5.  Repeat the extrusion on both sides → distance **0.625** and press confirm.
    
> [!TIP]  
> Check **X position in Inspector** to verify the overall wall width; it should now be **0.375**.

#

### Step 5 — Edit the Wall Top Faces
1.  Select the **3 inner top faces** → right-click → **Extrude Faces**.
    -   Distance: **0.0625** → confirm.
2.  Select the **innermost top face** → right-click → **Extrude Faces**.
    -   Distance: **3** → confirm.
3.  With the top face still selected → extrude **0.03125** → confirm.
4.  Repeat another extrusion → **0.3125** → confirm.

> [!NOTE]  
> The wall’s **Y position should now be 3.25** in the Inspector.

#

### Step 6 — Extrude Front Faces for Detail
1.  Select the **front faces of the top two sections** on both sides → right-click → extrude → distance: **0.0625** → confirm.
2.  Select the **front face of the topmost face** on both sides → extrude → distance: **0.625** → confirm.

#

### Step 7 — Create Wall Prefab
1.  Exit **ProBuilder Edit Mode**.
2.  Drag and drop your **Wall** from the Hierarchy → **Prefabs** folder in the Project window
3.  Position the prefab at `(0,0,0)` in the scene.

#

### Step 8 — Create a Multi-Wall Object
1.  Create a **new empty GameObject** → name it **WallX3** → position `(0,0,0)`.
2.  Place your wall object inside **WallX3** in the hierarchy.
3.  Duplicate the wall object inside **WallX3** → add two additional walls for a total of **3 walls**.
4.  Rename the wall objects: `Wall-01`, `Wall-02`, `Wall-03`.
5.  Drag and drop the **WallX3** object into the **Prefabs** folder → creates a multi-wall prefab.

#

### Step 9 — Place Walls in the Park
1.  Use the **WallX3 prefab** to quickly place walls in your park.
2.  For small gaps, use the **single Wall prefab** to fill spaces as needed.

#

### Step 10 — Save & Commit
1.  Save your scene: **File > Save** or **Ctrl + S**.
2.  Close Unity.
3.  Switch to **GitHub Desktop** → stage your changes.
4.  Commit with the message:
    -   `feat: created and extruded walls using ProBuilder.`
5.  Push your changes to the remote branch: `SceneBuilding`.

