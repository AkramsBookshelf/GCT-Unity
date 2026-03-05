| Class / Component                     | Category                 | Pattern Role              | Purpose |
|--------------------------------------|--------------------------|---------------------------|---------|
| UICommandType / UISettingType       | Structural               | Command (The Token)       | These enums act as standardized **tokens** that represent an intent. |
| UIMappingRegistry                   | Creational / Structural  | Flyweight / Adapter       | Maps raw UXML strings to enums. Acts as the **translator between design and logic**. |
| BaseUIView                          | Structural               | View (Base)               | Provides the structure for finding UIElements and registering their native events. |
| MainMenuController (Specific Controllers) | Behavioral        | Controller                | Bridges the View to the game logic by catching UI events and firing bus events. |
| UIEvents (The Bus)                  | Behavioral               | Observer / Mediator       | Dispatches signals so the sender (UI) does not need to know who the receiver (Logic) is. |
| UICommands (Static Class)           | Behavioral               | Command (The Invoker)     | Executes the actual logic for discrete actions such as **Start, Quit, and Save**. |
| UISettingsHandler                   | Behavioral               | Command / Strategy        | Executes the logic for state changes such as **volume, toggles, and graphics settings**. |
| PlayerPrefs / SaveSystem            | Structural               | Model                     | Holds the actual data state that the settings handler modifies. |
