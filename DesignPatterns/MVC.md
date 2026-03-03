
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
