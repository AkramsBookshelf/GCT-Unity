# 📜 Unity ProBuilder
> By: Akram Taghavi-Burris | © 2026

In most game projects, **3D models are created outside of Unity**, using programs like Blender, Maya, or 3ds Max. These tools give artists full control to create detailed assets, but they can take time to complete.

During the early stages of **level design**, you often need to **prototype game objects and environments quickly**, even before the final models are ready. Unity’s **primitive shapes** (cubes, spheres, planes, etc.) are useful for this purpose, allowing you to block out a level or test gameplay ideas.

However, primitives have limitations; primarily, they **cannot easily create custom forms** like walls with varying thicknesses, gates with openings, or modular architecture needed for level layouts.
    
This is where **ProBuilder** comes in.

## What is ProBuilder?

**ProBuilder** is a 3D modeling and level-design tool built directly into Unity. Unlike standard primitive shapes, ProBuilder allows you to **directly edit the geometry of objects**, adjusting **vertices (points), edges (lines), and faces (surfaces)**. This gives you the flexibility to **create complex, modular objects** without leaving Unity or waiting for final models.

> [!TIP]  
> Think of it like starting with a cube but being able to **push, pull, and reshape it** to fit exactly what your level needs — giving you **more control than primitives**, while keeping your prototyping workflow fast.

ProBuilder is designed to be **fast, flexible, and beginner-friendly**, making it ideal for:
-   **Level prototyping** – Quickly mock up walls, floors, gates, and modular structures.
-   **Precision modeling** – Work with exact dimensions, pivots, and modular layouts.
-   **Hierarchy-friendly design** – Organize objects into reusable prefabs while keeping the scene tidy.
-   **Subdivision and extrusion tools** – Easily add detail and shape geometry directly in the Editor.

### Experimental Boolean Operations
While **ProBuilder** is a step up from the primatives, it doesn't fully compare to a 3D modeling tool, especially in default mode. 
However, ProBuilder offers **experimental Boolean operations**, which allow you to create more complex shapes by **combining, subtracting, or intersecting objects**
-   **Union:** Merge two objects into a single mesh.
-   **Subtract:** Remove one object’s shape from another.
-   **Intersect:** Create a new object from the overlapping volume of two objects.

> [!NOTE]
> After installing the **ProBuilder** package, you can enable **Boolean operations** in Unity by going to:
> **Edit > Preferences > ProBuilder > Experimental Features Enabled**.
> 
> Once enabled, the tool can be accessed at: **Tools → ProBuilder → Experimental → Boolean (CSG) Tool**.
> CSG stands for **Constructive Solid Geometry**, and it lets you **combine, subtract, or intersect objects** to create new shapes.
>
> While this can be very powerful, Boolean operations often create **messy geometry** that can make scaling, pivots, and prefab creation more difficult.
>
> For this tutorial, we will **stick to ProBuilder’s standard tools**.
> 

# 

## Best Practices for ProBuilder Wall Creation
This tutorial will guide you through **creating and editing walls** using ProBuilder while maintaining best practices:
-   **Use Precise Dimensions** – Set pivot points and sizes for consistency.
-   **Work in Edit Mode Carefully** – Ensure the correct faces are selected before extrusion.
-   **Hierarchy Organization** – Parent multiple objects to keep the scene tidy.
-   **Prefab Usage** – Convert complex or reusable objects into prefabs to speed up scene building.

---

# ⚒️ Tutorial: Create the Park Wall with ProBuilder

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Scene Building     | 30 minutes       |   GitHub Desktop, Unity     |

</details>

> [!NOTE]
> Before starting this tutorial, ensure you have completed **[Scene Building](SCeneBuilding.md)** and that your project is synced with the **SceneBuilding** in GitHub Desktop.
>

> [!WARNING]
> If you have made any changes to your project from a different pc (i.e., classroom pc) and have pushed them to GitHub, you will need to **Fetch** the updates to your project in **GitHub Desktop** before starting this tutorial.
> 


## Step 1 — Install ProBuilder Using Package Manager

1.  Open the **ParkGame-Unity** project
2.  In the Unity Editor, open the **Package Manager**: 
    -   **Window > Package Manager**
        
3.  In the top-left dropdown, change the package source to:
    -   **Unity Registry**
        
4.  In the search bar, type:
    -   `ProBuilder`
        
5.  Select **ProBuilder** from the list.
    
6.  Click **Install**.
    

> [!TIP]  
> If ProBuilder is already installed, the button will say **Remove** instead of Install.
> 

# 
## Step 2 — Open the Default Sample Scene (Empty Workspace)

1.  In the **Project** window, open:
    
    -   `Assets/Scenes/SampleScene`
        
2.  Make sure the scene is mostly empty (just a camera and light are fine).
    

> [!NOTE]  
> We will build the ProBuilder wall inside the **SampleScene** so we don’t accidentally modify **Level-01**. This keeps your main level clean and lets you focus on building the wall prefab without distractions from the environment.
> 

#

### Step 3 — Create a Basic Cube
1.  Click and **hold on the CC Icon** in the main toolbar of the scene view.
2.  In the pop-up dialog, select **Create Cube**.
3.  Click and drag in the scene view to create a cube. **Do not worry about exact dimensions yet.**

![Create Probuilder Cube](imgs/probuilder/gct-probuilder-01.png)
    

> [!NOTE]  
> You can adjust the size later, so initial placement is just for convenience.
> 

#

### Step 4 — Set Cube Shape Settings
1.  With the cube in **creation mode**, open the **Shape Settings** window in the scene view.
2.  Set the following settings:
    -   **Pivot:** Center
    -   **Size:** X = 0.125, Y = 0.125, Z = 1
3.  Press **Q** to exit creation mode and return to pan mode.

![Create Probuilder Cube](imgs/probuilder/gct-probuilder-02.png)
   
> [!TIP]  
> If you accidentally clicked away or mistyped settings, select the cube in the **Hierarchy** → in the **Inspector**, click **Edit Shape** under the ProBuilder Shape component.
> ![Edit Probuilder Shape in Inspector](imgs/probuilder/gct-probuilder-03.png)

#

### Step 5 — Wall Properties
1.  With the Probuilder cube selected in the **Inspector** window
2.  Rename the cube to **Wall**
3.  Move the cube's **position** to X = 0, Y = 0, Z = 0

> [!IMPORTANT]  
> Keeping the wall at `(0,0,0)` helps avoid pivot and placement issues when converting it into a prefab.
>
    
#

### Step 6 — Enter ProBuilder Edit Mode
1. In the **Scene** window, click and **hold on the object icon** in the toolbar 
   - Select **ProBuilder** from the pop-up.

![Probuilder Edit mode](imgs/probuilder/gct-probuilder-04.png)
  
2.  The **ProBuilder Modify Toolbar** appears at the top of the scene window.
3.  Choose **Face Selection Mode** for this tutorial.

![Edit in Face Mode](imgs/probuilder/gct-probuilder-05.png)
    
#

### Step 4 — Extrude the Wall Faces
1.  Select the **long face of the wall**.
2.  Hold **Shift** and select the back side of the same long edge.
3.  Right-click in the scene view → **Extrude Faces**.
4.  In the Extrude Settings dialog, set the distance: **0.0625** and press confirm.

![Extrude the front faces](imgs/probuilder/gct-probuilder-06.png)

> [!IMPORTANT]
> After modifying a shape beyond its original dimensions, the base properties can no longer be edited in the Inspector.
> 
   
6.  Repeat the extrusion for both sides → distance **0.0625** and press confirm.
7. Check **X position in Inspector** to verify the overall wall width; it should now be **0.375**.
   
![Check Probuilder Object Size in Inspector](imgs/probuilder/gct-probuilder-07.png)

#

### Step 5 — Edit the Wall Top Faces
1.  Select the **3 inner top faces** → right-click → **Extrude Faces**.
    -   Distance: **0.0625** → confirm.
  
![Extrude the inner top faces](imgs/probuilder/gct-probuilder-08.png)

2.  Select the **innermost top face** → right-click → **Extrude Faces**.
    -   Distance: **3** → confirm.
  
![Extrude the main wall](imgs/probuilder/gct-probuilder-09.png)
  
3.  With the top face still selected → extrude **0.03125** → confirm.
4.  Repeat another extrusion → **0.03125** → confirm.

> [!NOTE]  
> The wall’s **Y position should now be 3.25** in the Inspector.
> 

#

### Step 6 — Extrude Front Faces for Detail
1.  Select the **front faces of the top two sections** on both sides → right-click → extrude → distance: **0.0625** → confirm.

![Top detail exturtion](imgs/probuilder/gct-probuilder-10.png)

2.  Select the **front face of the topmost face** on both sides → extrude → distance: **0.0625** → confirm.

![Wall top cap](imgs/probuilder/gct-probuilder-11.png)

3. Exit **ProBuilder Edit Mode**.

#

### Step 7 — Create Wall Prefab

1. Drag and drop your **Wall** from the **Hierarchy** → into the **Prefabs** folder in the **Project** window.

2. In the **Hierarchy**, right-click → **Create Empty**
   - Name it **WallX3**
   - Set its position to `(0,0,0)`.

3. Drag the **Wall** object into **WallX3** so it becomes a child.

4. Duplicate the wall **two times** (so you have 3 total walls).

5. Move the duplicated walls so that:
   - One wall is placed to the **left** of the original wall
   - One wall is placed to the **right** of the original wall  
   - The **original Wall stays centered at (0,0,0)**

> [!IMPORTANT]  
> Do **not** move the original wall. Keeping the center wall at `(0,0,0)` ensures the prefab’s pivot stays centered, which makes placement easier later.
> 

6. In the **Hierarchy** window, rename the walls:
   - `Wall-01` (Left)
   - `Wall-02` (Center)
   - `Wall-03` (Right)

7. Drag and drop the **WallX3** object into the **Prefabs** folder to create a multi-wall prefab.

> [!NOTE]  
> You now have two wall prefab options:
> - **Wall** = a 1-meter wall segment  
> - **WallX3** = a 3-meter wall segment  
>
> You *could* also create variations like **WallX2** or **WallX4**, but when designing prefabs, it’s best to only create the variations you are most likely to use. Too many prefab versions can quickly clutter your project.
> 


#

### Step 9 — Place Walls in the Park 
>[!CAUTION]
> Make sure that you have created the **Wall prefabs** and that they appear in the **Prefabs** folder in the **Project** window, before continuing.
>

1.  Exit the **SampleScene** and return to **Level\_01**.
    -   No need to save the SampleScene — we already made the **prefab**, which is saved automatically.
        
2.  In the **Hierarchy**, inside your **Environment** folder:
    
    -   Create a new empty GameObject → name it **ParkWall**
        
    -   Set its **position** to `(0,0,0)`
        
    -   This will serve as the **parent container** for all wall segments and can later be turned into a prefab.
        
3.  Place your **WallX3 prefabs** inside **ParkWall**.
    
    -   When duplicating single-wall segments, choose a **corner of the park as Wall-01** and number subsequent walls sequentially.
        
4.  Use the **WallX3 prefab** to create the main boundaries of the park.
    
    -   For small gaps, use the **single Wall prefab** to fill in.
        
5.  Add wall segments to divide the park into at least **two sections**.
    
6.  Leave a **3-meter opening** between the sections.
    
    -   We will add a gate here in the next tutorial.
        

> [!TIP]  
> Using an empty **ParkWall** object as a parent keeps your hierarchy organized and allows you to **convert the entire wall layout into a prefab** if needed. Numbering walls helps track placement and makes future edits easier.
> 

#

### Step 10 — Save & Commit
1.  Save your scene: **File > Save** or **Ctrl + S**.
2.  Close Unity.
3.  Switch to **GitHub Desktop** → stage your changes.
4.  Commit with the message:
    -   `feat: created Wall Prefab.`
5.  Push your changes to the remote branch: `SceneBuilding`.

---

# ⚒️ Tutorial: Create the Park Gate with ProBuilder

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Scene Building     | 30 minutes       |   GitHub Desktop, Unity     |

</details>

> [!NOTE]
> Before starting this tutorial, ensure you have completed **[Create the Park Wall with ProBuilder](#%EF%B8%8F-tutorial-create-the-park-wall-with-probuilder)**
 and that your project is synced with the SceneBuilding branch in GitHub Desktop.
>


### Step 1 — Open the Default Sample Scene (Empty Workspace)

1.  In the **Project** window, open:
    
    -   `Assets/Scenes/SampleScene`
        
2.  Make sure the scene is mostly empty (just a camera and light are fine).
    

> [!NOTE]  
> Just as with the last tutorial, we will create our Gate inside the **SampleScene** so we don’t accidentally modify **Level-01**. This keeps your main level clean and lets you focus on building the wall prefab without distractions from the environment.
> 

#

### Step 3 — Create a Gate Base
1.  Click and **hold on the CC Icon** in the main toolbar of the scene view.
2.  In the pop-up dialog, select **Create Cube**.
3.  Click and drag in the scene view to create a cube. **Do not worry about exact dimensions yet.**
4. With the cube in **creation mode**, open the **Shape Settings** window in the scene view.
5.  Set the following settings:
    -   **Pivot:** Center
    -   **Size:** X = 0.125, Y = 0.125, Z = 0.125

![Gate Base](imgs/probuilder/gct-probuilder-12.png)

6.  Press **Q** to exit creation mode and return to pan mode.
7.  With the cube selected in the **Inspector** window
    - Rename the cube to **Gate**
    -  Move the cube's **position** to X = 0, Y = 0, Z = 0

> [!NOTE]  
> This cube will serve as the starting post of the gate. Keep it centered at `(0,0,0)` to avoid pivot issues later.
> 

#

### Step 2 — Build the Gate Post

1.  Select the **top face** of the cube.
2.  Right-click → **Extrude Faces**, set **distance = 0.0625**, confirm.
3.  With the top face still selected, **extrude** in sequence:
    -   **0.75** → confirm
    -   **0.125** → confirm
    -   **1** → confirm
    -  **0.125** → confirm
    -   **1** → confirm
    -   **0.03125** → confirm
    -   **0.03125** → confirm
      
![Gate Base Extruded](imgs/probuilder/gct-probuilder-13.png)
    

> [!TIP]  
> The total **Y position** of the gate post should now be **3.25**.
> 

#

### Step 3 — Add Front Details
1.  Select the **front faces** of the bottom gate post (both sides) → **extrude 0.125**.
2.  Select the **front faces just above** the previous extrusion → **extrude 0.0625**.
   
![Gate Bottom front face extrusion](imgs/probuilder/gct-probuilder-14.png)
    
4.  Select the **front faces of the top two sections** (both sides) → **extrude 0.0625**.
5.  Select the **front face of the topmost face** (both sides) → **extrude 0.0625**.

![Gate Top front face extrusion](imgs/probuilder/gct-probuilder-15.png)

#    

### Step 4 — Build Intermediate Rails
1. From the **side of the gate post**, select the faces of the subdivisions made between posts 
2. Extrude these faces out **0.125**, repeat **10 times**. 

![Extrude the Gate Rails](imgs/probuilder/gct-probuilder-16.png)
    
3. Check to make sure the total **Z scale** of the gate is **1.5**.

#

### Step 5 — Subdivide and Extrude Panels

1. Select **alternating top faces** (_shift-click_) of the bottom row 
2. Right-click and choose **Subdivide Faces**
2.  With the faces still selected, select **Subdivide Faces** a second time.
    - This will create 4x4 faces on each face selected.
  
![Subdivided Faces](imgs/probuilder/gct-probuilder-17.png)
    
4. Deselect the faces.
5. Select the **inner 4 faces** of the first alternating faces. 
6. Right-click → **Merge Faces**, repeat for each square.

![Merge faces](imgs/probuilder/gct-probuilder-18.png)
    
7.  Select the **inner square face** of each merged section → extrude **2**.

![Extrude inner rails](imgs/probuilder/gct-probuilder-19.png)

8.  Rotate the gate to the bottom side of the bottom row of the gate
9.  Repeat steps **5.1–5.6** above for symmetry.
10. Select the **inner square face** of each merged section → **extrude** down **0.5**.

![Extrude bottom inner rails](imgs/probuilder/gct-probuilder-20.png)
    

### Step 6 — Add End Rail
1. Select the **last top face of the bottom row** → **extrude 2.125** → confirm.

![Extrude the end rail](imgs/probuilder/gct-probuilder-21.png)
    
2.  Extrude top face  → **0.03125** → confirm.
3.  Extrude one more time → **0.03125** → confirm.
4.  Select **front and back faces** of the topmost section → **extrude 0.03125** → confirm.
5.  Select the **top face again** → **extrude 0.03125** → confirm.
6.  With the top face still selected → use the **Scale tool** → uniformly scale inward to create a **cap**.

![End rail cap](imgs/probuilder/gct-probuilder-23.png)

7. Rotate the gate to the bottom side of the bottom row of the gate
8. Select the end rail face  → **extrude 0.5** → confirm.

![End rail bottom](imgs/probuilder/gct-probuilder-24.png)

9. Extrude another **0.25** → confirm.
10. With the top face still selected → use the **Scale tool** → uniformly scale inward to create a blunt point.

![End rail bottom cap](imgs/probuilder/gct-probuilder-25.png)



#    

## 🎯CHALLENGE: Add Gate Finial (Step 7)

Using the methods above, add decorative finials to the gate rails.
This adds visual interest and makes the gate look more detailed. 
Keep proportions consistent with the rest of the gate.

![Gate Finial](imgs/probuilder/gct-probuilder-26.png)

>[!NOTE]
>A **finial** is a decorative pointed ornament placed at the top of a gate rail or similar structure.
>

#

## Step 8 — Create the Full Park Gate Prefab

1.  Exit **ProBuilder Edit Mode**.
    
2.  Drag and drop your **Gate** object from the Hierarchy → into the **Prefabs** folder in the **Project** window.
    
    -   This creates a reusable **single gate piece** (one side of the park gate).
        

> \[!NOTE\]  
> The object we've created is essentially **one side of a two-door park gate opening**. Later, we will combine two of these pieces to make the full gate.

1.  In the **Hierarchy**, right-click → **Create Empty**:
    -   Name it **ParkGate**
    -   Set its **position** to `(0,0,0)`
    -   This will serve as the **parent container** for the full gate assembly.
        
2.  Drag the single **Gate** into **ParkGate** so it becomes a child.
    -   Set its transform to **X = 0, Y = 0, Z = -1.4375**
    -   Rename it **Gate-Left** in the Hierarchy.
        
3.  Duplicate **Gate-Left** → this will be the right side of the gate:
    -   Set the **Y rotation** to **180°** to mirror the opening
    -   Set its transform to **X = 0, Y = 0, Z = 1.4375**
    -   Rename it **Gate-Right**
        
4.  Select the **ParkGate** object in the Hierarchy → drag it into the **Prefabs** folder in the Project window.
    -   This creates a **full two-piece ParkGate prefab** that is **3 meters wide** and is ready to place in the park.
        
> [!TIP]  
> Keeping **Gate-Left** and **Gate-Right** as children of **ParkGate** ensures the pivot of the prefab is centered, making placement easier in your scene. You can still edit the individual pieces if you want to adjust details later.
> 

#

 ### Step 9 — Place the Gate in the Park
 >[!CAUTION]
> Make sure that you have created the **Gate prefab** and that they appear in the **Prefabs** folder in the **Project** window, before continuing.
>

1.  Exit the **SampleScene** and return to **Level\_01**.
    -   No need to save the SampleScene — we only made the prefab, which is saved automatically.
        
2.  In the **Hierarchy** window, expand the **ParkWall** game object
3.  Place your **ParkGate** prefab in the **3-meter opening**, we left in our wall segments.

#

### Step 10 — Save & Commit
1.  Save your scene: **File > Save** or **Ctrl + S**.
2.  Close Unity.
3.  Switch to **GitHub Desktop** → stage your changes.
4.  Commit with the message:
    -   `feat: created Gate Prefab.`
5.  Push your changes to the remote branch: `SceneBuilding`.

---




