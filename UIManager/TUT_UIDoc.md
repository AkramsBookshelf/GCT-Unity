# ⚒️ Tutorial: Adding UI Documents to Scenes
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| UI Toolkit        | 10 minutes        | Unity, IDE |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed [Styling UI With USS tutorial](TUT_USS.md)
> - Ensure you are on the **UIManager** branch
>   

# 
### Step 1: Create a UI Document GameObject
1.  Open the **MainMenu Scene** 
2.  Right-click in the **Hierarchy** and create **UI Toolkit > UI Document**:  

#

### Step 2: Create a Default Panel Settings Asset

1.  In the **Project** window, in the **Assets/UI** folder create a new folder named **UI-Settings**
2.  Right-click on the **UI-Settings** folder and choose **Create > UI Toolkit > Panel Settings**
    -   Name it: `DefaultPanelSettings`
        
3.  Select the new **Panel Settings asset**, inspect its properties:
    -   **Sort Order:** leave as `0` for now
    -   **Theme Style Sheet:** leave blank for default runtime theme
    -   Ensure **Sort Order** is `0` (for the base menu)
    -   Set the **Scale Mode**:
        -   `Scale With Screen Size` 
        -   Set **Reference Resolution** to your target resolution (e.g., 1920x1080)
        -   Leave **Screen Match Mode** at default **Match Width or Height**
    - Leave the remaining settings at default
            
2.  Save all changes.
      
4.  Save the asset.
    

> \[!NOTE\]  
> Unity will automatically assign a Panel Settings asset if none exists when you add a UIDocument.
> Creating one explicitly is a best practice, so you have full control over scaling, layering, and fallback behavior.
>

#

### Step 3: Create and Set the Theme Style Settings
1.  In the **Project** window, in the **UI-Settings** folder, right-click and choose **Create > UI Toolkit > Default Runtime Theme**
        
2.  Select the `DefaultPanelSettings` in the **Project** window, in the **Inpsector**
    - Set the **Theme Style Sheet** to the **Default Runtime Theme**

> \[!TIP\]  
> The Default Runtime Theme is mostly a **fallback system**. Assign USS directly to your UI Document whenever possible.
>

#

### Step 4: Assign UI Document Settings

1.  Select the `UI_Document` GameObject in the **Hierarchy**
    
2.  In the **UIDocument component**, assign the **Panel Settings** field:
    
    -   Drag `DefaultPanelSettings` into **Panel Settings**
3. Drag your **MainMenu UXML** from the **Project** window to the **Source Asset** field
4. Set the sort order to 1 
5.  Press **Play** to test in the Game View
    -   The menu should now appear exactly as defined in the UXML, with styling applied via USS.

> #### 💾 Save & Commit
> - Save all your scripts
> - Commit your changes with the message:
>    - *feat: Added UI Document Main Menu*
> - **Push** changes to GitHub
>

# 
### 🐞 BUG FIX – Troubleshooting Checklist

Experiencing issues with your UI Document setup? Try the following:
-   **UI does not appear in the Game View**
    -   Confirm the **UIDocument component** is attached to a GameObject in the scene.
    -   Make sure the **Source Asset** field is assigned to your **MainMenu UXML**.
    -   Verify the **Panel Settings** field is assigned; Unity requires this to render UI.
        
-   **Button styles look wrong or default**
    -   Check that your **USS files** are assigned either directly in the UIDocument or imported via a **Theme TSS**.
    -   Remember, the **Theme only applies at runtime**; UI Builder may not show it unless explicitly selected.
        
-   **UI scales incorrectly**
    -   Inspect the **Scale Mode** in the Panel Settings. For responsive menus, use **Scale With Screen Size**.
    -   Verify **Reference Resolution** matches your target screen size.
    -   Check **Screen Match Mode** (Match Width or Height is typical).
        
-   **Multiple UI Documents overlap unexpectedly**
    -   Adjust **Sort Order** in the Panel Settings for each UIDocument. Higher values render on top of lower values.
        
-   Use the **Console** to check for warnings or errors during scene play.
    
---

# 🎉 New Achievement: Main Menu Ready

You’ve successfully added the **MainMenu UI Document** to the scene. The menu is now ready to render at runtime with correct scaling, layering, and fallback behavior.

## 🚩 Checkpoint

Key takeaways from this lesson:

-   The **UI Document asset** (`.uxml`) defines **structure and layout** but lives only in the **Assets folder**.
-   The **UI Document GameObject** is what actually renders your UI in the scene.
-   **Panel Settings** define **how** the UI is rendered: scaling, layering, sort order, fallback text/sprites, and optional theme styling.
-   **Theme Style Sheets (.tss)** act as **global defaults** applied at runtime, but **UI Builder may not display them automatically**.
-   **Text Settings** provide **fallback fonts and sprites** if assets fail to load.
-   **Sort Order** controls the visual stacking of multiple UI Documents. Higher values render on top.
-   **Scale Mode and Screen Match Mode** control **responsive scaling** across resolutions.
-   Following these steps ensures your **MainMenu UI** is modular, properly layered, and ready for runtime updates.
