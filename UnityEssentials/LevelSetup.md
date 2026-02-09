# ⚒️ Tutorial: Level Setup

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Project Setup | 10 minutes         |   GitHub Desktop, Unity     |

</details>

> [!NOTE]
> Before starting this tutorial, make sure you have accepted the **GitHub Classroom assignment**, and have completed the **[project setup](https://github.com/CSG-3013/UnityRepoTemplate?tab=readme-ov-file#%EF%B8%8F-setup-instructions)**
> 

### Step 1 — Open Project from GitHub Desktop

1.  Open **GitHub Desktop**.
    
2.  Ensure your **Park Game repository** is selected in the top-left dropdown.
    
3.  Make sure you are on the **main branch**.
    
    -   If not, switch to **main** using the branch dropdown.
        
4.  Click **Fetch origin** to make sure your local project is up to date.

#

### Step 2 — Open the Project in Unity

1.  Open **Unity Hub**.
    
2.  Click **Add Project** → navigate to your local repository folder → select it.
    
3.  Once added, click the project to open it in **Unity Editor**.
    
#

### Step 3 — Create a New Scene

1.  In the **Project** window, navigate to the **Scenes** folder.
    
2.  Right-click → **Create > Scene > Scene**.
    
3.  Name the new scene: **Level_01**.
    
4.  Double-click the scene to open it in the **Scene** view.
    
> [!NOTE]
> By default, basic scenes will be created with two default Game Objects:
> -   **MainCamera**
> -   **Directional Light**
>
> We will organize these into folders to keep our **Hierarchy** window clean and manageable.
>

#

**Step 5 — Organize the Hierarchy**

1.  Right-click in the **Hierarchy** window and choose **Create Hierarch Folder**
     - Name the folder `Cameras`.
     - Set the folder color to **Silver**.
    
2.  In the **Hierarchy** window, drag and drop the **Main Camera** Game object in the `Cameras` folder.

3.  Right-click in the **Hierarchy** window and choose **Create Hierarch Folder**
     - Name the folder `Lights`.
     - Set the folder color to **yellow**.
    
4. In the **Hierarchy** window, drag and drop the **Directional Light** Game object in the `Cameras` folder.

>[!WARNING]
> If you do not see an option for **Create Hierarch Folder** ensure that you have properly installed the **Simple Unity Hierarchy Folder** package as directed in the **[project setup](https://github.com/CSG-3013/UnityRepoTemplate?tab=readme-ov-file#%EF%B8%8F-setup-instructions)**.
>

#

**Step 6 — Save & Commit**

1. Save your scene: **File > Save** or press **Ctrl + S**.
2. Close the Unity editor
3. Switch back to **GitHub Desktop**
   - Commit your changes with the message:
     - *feat: Scene Level_01 setup*
4. **Push** the changes back to the **main** branch.


