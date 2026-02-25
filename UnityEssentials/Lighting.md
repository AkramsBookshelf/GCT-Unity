# ⚒️ Tutorial: Custom Skybox

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Environement Lighting   | 45 minutes       |   GitHub Desktop, Unity, Media Asset Package |
</details>

> [!NOTE]
> Before starting this tutorial, ensure you have :
>  - Completed **[Skybox Tutorial](Skybox.md)**
>  - That you are on the **Lighting** branch in GitHub Desktop.
>

### Step 1 — Adjust the Sunlight
1.  Ensure that **Level_01** scene is open
2.  In the **Hierarchy** window, select the **Directional Light**
3.  With the light selected in the **Inspector** set the following:
    - **Position:** X: 0, Y: 10, Z: 0
    - **Rotation:** X: 170, Y: 15, Z: 0
    - **Light Appearance:** Color
    - **Color:**  R: 160, G: 140, B: 255, A: 255
    - **Intensity:** 0.5
    - **Shadow Type:** Soft Shadows
      
> [!TIP]
> This should give us a dusk, early-evening look to the scene.
>

#

### Step 2 — Prepare a Lamp for Emission
1.  Select one of your lamps in the scene.
2.  Right-click → **Prefab → Unpack Completely** (detaches it from the original prefab).
3.  With the lamp selected, right-click → **Create Empty Parent**.
    -   Name the parent **Lamp**.
4.  Drag the **Lamp** parent into the **Assets > Prefabs** folder
5.  With the new prefab selected in the Project window, right-click and choose **Prefab > Open in Context**.

# 
### Step 3 — Make Lamp Glass Look Lit
1.  In the **Hierarchy** select your newly created **Lamp** prefab
    - Right-click and choose **Prefab > Open Asset in Context**
3.  Select the **Lamp** in the **Prefab Hierarchy**.
4.  From the top menu, choose: **Tools >  ProBuilder > Object > ProBuilderize**
5.  In the **Project** window, create a new material named **M_LampGlass** and set:
    -   **Workflow**: Specular
    -   **Surface Type**: Transparent
    -   **Base Color**: R: 244, G: 223, B: 131, A: 120
    -   **Emission**: Checked
        -   Use the eyedropper to match the base color
        -   **Emission Intensity**: 0.5
6.  In **Prefab context**, switch to **ProBuilder mode**, select the **glass faces**
7. With the faces selected, drag **M_LampGlass** onto them.

>[!WARNING]
> ProBuilderizing only works on simple models. Complex models may not ProBuilderize correctly.
>

#

### Step 4 — Add a Spot Light to the Lamp
1.  Ensure you are still _editing the **Lamp** prefab in context_
2.  Right-click on the **Prefab Hierarchy** and choose **Create > Light > Spot Light** as a child of the lamp.
3.  Position it at the top of the lamp, just inside the glass, and rotate it to face down:
    - Rotation X: -180
4.  In the **Inspector**, set the following:
    -   **Spot Angle**: Inner 50 / Outer 80
    -   **Color**: R: 244, G: 223, B: 131, A: 255
    -   **Intensity**: 5
    -   **Range**: 5
    -   **Shadow Type**: Soft Shadows
    -   **Shadow Strength**: 1
    -   **Bias / Near Plane**: 0.5
  
# 

### Step 5 — Save Lamp as Prefab Variant
1.  Exit **Prefab editing mode**.
2.  Drag the edited lamp prefab into the **Prefabs folder** in the Project window.
3.  When prompted, choose **Create Prefab Variant**.
4.  Rename the variant: Lamp_SpotLight

# 

### Step 6 — Replace All Scene Lamps
1.  Select all other lamps in the scene
2.  In the **Inspector**, under **Prefab**, drag **Lamp_SpotLight** onto it.
3.  When prompted, choose **Replace**.

> [!NOTE]
> Now all lamps in the scene will use the lit, spot-lighted variant.
> 

# 
### Step 7 — Test the Lighting
1.  Press **Play** in Unity.
2.  Observe dusk sunlight and illuminated lamps in the scene.
    
# 

### Step 8 — Optimize for Web Builds
1.  Open **Edit → Project Settings → Graphics**.
2.  From the **Project** window, in the **Settings** folder locate the **Mobile_RPAsset**
3.  Drag the **Mobile_RPAsset** into the  **Default Render Pipeline**    
4.  Close the settings and **save the scene**.

> [!NOTE]
> Using the **Mobile_RPAsset** instead of the **PC_RPAsset** will reduce real-time shadow issues and improve performance for Web builds.
>

#

### Step 9 — Save Your Work
1.  Save the scene: **File → Save** or **Ctrl + S**
2.  Close Unity.
3.  In **GitHub Desktop**:
    -   Stage your changes
    -   Commit with message:
        -   `feat: Lighting added.`
4.  Push to the appropriate branch.


