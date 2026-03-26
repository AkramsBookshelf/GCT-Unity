### 1. The Core Classes (The Crew)

-   `UIManager` (The Stage Manager): This is the central hub. It knows which "sets" (UI panels) are available in its library. It is responsible for physically placing a menu on the screen, deciding if it should stay there (caching) or be thrown away (destroying), and making sure the most important menu is always in front.
    
-   `UIViewBase` (The Standard Script): This is a template that every UI panel uses. It automatically "scans" a new menu to find all its buttons and sliders so you don't have to link them manually. It also handles "Focus," so if you click a menu, it tells the `UIManager` to bring that menu to the front.
    
-   `UIEvents` (The Intercom): This is a shared radio channel. Instead of a button talking directly to the `GameManager`, it just shouts an announcement like "The Start Button was pressed!" onto this channel.
    
-   `UICommandHandler` & `UISettingsHandler` (The Coordinators): These listen to the "Intercom" (`UIEvents`). When they hear a specific announcement, they translate that into an actual action, like telling the game to quit or changing the volume.
    
-   `UIAssetData` (The Blueprint): A simple file that tells the `UIManager` three things: the name of the UI, what the prefab looks like, and whether to keep it in memory when it’s closed.
    

### 2. Communication with the GameManager

To keep the system clean, the `UIManager` is the primary "bridge" to the `GameManager`:

1.  Listening: The `UIManager` watches the `GameManager`'s state. If the director says "We are now in the Main Menu state," the `UIManager` automatically looks in its library and turns on the `main_menu`.
    
2.  Requesting: When you click "Start Game," the `UICommandHandler` receives that event and tells the `GameManager` to change the state. Because the `UIManager` is listening for that change, it sees the new state and automatically hides the menu.

3. Splitting responsibilities:
   - UIManager: The "Stage Manager" (Handles Visuals—What is on screen?).
   - UICommandHandler: The "Director" (Handles Logic—What does this button do?).
    

### 3\. Handling Other Systems (Like Inventory)

For modular systems like an Inventory, the goal is "Self-Registration."

-   Modular Design: The Inventory system is a separate package. It doesn't need to be pre-listed in the `UIManager` library.
    
-   The Handshake: When the Inventory is spawned, it uses a specialized "Register" command to introduce itself to the `UIManager`.
    
-   Shared Control: Once registered, the `UIManager` treats the Inventory like any other menu. It manages the layering (making sure the Inventory stays on top of the HUD) and focus, even though the `UIManager` didn't originally create it.
    
-   The Interface (`IManagedUI`): This acts as a contract. As long as the Inventory follows the rules of the `IManagedUI` interface, the `UIManager` knows exactly how to handle it without needing to know how the inventory actually works inside.
