# 📜 Unity Skyboxes
> By: Akram Taghavi-Burris | © 2026

Skyboxes are a key part of creating immersive game environments. They define the background of your scene, providing the illusion of vast skies, distant landscapes, or even enclosed spaces. While the terrain and objects establish the world itself, skyboxes shape the atmosphere, lighting, and mood, making a scene feel alive and believable.

In Unity, skyboxes also influence **lighting calculations and reflections**, affecting how objects appear under environmental light and reflective surfaces. A well-chosen skybox enhances realism and immersion.

## What is a Skybox?
A **skybox** is essentially a textured cube (or sphere) that surrounds your scene. The player cannot leave it—it’s infinitely far away—so it always looks like the sky or environment continues endlessly. Skyboxes typically serve as the **environment backdrop** and can include elements like:
-   Open skies with clouds or stars
-   Mountains, forests, or city skylines
-   Space scenes with planets and nebulae

# 

### Why Skyboxes are Important
Skyboxes impact your game in multiple ways:
-   **Visual Atmosphere**: Sets the tone—daytime, sunset, night, or alien world.
-   **Lighting Influence**: Skyboxes feed light into Unity’s **global illumination system**, affecting how objects are lit.
-   **Reflections**: Skyboxes are used for reflections on shiny surfaces (via reflection probes or standard reflection settings).
-   **Player Immersion**: A realistic or stylized sky helps the player feel grounded in the game world.
    

> [!TIP]  
> Even a subtle dusk or cloudy sky can dramatically change how a scene feels.
>

# 

### Types of Skyboxes in Unity

Unity supports several common skybox types:
1.  **Panoramic (2D/360° Image)**
    -   Uses a single spherical image to wrap the scene.
    -   Good for real-world panoramas or stylized backgrounds.
        
2.  **Cubemap (6-Sided Texture)**
    -   Consists of six square images forming the faces of a cube.
    -   Preferred for high-quality environmental lighting and reflections.
    -   Handles directional lighting and reflections more accurately than panoramic images.
        
3.  **Procedural Skybox**
    -   Dynamically generates the sky based on parameters like sun position, atmosphere, clouds, and tint.
    -   Useful for fully dynamic day-night cycles.
        

> \[!NOTE\]  
> In most cases, cubemaps give the best results for skyboxes:
> - Seamless visuals: Unlike panoramic textures, cubemaps avoid pinching or distortion at the top and bottom of the sky.
> - Better performance: Modern GPUs handle cubemap sampling efficiently using 3D direction vectors.
> - Accurate reflections and lighting:  Cubemaps work naturally with Unity’s lighting and reflection systems, making scenes look more realistic.

---

## Unity Environment Tab Overview
In Unity, skyboxes are applied via the **Environment** settings in the **Lighting** window:  
**Window > Rendering > Lighting > Environment**

Key points:
-   **Skybox Material**: Assigns the material that will render your sky.
-   **Ambient Lighting**: Determines the general light color and intensity that fills the scene from the skybox.
-   **Reflections**: Skybox affects **Reflection Probes**, influencing how shiny or reflective objects look.

> [!NOTE]  
> Setting the **Skybox Material** in the **Environment** tab of the **Lighting** window in Unity is **scene-specific**.
> -   Each scene has its own Lighting settings stored in the scene file.
> -   Changing the skybox in one scene **does not affect other scenes**.
> -   If you want multiple scenes to share the same skybox, you need to apply the material in each scene individually or create a **Lighting Settings preset** and reuse it.
> 

# 

Skyboxes are more than just background images—they are integral to **lighting, reflections, and immersion**. Choosing the right skybox type (cubemap, panoramic, or procedural) ensures that your scene looks consistent and believable. By understanding how Unity applies skyboxes via the Environment tab, you can control both the aesthetic and the technical behavior of your game’s world.

--- 

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
