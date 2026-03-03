
| Layer / Role       | Component             | Real-World Analogy (The Restaurant)                                                                 |
|-------------------|---------------------|----------------------------------------------------------------------------------------------------|
| View               | BaseButtonView       | The Menu: The physical item the customer touches. It lists the options but doesn't cook the food. |
| Controller         | MainMenuController   | The Waiter: Listens to what the customer wants and tells the kitchen.                             |
| Model / Service    | UICommands           | The Kitchen: The place where the actual work (cooking) happens. It doesn't care who ordered; it just knows how to make the dish. |



| Term        | Role                              | Example from Your Project                  |
|-------------|-----------------------------------|--------------------------------------------|
| The "On"    | The Event: The signal being broadcast | OnGlobalButtonClickedEvent                 |
| The "Handle"| The Listener: The function that "catches" the broadcast | HandleGlobalAction(UIButtonAction action) |
| The "Action"| The Command: The actual Unity logic being performed | UICommands.StartGame()                     |


### 1\. The Kitchen (`UICommands`)

Why first? These are the core "Capabilities" of your game.

-   The Lesson: "What can our game actually do?"
    
-   The Goal: Write the logic for `StartGame`, `QuitGame`, and `CloseMenu`. At this stage, you can even test these by calling them from a simple `Update` loop (e.g., "Press Space to Start") before the UI even exists.
    

### 2\. The Menu Card (`UIButtonAction` & `Map`)

Why second? You need to define the "Vocabulary" between the kitchen and the front-of-house.

-   The Lesson: Creating an abstraction layer. We don't want to use "magic strings" everywhere.
    
-   The Goal: Define the Enum and the Static Map. This creates a contract: "If the UI says this string, it definitely means this action."
    

### 3\. The Table (`BaseButtonView`)

Why third? Now you build the physical interface the user interacts with.

-   The Lesson: Automation. Instead of dragging 50 buttons into 50 slots in the Inspector, we write code that "Finds" the buttons for us using UXML names.
    
-   The Goal: Get the `UIDocument` working and ensure the code can "see" the buttons.
    

### 4\. The Waiter (`MainMenuController`)

Why last? The Waiter is the "Glue." They need to know what the Menu says (The View) and what the Kitchen can do (The Commands).

-   The Lesson: Wiring. This is the "A-ha!" moment for students where they see the events from the View trigger the logic in the Commands.
    
-   The Goal: Subscribe to the View’s events and call the appropriate `UICommands`.
