CSG.Core 
- DataTypes
    -  SettingChange: strut to associate a GameSetting (enum) and value type
- Enums
    - GameCommand: named values of game commands (i.e., StartGame, QuitGame) 
    - GameSetting: named value of game settings (i.e., FullScreen, MuteAll)
 - Events
     - ParamaterEventChannel: base class for event channels that pass a parameter
     - VoidEventChannel: event channel with no parameter
     - Folders are divided by parameter type for each channel

CSG.Shared 
  - Events: All event assets. These are channels that can be used to broadcast to or subscibe too.
  - 

CSG.UIManagement 
 -UXML 
   - All uxml documents (menus)
 - SytleSheets
 - UI-Setting
    - DefaultSettings Pannel
  - Assets
     - Individual UI assets, that denote the prefab, name/id, and caching and the states the menu can live on
- Prefabs: for each UI doc, has the controller attached
- Scripts
   - MODEL
       - UIAssetData: data for each ui asset, like id, prefab, associated button name, caching, and states allowed
   - VIEW 
      - UIViewBase: sets up the ui document, finding all interactable items and registers the callbacks
    - Controller
        -UIMenuController: inherits UIViewBase, defines the channel to broadcast to, defines submenus, will boardcast event to trigger commands/behaviors
      - DO I EVEN NEED SPECIFIC MENU CONTROLLERS??


 -  UIInteractableRegistry - associates commands and settings with button names [MIGHT BE A BTTER WAY TO DO THIS]
 - UICommandHAndler: subscribes to events and handles the functions. [SHOULD THIS BE GLOBAL OR JUST FOR THE UI]
 - UIManager: handles all menus, activationa/deactivation and sorting
 - UIStateHandler: handles if UI assets are allowed on specific states 

  UISettingHAndler - NEEDS FIXING 
  IManageableUI -- NEEDS BUILDING (for self registeration to UIManger)











| Category        | Class                   | What it does                                                   | How it works                                                                                                      |
|----------------|------------------------|----------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Communication  | UI Event Channels      | Acts as the "Cables" or "Intercoms" for the system.            | ScriptableObject assets that use the Observer pattern. One system calls `.Raise()`, and any number of listeners respond via the `OnRaised` action. |
| Commands       | UIInteractablesRegistry| Register "UI Names" with specific command types                        | A static dictionary that maps UXML names (like "playButton") to UICommandType tokens (like StartGame). |
| View Logic     | UIViewBase             | Automatically discovers UI elements in a UXML document.        | Scans the UIDocument on Awake. It uses the UIInteractablesRegistry to decide if a button is a "Global Command" or "Menu Specific." |
| View Logic     | UIMenuController       | Routes button clicks to the correct destination.               | Inherits from UIViewBase. If a click is "Global," it raises an Event Channel. If "Specific," it looks at the UINavigationMap to tell the UIManager what to open.  What events can be triggered |
| View Logic     | UIMainMenuController   | Specialized controller for the Main Menu.                      | Inherits from UIMenuController. It handles menu-specific needs, such as setting the PickingMode so buttons can be clicked. |
| Data           | UINavigationMap        | A "Link" between a button name and a UI Asset.                 | A simple Serializable struct containing a string (button name) and a UIAssetData reference. Used in a list within the UIMenuController. |
| Data           | UIAssetData            | The "ID Card" for a specific UI panel.                         | A ScriptableObject that stores the Prefab, a unique snake_case ID, and lists which GameStates the UI is "Allowed" to be visible on. |
| Commands       | UICommandType          | Standardized tokens of player intent.                          | An enum (e.g., StartGame, Quit) that ensures every system uses the same "language" for actions. |
| Commands       | UICommandHandler       | The "Global Executor" of player actions.                       | Listens to a list of UICommandEventChannels. When a channel is raised, it performs the actual logic, like calling `Application.Quit()`. What are we listening for. |
| State Flow     | UIStateHandler         | The "Driver" that automates UI based on Game State.            | Listens to the GameManager.OnStateChanged event. It checks the AllowedStates list in UIAssetData to automatically open or close menus. |
| Management     | UIManager              | The "Stage Manager" for all UI GameObjects.                    | A Singleton service. It physically instantiates prefabs, handles the active/cached lists, and manages sortingOrder so the right UI is on top. |
