# ⚒️ Tutorial: Styling Your UI: USS & Themes
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

## Step 1 — Global Styles
1. Open your Unity project in your **IDE**
2. In the **Assets / UI** folder, create a new file:
   - GlobalSystles.uss

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
    
    flex-direction: column;
    justify-content: center;
    align-items: center;
    
    -unity-font-definition: url("project:/Assets/UI/Fonts/Roboto SDF.asset");
    font-size: 14px; 
    -unity-font-style: bold;
    
    background-color: #fff;
}

.game-title {
    margin-bottom: 4%;

    -unity-font-definition: url("project:/Assets/UI/Fonts/Bangers-Regular SDF.asset");
    font-size: 100px;

    color: #ffd166;

    -unity-text-outline-color: #000000;
    -unity-text-outline-width: 2;
}

/* Button container: vertical layout */
.button-container {
    flex-direction: column;
    width: 60%;
    max-width: 360px;
    min-width: 220px;

    height: 40%;
    min-height: 200px;

    justify-content: space-evenly;
    gap: 12px;
}

/* Buttons styling */
.button-container Button {
    width: 100%;
    height: 50;
    
    font-size: 16px;
    background-color: #fff;
    color: #000;

    border-radius: 8px;
    border-top-width: 2px;
    border-right-width: 10px;
    border-bottom-width: 10px;
    border-left-width: 2px;
    border-color: #000;
    border-style: solid;

    unity-text-align: middle-center;
}

.button-container Button:hover {
    background-color: #ffd166;
    border-right-width: 6px;
    border-bottom-width: 6px;
}

```
