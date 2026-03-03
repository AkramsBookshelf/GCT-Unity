Select the trashback prefab. 
Add a box collider
Resize the box collider a little a to proivde room for the player to interact. 
 X1,y1,z1 size is good
 SEt collider as trigger

create a folder named Scripts
From window > Visual Scripting > Visual Script Graph. 
 - Create a new script graph named Trash
 - save this in teh scripts folder

Trash asset
 delete the start and update method. 
 In the script grpah viwe right clook and choose OnTriggerEnter
  rightclick and add a debu log, right click and add a string literal
   set the string to "hit trash bag" 

   set the string to the message output of the Debug.log

   Set the ontrigger output to the DebugLog input 

   With the trashbag selected in the scene add a scriptmachine component
   Set the graph to the Trashbag. 

   run play , when entering thtrigger area the debug should log in the console. 
