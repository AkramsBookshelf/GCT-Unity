# ⚒️ Tutorial: Importing Environment Assets

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Project Setup | 10 minutes         |   GitHub Desktop, Unity     |

</details>

> [!NOTE]
> Before starting this tutorial, ensure you have completed **[Level 01 setup](LevelSetup.md)** and that your project is synced with the **main branch** in GitHub Desktop.
> 

### Step 1 — Create a New Branch in GitHub Desktop

1.  Open **GitHub Desktop**.
    
2.  Ensure your **Park Game repository** is selected.
    
3.  Click the **Current Branch** dropdown → choose **New Branch**.
    
4.  Name the branch: **SceneBuilding**.
    - Make sure it is based on **main** → click **Create Branch**.
    
6.  Publish the branch to the remote repository by clicking **Publish Branch**.
    
#

### Step 2 — Open Your Unity Project

1.  Open **Unity Hub**.
    
2.  Open your local **Park Game Unity project**.

#

### Step 3 — Import the First Asset Package (Low-Poly Park)

1.  With the Unity Editor open, open your browser and navigate to the Unity Asset Store.
    
2.  Log in with your **Unity ID**.
    
3.  Find the **Low-Poly Park** asset: **[Low-Poly Park](https://assetstore.unity.com/packages/3d/environments/urban/low-poly-park-61922?utm_source=chatgpt.com)**.
    
4.  Click **Add to My Assets** → then **Open in Unity**.
    
5.  Unity will return to the **Package Manager** → select the asset → click **Download** → then **Import** → **Import All Assets**.
    

> [!TIP]  
> The asset will appear in the **Project** window under **Assets**.
>

#

### Step 4 — Organize External Assets

1.  In the **Project** window → right-click → **Create > Folder** 
   - Name it **3rdParty**.
    
2.  Drag the imported **Low-Poly Park** asset folder into the **3rdParty** folder.
    

> [!NOTE]  
> This is best practice to differentiate your own assets from external asset packs.
>

#


### Step 5 — Import the Second Asset Package (Playground Low Poly)

1.  Go back to the Asset Store → find the **Playground Low Poly** pack: [Playground Low Poly](https://assetstore.unity.com/packages/3d/environments/playground-low-poly-191533?utm_source=chatgpt.com).
    
2.  Add the asset to your account → open it in Unity.
    
3.  In the **Package Manager**, download and import all assets.
    
4.  Once imported, move the **Playground Low Poly** asset folder into the **3rdParty** folder.

#

> [!CAUTION 
> Many asset packages are created for Unity’s **Built-in Render Pipeline**. If your project uses the **Universal Render Pipeline (URP)** or **High Definition Render Pipeline (HDRP)**, materials may not display correctly.
> 
> Upgrading the materials converts them to the correct shader type for your project’s render pipeline, so textures, lighting, and effects render properly in your scene.
>
>  1.  In the **Project** window, click the **Serch by Type** button
>      - In the drop-down select **Material**
>      - _This will display only the materials in the project window**
>  2.  Select **all materials** (_CTRL + CLICK_) that appear in a **pink magenta** color
>  3.  Go to **Windows > Rendering > Render Pipeline Converter**.
>  4.  In the dialog that appears, choose **Material Upgrade**.
>
> The materials should be correctly displayed. Before moving on, clear the _Material_ filter in the **Project** window search box.
>

#

### Step 7 — Save & Commit

1.  Save your scene: **File > Save** or press **Ctrl + S**.
    
2.  Close the Unity Editor.
    
3.  Switch back to **GitHub Desktop** → stage your changes.
    
4.  Commit with the message:
    - `init: Environmental Packages Imported.`

5.  Push your changes to the remote branch: `SceneBuilding`.
    
