# 📜 Unity Materials
> By: Akram Taghavi-Burris | © 2026

Materials are one of the core building blocks for giving objects their visual appearance in Unity. A **material** defines how the surface of a 3D object interacts with light and color. In Unity, materials are powered by **shaders**, which are programs that calculate how pixels are rendered on the screen.

> [!TIP]
> Shaders are tied to the render pipeline. In this course, we use the **Universal Render Pipeline (URP)**, which is optimized for a wide range of devices. For most cases, we’ll use the **Lit Shader**, which supports realistic lighting, shadows, and reflections.

## Creating Materials

Materials are assets that can be created in Unity by right-clicking in the **Project** window and choosing **Create > Material**. Once created, you will need to name your material.

If you need to rename the material, unlike most other assets, the name **cannot** be changed in the **Inspector**. Instead, it must be changed by right-clicking the asset in the Project window and choosing **Rename**.

> [!IMPORTANT]  
> All materials should follow the proper naming scheme, beginning with the prefix **`M_`** followed by a descriptive name, such as:  
> - `M_WoodOak`
> - `M_RustyMetal`
> - `M_GoldMetal`

Once the material is created, you can adjust its properties. These properties can vary depending on which **shader** is selected.

In this course, we use the **Universal Render Pipeline (URP)**, and for most cases, we will use the **URP/Lit Shader**, which is the default shader used for realistic materials. Because of that, your material will include the following core properties.

# 

### Surface Options
![Surface Options](imgs/materials/gct-matProperties-01.png)

- **Workflow Mode**
  -   **Metallic Workflow:** Uses a metallic value to simulate metal vs non-metal surfaces. Best for realistic metals like gold, iron, or aluminum.
  -   **Specular Workflow:** Uses a specular map to define the reflectivity and color of the reflection. Gives more control for non-metal surfaces like plastics or painted surfaces.
    
>[!TIP]
> Use metallic workflow for most standard objects. Use the specular workflow if you need fine control over non-metal reflections.

- **Surface Type**
  -   **Opaque:** Blocks light fully; most solid objects.
  -   **Transparent:** Allows light and background to show through; used for glass, water, or semi-transparent materials.
    
- **Rendering Face**
  -   **Front:** Only the front side of a mesh renders.
  -   **Back:** Only the backside renders.
  -   **Both:** Renders both sides. Useful for thin objects like paper, leaves, or cloth.
    
- **Shadows**
  -   **Receive Shadows:** Determines whether the object shows shadows cast on it from other objects.
  -   Turning off shadow reception can improve performance for background objects or distant scenery.

 #
 
### Surface Inputs

![Surface Inputs](imgs/materials/gct-matProperties-02.png)

- **Base Map (Albedo)**
  -   The base color or texture of the material.
  -   Can be a **flat color** or a **texture image**.
  -   Think of this as the "skin" of your object.
  -   If a texture and base color are set, the color will tint the texture image.
    
- **Metallic Map / Value**
  -   Controls how metallic the surface looks.
      -  0 = non-metal (plastic, wood)
      -   1 = metal (steel, copper)
    
-  **Smoothness**
  -   Determines how shiny or rough the surface is.
  -   Can be set as a **value** or **texture**.
    
- **Normal Map**
  -   Simulates small surface details (bumps, grooves) without adding geometry.
  -   **Strength:** Adjusts the intensity of the effect.
  
> [!WARNING]
> The Texture type of the texture image must be set as **Normal Map** in Unity for it to be correctly applied
>

- **Tiling and Offset**
  -   **Tiling:** Repeats the texture across the surface.
  -   **Offset:** Moves the texture along the surface.
  -   Useful for patterns like bricks, tiles, or wood planks.

---
## Textures
Above, we stated that the **Base Map** property of a material can be set to an albedo (color) or a texture. 
A **texture** is a 2D image that gets mapped onto the surface of a 3D object. Textures are most commonly used to add detail that would be too expensive (or time-consuming) to model directly into the mesh.

For example, instead of modeling every wood grain, crack, or scratch into a wall, we can apply a texture that creates the illusion of that detail.

## How Textures Are Used in Materials
Materials can use several different texture inputs, and each one controls a different part of how the surface looks.
The most important texture is the **Base Map (Albedo)**. This is the texture that defines the object’s main color and overall appearance (wood grain, bricks, painted metal, etc.).

> [!TIP]  
> If your material has a Base Map texture, the **Base Color** should usually be left as **white**. 
> Otherwise, the **Base Color** acts like a **tint**.
> For example, if you apply a wood texture and set the Base Color to green, the wood will appear green, like it was painted or weathered.
> 

Once the Base Map is set, additional texture inputs can be used to control lighting and surface behavior:
-   **Normal Map:** Adds surface detail by affecting how light hits the object (bumps, grooves, cracks).
-   **Metallic Map:** Controls which parts of the surface behave like metal.
-   **Smoothness:** Controls how shiny or rough the surface looks.
    
Many of these maps are not full-color images. Instead, they are **converted from real-world surface data into numeric values** that the shader can interpret.

For example:
-   A **metallic map** is typically a grayscale image where white represents metal (1) and black represents non-metal (0).
-   A **smoothness map** stores how shiny or rough each pixel is, usually in grayscale.
-   A **normal map** stores surface direction for lighting calculations using the RGB channels: red = X (left/right), green = Y (up/down), blue = Z (outward).
  
> [!TIP]  
> Because these maps encode data instead of color, they often look strange to the eye (e.g., grayscale or purple), but the shader uses the information to produce realistic lighting and surface detail.
> 

## Creating Textures 
Textures can be created in a number of ways. They can be:
-   **Photographs** of real-world surfaces (wood, stone, metal, etc.)
-   **Digitally painted** in software like Photoshop or Krita
-   **Procedurally generated** using tools like Substance Designer or Blender
-   **Vector-based**, created in programs like Illustrator or Inkscape

They **do not need to be** realistic. Textures can also be stylized or cartoony, **depending on the visual style of your game**.

> [!TIP]  
> If you plan to create additional texture maps, like **normal maps**, **metallic maps**, or **roughness maps**, these often need to be generated from the base texture.  
> This can be done in image-editing software or with online converters such as the free [NormalMap Online Generator](https://cpetry.github.io/NormalMap-Online/).
> 

When creating or importing textures into Unity, there are a few best practices you should follow to ensure they perform well and look correct:

#

### Power of 2
Most textures should be saved at a resolution that is a **power of 2**, such as:
-   128 × 128  
-   256 × 256    
-   512 × 512    
-   1024 × 1024    
-   2048 × 2048
    
This allows Unity to generate mipmaps efficiently and compress textures properly for real-time rendering.

>[!IMPORTANT]
> **A texture only needs to be as large as the object will appear on screen**.
>
>For example, if a game object is only ever 200 × 200 pixels on screen, it does **not** need a 4K texture. You can start with a high-resolution image for detail, but before using it in the game, it’s better to create a lower-resolution version that matches the maximum size the object will need.
>
>In Unity, the **Max Size** setting for a texture ensures the engine only uses the resolution required for the object. This helps save memory, improve load times, and maintain performance on lower-end devices.
>

#

### Tileable Textures

If a texture will repeat across a surface (like brick, grass, or wood planks), it should be **tileable** (also called **seamless**).

A tileable texture can repeat without showing visible seams or edges. This is important because repeated patterns that don’t match at the edges create obvious lines or breaks, breaking the illusion of a continuous surface.

![TileableTextures](imgs/materials/gct-seamlessTextrues.png)

Using tileable textures can improve **performance**, because a single texture can cover large surfaces. They also provide **flexibility for variation**, since textures can be stretched, rotated, or offset to create unique patterns without needing additional images.

Textures can be made tileable by using **image-editing software** or specialized **pattern-generation tools**. Techniques often include offsetting the image and blending edges to remove visible seams.

> [!NOTE]  
> Not every texture needs to be tileable. **Only make a texture seamless if it will be repeated** across a large surface.
>
> For unique objects or decals, non-tileable textures are fine.
> 

#

### Compression
Textures are one of the largest sources of memory usage in most games. Unity can compress textures automatically, but the compression settings affect several important aspects:
-   **Visual quality:** High compression can introduce artifacts or blur fine details.
-   **File size:** Compressed textures take up less disk and memory space.
-   **Performance:** Smaller textures are faster to load and render, improving framerate, especially on lower-end devices.
    
Some ways you can optimize texture memory and performance include:
-   **Reduce resolution:** Lower the Max Size in the texture import settings to match the actual screen size the object will occupy.
-   **Choose the right Compression format:** Compression formats (such as DXT, ASTC, or ETC) determine how the image is stored in memory. Each have their trade-offs.
-   **Reuse textures:** Tileable textures and shared materials allow multiple objects to use the same image rather than duplicating textures.
    
> [!TIP]  
> Balancing texture size, compression, and quality is key. Always test on target hardware to make sure textures look good without causing unnecessary memory or performance issues.
> 

---

## Texture Import Settings (Inspector)
When you click a texture in the Project window, Unity shows its **import settings** in the Inspector. 
These settings determine how Unity processes and compresses the texture.

![Texture Settins](imgs/materials/gct-textureProperties.png)

- **Texture Type**: Sets how the texture is meant to be used in Unity.
   -  Common types include:
      -   **Default** (most color textures)
      -   **Normal Map** (used for surface detail)
      -   **Sprite (2D and UI)** (used for 2D assets)
    
> [!IMPORTANT]  
> A normal map will not work correctly unless its Texture Type is set to **Normal Map**.
> 
#
- **Texture Shape**: Controls how Unity interprets the texture.
    - Most textures should remain set to:**2D**
    - Other shapes (like Cube) are used for special cases such as skyboxes and reflection probes.

- **sRGB (Color Texture)**: Determines whether the texture should be treated as a color image.
    -   Turn ON for **Base Map / Albedo** textures
    -   Turn OFF for **data textures** like:    
        -   Metallic maps
        -   Roughness / Smoothness maps
        -   Height maps
        -   Mask maps
        
> [!TIP]  
> If a metallic map looks “wrong,” the first thing to check is whether sRGB is incorrectly enabled.
> 
#
-   **Alpha is Transparency** property, determines how Unity uses the alpha channel of a texture.
    -   Many textures include an **alpha channel**, which stores transparency information for the image.
    -   The most common option is **Input Texture Alpha**, which tells Unity to use the alpha channel embedded in the texture itself.
    -   A texture having alpha does not automatically make the material transparent.
        -   Transparency is controlled by the **material’s Surface Type**.

> [!TIP]  
> If a texture does not have an alpha channel, this setting has no effect. For transparency, make sure the texture includes alpha and that the **Surface Type** of the material is set to **Transparent**.
>

> [!NOTE]
> If the texture contains alpha data, Unity can use it for:
> -   Transparent surfaces
> -   Cutout shapes (like leaves, fences, decals, etc.)
>

### Compression Settings 
Inside the Texture properties, you can also set how the textures are to be resized and compressed for the game build. 
These settings are divided into tabs based on the build platform. The **Default** tab will have the baseline settings that apply to all. These settings include: 
 - **Max Size**: limits the maximum resolution Unity will use.
   - For example a 2048×2048 texture can be forced down to 1024×1024 or 512×512.
   - This is one of the easiest ways to reduce file size.

- **Resize Algorithm**: determines how Unity resizes textures when Max Size is smaller than the original.
  - For most cases, Unity’s default is fine.

- **Format**: determines how the texture is stored on the GPU.
  - Different formats can affect:
    -   Visual quality
    -   Transparency support
    -   Compression efficiency
    -   Platform compatibility
    
- **Compression**: reduces file size and improves performance, but may lower quality.
  - Common options:
    -   **None** (highest quality, largest size)
    -   **Low / Normal / High Quality** (recommended)
    
- **Crunch Compression**: an additional compression option that can reduce file size further.

> [!TIP]  
> Crunch compression is useful for reducing build size, but it increases texture load/decompression time.  
> It is best used for textures that do not need to load instantly.
>

--- 

# ⚒️ Tutorial: Create Wall & Gate Materials

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Scene Building     | 15 minutes       |   GitHub Desktop, Unity, Texture.unitpackage     |

</details>

> [!NOTE]
> Before starting this tutorial, ensure you have completed **[ProBuilder](ProBuilder.md)** tutorial and that your project is synced with the **SceneBuilding** in GitHub Desktop.
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
    
