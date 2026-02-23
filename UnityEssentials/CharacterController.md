
# ⚒️ Tutorial: Animate the Gate

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Animation     | 30 minutes       |   GitHub Desktop, Unity, Gate Prefab   |
</details>

> [!NOTE]
> Before starting this tutorial, ensure you have :
>  - Completed **[Animation Tutorial](Animation.md)**
>  - That you are on the **Audio** branch in GitHub Desktop.
>

### Step 1 — Import the Starter Assets: Character Controllers

1.  With the Unity Editor open, open your browser and navigate to the Unity Asset Store.
2.  Log in with your **Unity ID**.
3.  Find the **Starter Assets: Character Controllers** asset: **[Starter Assets: Character Controllers]([https://assetstore.unity.com/packages/3d/environments/urban/low-poly-park-61922?utm_source=chatgpt.com](https://assetstore.unity.com/packages/essentials/starter-assets-character-controllers-urp-267961?srsltid=AfmBOooO6C5bI-SU14OC2283-LXsdpsGay9LbwfP015KE5BobTSAIREF))**.
4.  Click **Add to My Assets** → then **Open in Unity**.
5.  Unity will return to the **Package Manager** → select the asset → click **Download** → then **Import** → **Import All Assets**.
    
> [!TIP]  
> The asset will appear in the **Project** window under **Assets**.
>

#
        
### Step 2 — Organize the Starter Assets Folder
1.  In the **Project** window, locate the imported **Starter Assets** folder.
2.  Drag the entire **Starter Assets** folder into your existing **3rdParty** folder.

> \[!NOTE\]  
> Keeping third-party assets inside a **3rdParty** folder helps maintain a clean and professional project structure.
>

#

### Step 3 — Explore the Sample Controllers

1. In the **Project** window, locate the folder **Starter Asests/ Samples/** folders
2. Inside you will find two folders: 
   - FirstPersonController
   -  ThirdPersonController
3. Each contains a **Playground** scene where you can test the controller.
4. Double-click on the **Playground** scene to open it.
5. Click **Play** in the Unity Editor to test the controllers
    - Use WASD or the arrow keys to move around
    - Use the Shift key while moving to speed up
    - Use the Space bar to jump
    - Use the Mouse to look around

> [!NOTE]
> You may open these scenes to experiment with each controller. For this project, we will eventually implement the Third Person Controller.
>

# 

### Step 4 — Prepare Level_01 Scene
1. Open your Level_01 scene.

>> [!WARNING]
> If you are prompted to save changes to the **Playground** sample scene before switching branches, choose **No**.
> 
> The Playground scene is part of the imported **Starter Assets** package and should remain unchanged.
>

2. Make sure your ground (i.e., grass tiles, tiles, etc.) prefab has a Collider component.

> [!NOTE]
> The **ParkGrouns** tiles should already have a **Mesh Collider** attached.
>

3. Delete the default Main Camera in the scene (if present).

> [!IMPORTANT]
> Your scene should contain only **one** camera tagged **Main Camera**.
>
> Since we are using the **MainCamera** prefab included with the **Starter Assets**, you must delete the default **Main Camera** that Unity automatically creates in the scene. Having multiple cameras tagged as **Main Camera** can cause unexpected behavior.
> 

#

### Step 5 — Add the Third Person Controller Prefabs

1. In the **Project** window locate the **3rdParty/Starter Assets/ Runtime/ ThridPersonController/Prefabs/** folder
2. Drag **PlayerArmature** prefab into the Hierarchy.
3. Drag **PlayerFollowCamera** prefab into the Hierarchy.
4. Drag **MainCamera** prefab into the Hierarchy.

#

### Step 6 — Configure the Camera Follow Target
1. Select **PlayerFollowCamera** in the Hierarchy.
2. In the Inspector, locate the **Cinemachine Virtual Camera** component.
3. In the **Hierarchy** expand the **PlayerArmature** prefab and locate the **PlayerCameraRoot** object
4. Drag and drop the **PlayerCameraRoot** object into the **Cinemachine Virtual Camera > Follow** property on the **PlayerFollowCamera** in the **Inspector** window.

> [!NOTE]
> **PlayerCameraRoot** is a child object of the **PlayerArmature** prefab. It defines the _transform_ position that the **PlayerFollowCamera** will follow.
>

#

