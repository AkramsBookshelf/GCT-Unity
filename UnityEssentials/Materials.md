# 📜 Unity Materials
> By: Akram Taghavi-Burris | © 2026

Materials are one of the core building blocks for giving objects their visual appearance in Unity. A **material** defines how the surface of a 3D object interacts with light and color. In Unity, materials are powered by **shaders**, which are programs that calculate how pixels are rendered on the screen.

> [!TIP]
> Shaders are tied to the render pipeline. In this course, we use the **Universal Render Pipeline (URP)**, which is optimized for a wide range of devices. For most cases, we’ll use the **Lit Shader**, which supports realistic lighting, shadows, and reflections.

## Material Properties

- Workflow Mode
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
  -   **Tip:** Turning off shadow reception can improve performance for background objects or distant scenery.
    

### Surface Inputs

- **Base Map (Albedo)**
  -   The base color or texture of the material.
  -   Can be a **flat color** or a **texture image**.
  -   Think of this as the "skin" of your object.
    
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

## Texture Guidelines

1.  **Power of 2:** Textures should have dimensions like 128x128, 256x256, 512x512, etc. for efficient memory usage.
2.  **Seamless:** Textures that repeat across a surface should tile without visible seams.
3.  **Compression:** Unity allows you to adjust texture compression for performance; higher compression reduces quality.
4.  **Texture Type:** Default should be **2D** unless using special types like Cubemaps or Normal Maps.

# 

### Normal Maps
-   Normal maps simulate depth and detail by altering how light interacts with a surface.  

> [!NOTE]
> Normal maps don’t change the actual geometry; they only affect lighting calculations. Use them to add detail without increasing polygon count.
> 

---

### 🚩 Checkpoint
-   Materials define the look of a surface using shaders.
-   URP + Lit Shader is standard for most objects.
-   Workflow mode, surface type, rendering face, and shadows control how the material interacts with light.
-   Surface inputs like base map, metallic, smoothness, and normal maps define visual characteristics.
-   Textures must be properly prepared: seamless, power-of-2, and correct type.
