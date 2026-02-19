# 📜 UI Document – Game Object

We’ve already created a **UI Document asset** (the `.uxml` file), which you can think of as the **UI Toolkit equivalent of a Canvas layout** in the older uGUI system. However, unlike a Canvas, this UI Document **only exists in the Assets folder**; it is just a file and does **not appear in the scene** by itself.

To actually display this UI in the scene, we need a **UI Document GameObject**. Unity’s naming can be confusing here: the **GameObject is not the UXML file**. Instead, it is a scene object with a **UIDocument component**, which is responsible for rendering the assigned UXML at runtime.

Once the UI Document GameObject is created, we assign the **UXML file** to its **Source Asset** field. This tells Unity which UI Document asset to load and display in the Game View.

## UI Panel Settings

While the **UI Document GameObject** renders the UXML, it also needs rules for **how** to render it. This is handled by a **Panel Settings asset**, which defines:
-   UI scaling behavior
-   Rendering rules
-   Layering and sort order
-   Some performance and fallback behavior
    

> [!NOTE]  
> Most projects only need **one Panel Settings asset** for all menus.
>
> When you create a new UIDocument, Unity searches the project for an existing Panel Settings asset. If none exists, it automatically creates one.
>
> If one does exist, it will pick the one with the lowest **Internal Instance ID** (often the first one created or imported).
>

#

### Theme Style Sheet

The first key setting on a Panel Settings asset is the **Theme Style Sheet**.

> [!WARNING]
> The **Theme Style Sheet** is **not the same** as the USS style sheets you assign directly to a UIDocument in the UI Builder.
    
The Theme Style Sheet is a **.tss file**, which acts as a set of **global style defaults**. For example, Unity’s default button is grey with black text. If you want **all buttons across all UI Documents** to have white backgrounds and black text, you could define a global USS in a Theme TSS file.

**How it works in practice:**
-   Assign a Theme TSS to the Panel Settings asset.
-   That TSS can reference global USS files (via `@import`) or define styles directly.
-   UIDocuments using that Panel Settings will **use the theme styles**, unless the UIDocument itself has **specific USS assigned**.

> [!IMPORTANT]
> The **Theme only affects runtime**; UI Builder will **not automatically show theme styles** unless you select the theme in the UI Builder dropdown.
>
> Many developers find it simpler to **use Theme TSS only as a fallback** and assign USS directly to UIDocuments.
>     

**Creating a Theme Style Sheet:**
1.  Right-click in the Assets folder and choose **Create > UI Toolkit > TSS File (Theme File)** or choose **Default Runtime Theme**.
2.  Select the new TSS file and, in the Inspector, you can assign **default style sheets**.

> [!TIP]  
> For most small projects, it’s easier to create a **Default Runtime Theme** with no specific settings and define **all styles per UI Document** using USS and UXML.
>

#

### Text Settings
The **Panel Settings** asset also includes **Text Settings**, which are **fallback assets for text, sprites, and font rendering**.
-   If a font, sprite, or TextMeshPro asset fails to load, Unity uses the Text Settings asset to provide defaults.
-   These assets must reside in the **Resources folder** so they can be preloaded at runtime.
-   Adding Text Settings increases memory usage slightly, but ensures that UI elements **always have a fallback**.
    
**Creating a Text Settings asset:**
1.  Right-click in the Assets folder and choose **Create > UI Toolkit > Text Settings**.
2.  Assign it to your Panel Settings asset as needed.

> [!NOTE]  
> In most simple projects, the default Text Settings can be safely ignored.
>

# 
