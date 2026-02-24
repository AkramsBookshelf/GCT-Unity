# 📜 Controlling UI Documents 
> By: Akram Taghavi-Burris | © 2026

Unity’s UI Toolkit is built on an event-driven architecture that closely follows the **[Observer pattern](../DesignPatterns/ObserverPattern.md)**. 

In the Observer pattern, one object (the _subject_) broadcasts notifications when something happens. Other objects (the _observers_) subscribe to those notifications and react accordingly. The subject does not know what the observers will do in response; it simply announces that an event occurred.

## New UI Toolkit and Observer Pattern

UI Toolkit applies the Observer pattern directly to user interaction. Unlike the old Canvas UI system, where buttons were often assigned a specific method to run in the Inspector when clicked, UI Toolkit exposes events such as `clicked` that scripts can subscribe to. 

For example, a `MainMenuController` class subscribes to a button’s `clicked` event and runs a method like `OnPlayButtonClicked` when the event occurs. 

Importantly, the button itself does not know or care what method will run; the controller handles the behavior. This decouples the UI element from the game logic, so interaction and response are separated, even if the controller and button exist on the same GameObject.

#

### From Input to Event
To understand how a UI Document is controlled, we must trace the path of a single interaction.
1.  The **Input System** detects hardware input (mouse, keyboard, controller).
2.  The UI system receives that input.
3.  UI Toolkit determines which element is being interacted with.
4.  That element raises an event (for example, `clicked`).
5.  A controller script that subscribed to the event executes a method.

Notice what does _not_ happen: the controller never directly checks for mouse clicks or key presses. It does not poll input. It reacts to notifications.
This is the core idea of event-driven design.

---

## The Role of the UIDocument
The `UIDocument` component is responsible for loading a UXML file and rendering it into the scene. Without it, a UXML file remains only a static asset.

When the `UIDocument` initializes, it constructs a hierarchy of UI elements known as the **visual tree**. This tree represents the structure defined in the UXML file.

At the top of this hierarchy is the `rootVisualElement`.

#

### The rootVisualElement: Entry Point to the UI

The `rootVisualElement` is the top-level container of the interface at runtime. Every button, label, and container exists somewhere beneath this root.

Because UI Toolkit elements are not individual GameObjects, we cannot use methods like `GetComponent()` to retrieve them. Instead, we search the visual tree.

This is done using the query system `root.Q<Button>("playButton");`

The `Q<T>()` method searches the visual hierarchy for an element of type `T` with a matching name. The name must exactly match what was defined in UXML. If it does not, the query returns `null`.

Through querying, a controller script establishes references to specific UI elements so it can subscribe to their events.

Through querying, a controller script establishes references to specific UI elements so it can subscribe to their events. With this understanding in mind, we can now begin implementing a **MainMenuController** that connects to our UI Document, queries the buttons, and responds to user interactions step by step.

---
# ⚒️ Tutorial: Adding UI Documents to Scenes
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| UI Toolkit        | 15 minutes        | Unity, IDE |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed [Adding UI Documents to Scenes tutorial](TUT_UIDoc.md)
> - Ensure you are on the **UIManager** branch
>

### Step 1: Create the MainMenuController Script

1. Open your project in the Unity Editor
2. In the **Project** window create a new folder named **GameManager**
2. Inside **UI** folder create a folder named **Scripts**
3. Inside the **UI/Scripts** folder create new script** file using your **script template**
   - Name it: **MainMenuController**
5. Open it in your IDE

#

### Step 2: Add Required Namespaces
1. At the top of the script, add:

```csharp
using UnityEngine;
using UnityEngine.UIElements;

```
> [!IMPORTANT]
> The `UnityEngine.UIElements` namespace gives us access to UI Toolkit classes like `Button` and `VisualElement`.
>

# 

### Step 3: Add the UIDocument Field
To control the UI, we need a reference to the `UIDocument` component that renders our UXML.

1. Add the field for the UI Document

```csharp
[Header("Assign UIDocument GameObject")]
public UIDocument UIDocument;
```
> [!NOTE]
> We could use `GetComponent<UIDocument>()`, but explicitly assigning the reference makes dependencies clear and avoids hidden coupling.
>

2. Save the `MainMenuController` class
3. Return to the Unity Editor and in the **Hierarchy** window
4. Attach the `MainMenuController` class to the **UI Document** game object in the **MainMenu** scene.
5. With the **UI Document** selected in the **Inspectory** window drag the **UI Document** GameObject from the **Hierarchy** window to the `UI Document` field
6. Save the Scene and return to your IDE. 

#

### Step 4: Add the Button Field 
Next, we store references to the buttons we want to control.

private Button _playButton; 
private Button _optionsButton;
private Button _quitButton;

# 

### Step 5: Query the Buttons in Awake
When the **UI Document** GameObject initializes, we search the UI hierarchy and assign the buttons. To search, we need to reference the `rootVisualElement`. This provides us with the top-level container of the UI. Using `Q<Button>("name")`, we search the hierarchy for (in this case) buttons that match a specific name in the UXML document. The name must match exactly, or the result will be null. 

```csharp
private void Awake()
{
    VisualElement root = UIDocument.rootVisualElement;
    _playButton = root.Q<Button>("playButton");
    _optionsButton = root.Q<Button>("optionsButton");
    _quitButton = root.Q<Button>("quitButton");

}//end Awake()
```

# 

### Step 6: Subscribing to Clicks
The New Input System uses an Observer Pattern, in which any action sends a notification to listeners who subscribe. In the case of UI button,s the input system will send out a `clicked` notification, which we can subscribe to. When the action happens, our `MainMenuController` will run the method that is triggered by that action. 

1. Register the Button Events

```csharp

    private void OnEnable()
    {
        if (_playButton != null) _playButton.clicked += OnPlayButtonClicked;
        if (_optionsButton != null) _optionsButton.clicked += OnOptionsButtonClicked;
        if (_quitButton != null) _quitButton.clicked += OnQuitButtonClicked;
        
    }//end OnEnable()

```

2. Unregister on Disable

```csharp

    private void OnDisable()
    {
        if (_playButton != null) _playButton.clicked -= OnPlayButtonClicked;
        if (_optionsButton != null) _optionsButton.clicked -= OnOptionsButtonClicked;
        if (_quitButton != null) _quitButton.clicked -= OnQuitButtonClicked;
        
    }//end OnEnable()

```
> [!TIP]
> Unregistering events `OnDisable()` helps to prevent any memory leaks or duplicate calls.
>

# 

### Step 7: Create the Click Handler
1. Create the `OnPlayButtonClicked` method

```csharp
private void OnPlayButtonClicked()
{
    Debug.Log("Play button clicked! Loading game...");

}//end OnPlayButtonClicked()

```

2. Create the `OnOptionsButtonClicked` method

```csharp
private void OnOptionsButtonClicked()
{
    Debug.Log("Options button clicked! Loading Options menu");

}//end OnOptionsButtonClicked()

```

3. Create the `OnQuitButtonClicked` method

```csharp
private void OnOptionsButtonClicked()
{
    Debug.Log("Quit button clicked! Exiting Game...");

#IF UNITY EDITOR
  exit play mode
# Else
    Application.Quit();
# End IF

}//end OnQuitButtonClicked()

```







