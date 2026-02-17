# ⚒️ Tutorial: Styling UI With USS
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| UI Toolkit        | 10 minutes        | Unity, IDE |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed [UI Document tutorial](TUT_UIDoc.md)
> - Ensure you are on the **UIManager** branch
>   

Now that we have a working **UXML scaffold** for our main menu, the next step is to **style it**. While Unity’s **UI Builder** allows visual styling, it often produces **inline styles**, which can be cumbersome and harder to maintain. Instead, we’ll use **USS (Unity Style Sheets)** to define **global and theme-specific styles**, giving us a clean, reusable, and modular design workflow.

# 
## USS vs CSS

While **USS is inspired by CSS**, it is **not identical**. Key points to note:
-   Not all CSS properties are available in USS. For example, advanced CSS features like `grid-template-areas`, complex animations, or certain pseudo-selectors are **not supported**.
-   USS includes **Unity-specific properties** that control things like fonts, text outlines, and text alignment. Examples include:
    -   `-unity-font-definition` → assigns a TextMeshPro SDF font asset
    -   `-unity-font-style` → bold, italic, etc.
    -   `-unity-text-outline-color` → stroke color for text
    -   `-unity-text-outline-width` → stroke thickness in pixels
    -   `unity-text-align` → horizontal and vertical alignment
        
> \[!TIP\]  
> Think of USS as **“CSS for Unity UI Toolkit”**: most familiar properties like `flex-direction`, `justify-content`, `align-items`, `margin`, `padding`, `width`, `height`, and `color` work as expected, but **Unity adds extra controls and some CSS rules won’t work**.
>

# 

## Step 1 — Global Styles
1. Open your Unity project in your **IDE**
2. In the **Assets / UI** folder, create a new file:
   - **GlobalSystles.uss**
     
Global styles act as a **baseline** for all UI documents. They define default layouts, fonts, colors, and spacing for general use.

Here’s an example `GlobalStyles.uss` file:

```css
/*****************************
GLOBAL DEFAULT STYLES
*****************************/

/* Root container fills screen and centers content */
.root-container {
    width: 100%;
    height: 100%;

    flex-grow: 1
    flex-direction: column;
    justify-content: center;
    align-items: center;
    
    -unity-font-definition: url("project:/Assets/UI/Fonts/Roboto/Roboto-Regurlar-SDF.asset");
    font-size: 14px; 
    -unity-font-style: bold;
    
    background-color: #fff;
}

.game-title {
    margin-bottom: 4%;

    -unity-font-definition: url("project:/Assets/UI/Fonts/Bangers-Regular-SDF.asset");
    font-size: 100px;

    color: #ffd166;

    -unity-text-outline-color: #000000;  /* stroke color */
    -unity-text-outline-width: 2;        /* thickness in px */
}


/* Button container: vertical layout */
.button-container {
    flex-direction: column;
    width: 60%;          /* responsive */
    max-width: 360px;    /* prevents being huge on wide screens */
    min-width: 220px;    /* prevents being tiny */

    height: 40%;        /* gives it space to distribute */
    min-height: 200px;  /* prevents it from collapsing */

    justify-content:space-evenly; /* Evenly space out the buttons */
}


/* Buttons styling */
.button-container Button {
    width: 100%;       /* fills the container */
    height: 50px;      /* Set a standard height */
    
    font-size: 16px; 

    background-color: #fff;
    color: #000;

    border-radius: 8px;

    border-top-width: 2px;
    border-right-width: 10px;
    border-bottom-width: 10px;
    border-left-width: 2px;

    border-color: #000;

    -unity-text-align: middle-center;
}


.button-container Button:hover {
    background-color: #ffd166;

    border-right-width: 6px;
    border-bottom-width: 6px;
}


```
> [!WARNING]
> To define fonts, the font must first be imported into the Unity project (i.e, drag and drop TTF/OTF files into your project). Inside Unity, select the font file, choose **Create > Text Core > Font Asset > SDF**. These font assets can then be referenced in USS via -unity-font-definition.
>

# 

## Step 2 — Assign the USS to Your UI Document

1.  Open your **MainMenu** UI Document in **UI Builder**.
    
2.  In the **Inspector → StyleSheets**, click **Add StyleSheet** and select `GlobalStyles.uss`.

Your UI Document now uses the **global baseline styles**, giving your menu a clean, consistent look across projects.

# 

## Step 3 — Theme-Specific Styles
While the Global Style sheet sets the baseline, if we want to create theme specfic overrides, we can do this in a separate stylesheet. 

1. With your Unity project opened in your **IDE**
2. In the **Assets / UI** folder, create a new file:
   - **CampCraft.uss**
  
Create the following _Camp Craft_ specific styles: 

```css
/*****************************
CAMP CRAFT THEME STYLES
*****************************/

/*** THEME FONTS & COLORS **/
/* 

Body Font: Lato-Regular
Heading Font: Courgette

Warm White: rgba(250, 245, 230, 0.5) (semi-transparent background)
Warm Yellow: rgba(255, 209, 102, 1) 
Warm Beige: rgba(245, 230, 196, 1)
Dark Beige: rgba(233, 216, 166, 1)
Chocolate Brown: rgba(255, 209, 102, 1)

 */

.root-container.campcraft-theme {
    width: 30%;
    height: 100%;
    margin-left: 10%;
    -unity-font-definition: url("project:/Assets/UI/Fonts/Lato/Lato-Regular-SDF.asset");
    background-color: rgba(250, 245, 230, 0.5);
}

.campcraft-theme .game-title {
    font-size: 50px;
    -unity-font-definition: url("project:/Assets/UI/Fonts/Fonts/Courgette-Regular-SDF.asset");
    color: rgb(255, 209, 102);
    -unity-text-outline-color: rgb(92, 58, 33);
}

.campcraft-theme .button-container Button {
    color: rgb(92, 58, 33);
    background-color: rgb(245, 230, 196);
    border-color: rgb(92, 58, 33);
}

.campcraft-theme .button-container Button:hover {
    background-color: rgb(233, 216, 166);
}

```

> [!TIP]
> When building a themed UI like Camp Craft, keeping your brand identity consistent is key. While listing your colors and fonts in comments is a great starting point for quick reference, USS Variables (Custom Properties) are the "pro" way to manage styles.
> By using variables like `--color-primary`, you can update your entire game's color palette in one place without hunting through hundreds of lines of code. For more on implementing USS Variables in Unity 6, check out the [Unity Manual on Custom Properties](https://docs.unity3d.com/2021.2/Documentation/Manual/UIE-USS-CustomProperties.html).
>

---
