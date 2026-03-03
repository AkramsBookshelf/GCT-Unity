1.  **Open the Trash Bag Prefab**
    
    -   In the **Hierarchy**, locate a trash bag object in the **Park Scene**.
    -   Right-click the object and select `Prefab > Open Asset in Context` to edit the prefab directly.
2.  **Add a Box Collider (Trigger)**
    
    -   With the trash bag prefab selected, go to the **Inspector** panel.
    -   Click `Add Component` and search for `Box Collider`.
    -   Enable `Is Trigger` to allow interaction without physical collision.
    -   Set the size of the box collider to allow for easy interaction with the player and trash bag.
3.  **Add an Audio Source**
    
    -   Click `Add Component` and search for `Audio Source`.
    -   In the `Audio Source` component, **uncheck** `Play on Awake` so the sound only plays when triggered.
    -   Set the `Loop` to **unchecked** as this clip will only play once.
    -   Click the `Audio Clip` field and assign the **SX\_collected-trash.wav** sound file.
4.  **Add a Script Machine**
    
    -   Click `Add Component` and search for `Script Machine`.
    -   In the `Script Machine` component, click `New` to create a new Script Graph.
    -   Name the graph **TrashBag** and save it in your **Scripts** folder.

_The following is a screenshot of the trash bag prefab with the above setting applied_

With the **Trash Bag Prefab** still open in **Prefab Mode**, follow these steps to set up the interaction logic:

1.  **Open the Script Graph**
    
    -   In the **Inspector**, locate the `Script Machine` component.
    -   Click `Edit Graph` to open the visual scripting editor.
2.  **Remove Default Nodes**
    
    -   In the script graph, delete the default `OnStart` and `OnUpdate` nodes, as they are not needed for this interaction.
3.  **Add an OnTriggerEnter Node**
    
    -   In the **Graph Editor**, right-click and search for `OnTriggerEnter`.
    -   This node will trigger when another object enters the trash bag's `Box Collider`.
4.  **Check If the Player Entered the Trigger**
    
    -   Add a `Collider → Get Game Object` node and connect it to `OnTriggerEnter`.
    -   Add a `Game Object → Compare Tag` node and set the tag to **Player**.
    -   Connect the `Get Game Object` node to the `Compare Tag` node.
    - 
Before we get much further, we will test to make sure that the `OnTriggerEnter` event is even working. We can easily test this with a `Debug Log` message sent to the console.

1.  **Test the Condition with Debug Logs**
    
    -   Add an `If` (Branching Node) and connect the `Compare Tag` output.
    -   Create a `Debug Log` node and a **String Literal** node.
    -   Set the **String Literal** to `"Player Entered"` and connect it to the **True** output of the If node.
    -   Add a second **Debug Log** and **String Literal**, setting the message to `"Player did not Enter"`.
    -   Connect this to the **False** output of the If node.
2.  **Test the Interaction**
    
    -   Save the graph and close the **Script Graph** window.
    -   Enter **Play Mode** in Unity and move the player toward a trash bag.
    -   Check the **Console** to verify the correct debug message appears when entering the trash bag's trigger.
  
3. Once the debug messages confirm the interaction is working, we can proceed to implement the actual trash collection logic.

Add  an Audio source Play One Shot (used for short soudn fx) 
The Audio Source should be set to "this"
Add a Audio Source Get Clip node. Set it to this. This will get the clip on the game object. 
Set this as the value of the clip on the Play ONe Shot node. 

Have the Debug.Log for the Player Triggered output to the Playe One Shot 

 add a Destroy node with an object and time delay parameter.
Set the target to This, which refers to the trash bag object.
Set the delay time to 0.5 seconds to match the length of the sound clip.
Have the Play One Shot output to the Destory Gameobject. 
Save your graph, then test it in Play mode to ensure the trash bag is destroyed only after the sound has finished playing.

Select the entire graph and ctrl+click to group all the nodes. Name this group OnTriggerEnter change it's color to your liking.

Drag the trashbag prefab from the hierarchy to the Prefabs folder in the project window to create a prefab varient. Rename the variabnt to TrashBag_Interactable.

----

Create a new empty game object in the hiearchy. 
Name this object LevelManager. 
Add a script machine component
Under graph choose new
Name this graph LevelManager and save it in the scripts folder
In the inspector choose edit graph.
Remove the on start and update methods


