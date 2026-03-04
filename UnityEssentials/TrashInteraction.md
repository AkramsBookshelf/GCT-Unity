# ⚒️ Tutorial: TrashInteraction

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Visual Scripting   | 45 minutes       |   GitHub Desktop, Unity, Trash Package |
</details>

> [!NOTE]
> Before starting this tutorial, ensure you have :
>  - Completed **[Physics Interaction Tutorial](PhyscisInteractions.md)**
>  - That you are on the **Interactions** branch in GitHub Desktop.
>

### Step 1 — Add the TrashBag to the Scene
1.  In the **Project** window **3rdParty/POLYGON City / PRefabs** folder,
   - Locate the **trashBag**
2. Drag the **trashBag** prefab into the scene.
3. Place it somewhere in **Area One** of the scene.
4. Save the scene.

#

###  Step 2 — Add a Trigger Collider
1.  With the trash bag selected, go to the **Inspector**.
2.  Click **Add Component** → search for **Box Collider**.
3.  Enable **Is Trigger**.
4.  Adjust the **Box Collider size** so the player can easily enter the interaction area.

> \[!TIP\]  
> The trigger should be slightly larger than the trash bag mesh to make interaction feel responsive.
>

#

###  Step 3 — Add an Audio Source

1.  Click **Add Component** → search for **Audio Source**.
2.  Configure the component:
    -   **Play On Awake**: ❌ Unchecked
    -   **Loop**: ❌ Unchecked
    -   **Audio Clip**: Assign `SX_collected-trash.wav`
        
> [!NOTE]
> This sound will play when the trash bag is collected.
>

#

###  Step 4 — Create a Scripts Folder 
1. In the **Project** window create a new folder
2. Name this folder **Scripts**

> [!NOTE]
> All of our visual script graphs will live in this folder. The same is true if you are creating any c# scripts.
>

# 

###  Step 5 — Add a Script Machine
1.  Click **Add Component** search for **Script Machine**.
2.  Click **New** to create a new Script Graph.
3.  Name the graph: **TrashBag**
4.  Save it inside your **Scripts** folder.

# 

###  Step 6 — Create the Trigger Logic
1. . Open the Graph
-   In the **Inspector**, locate the Script Machine.
-   Click **Edit Graph**.
    

2.  Remove Default Nodes in the **Graph Editor area** (blackboard),  
-   Delete **On Start**
-   Delete **On Update**

They are not needed for this interaction.


3.  Add OnTriggerEnter
 -  In the **Graph Editor area**, **Right-click** to open the node search menu.
 -  In the search bar, type: `OnTriggerEnter`
 - Click **OnTriggerEnter (Event)** to add it to the graph.

> \[!NOTE\]  
> This is an **Event Node**. It runs automatically when another collider enters the Trash Bag’s trigger collider.
> 
 
 #

 ###  Step 7 —  Get the Other Object

The `OnTriggerEnter` node gives us a **Collider** reference for the object that set off the trigger (i.e., the other object).  
We need to find out which GameObject owns that collider.

1.  **Right-click** in the graph.
    
2.  Search for:  `Get GameObject`
    
3.  Select **Collider → Get GameObject**
    
4. Connect it:
    -   Click and drag from the **Collider output port** on `OnTriggerEnter`
    -   Connect it to the **Collider input port** on `Get GameObject`
  
>[!TIP]
> This converts the collider into a GameObject reference.
>

# 

###  Step 8 —  Compare to Player

Now we verify that the object entering the trigger is the Player. 

1.  **Right-click** in the graph.
    
2.  Search for: `Compare Tag`
    
3.  Set the **Tag** value to **Player**

4. Connect it:
    -   Drag from the **GameObject output** of `Get GameObject`
    -   Connect it to the **GameObject input** on `Compare Tag`
    -   Drag the flow output from **OnTriggerEnter** to the input of `Compare Tag`
  
# 

## Step 9 — Test With Debug Logs

Before building full functionality, confirm the trigger works.

1.  Add an **If (Branch)** node.
    
2.  Connect the **Compare Tag** result into the condition.
    
3.  Add two **Debug Log** nodes 
4. Add two **String** (literal) nodes with the following message
   - Other triggered bag
   - Player triggered bag
4.  Connect it
     - Connect the **String** nodes one to each **Debug Log** node.
     - Connect the **Debug Log** node with the _Player_ message to the **TRUE** output of the `if` branch
    - Connect the **Debug Log** node with the _Other_ message to the **FALSE** output of the `if` branch
7.  Save the graph.
8.  Enter **Play Mode**.
9.  Move the player into the trash bag trigger.
10.  Verify correct messages appear in the **Console**.
    
> \[!NOTE\]  
> Always test event detection before adding complex behavior.
> 

![Trashbag Interactions](imgs/interactions/gct-trashbag-04.png)





















