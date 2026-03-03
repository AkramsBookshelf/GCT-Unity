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
