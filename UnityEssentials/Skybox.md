# ⚒️ Tutorial: Custom Skybox

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Environement Lighting   | 30 minutes       |   GitHub Desktop, Unity, Media Asset Package |
</details>

> [!NOTE]
> Before starting this tutorial, ensure you have :
>  - Completed **[Audio Tutorial](Audio.md)**
>  - That you are on the **Lighting** branch in GitHub Desktop.
>

### Step 1 — Prepare the Skybox Texture

1.  In the **Project** window, navigate to the **Textures** folder.
2.  Select **T_eilenriede_park_2k**.
3.  In the **Inspector**, set the following properties:
    -   **Texture Type** → Default
    -   **Texture Shape** → Cube
    -   **Wrap Mode** → Clamp
        
> \[!NOTE\]  
> Clamp ensures the texture does not tile, giving a seamless sky appearance.
>

#
### Step 2. — Create the Skybox Material
1.  Navigate to the **Materials** folder in the **Project** window.
2.  Right-click → **Create > Material**.
3.  Name the new material **M_Park_SB**.
4.  With **M_Park_SB** selected, set the following in the **Inspector**:
    -   **Shader** → Skybox / Cubemap
    -   **Tint Color** → `#6E6464`
    -   **Exposure** → 0.5
    -   **Spherical HDR Texture** → `T_eilenriede_park_2k`
        
> \[!NOTE\]  
> These settings create a subtle dusk look in your skybox.
>

# 

### Step 4 — Apply the Skybox
1.  Go to **Window → Rendering → Lighting**.
2.  Under the **Environment** tab, set **Skybox Material** → **M\_Park\_SB**.

#

### Step 5 — Test the Skybox
1.  Press **Play** in Unity.
2.  Observe the scene with the new skybox applied.
   
> [!TIP]  
> You can adjust the **Tint** and **Exposure** later to fine-tune the atmosphere.
>

#

### Step 6 — Save Your Work
1.  Save the scene: **File > Save** or **Ctrl + S**.
2.  Close Unity.
3.  In **GitHub Desktop**:
    -   Stage your changes.
    -   Commit with message:
        -   `feat: added park skybox.`
    -   Push to the appropriate branch.
