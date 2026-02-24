

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
> The `UnityEngine.UIElements` namespace gives us access to UI Toolkit classes like Button and VisualElement.
>

# 

### Step 3: Add the UIDocument Field
In order to control the main menu buttons, we will need a reference to the UI Document GameObject that has the UXML document attached. In most cases, it will be the document the _menu controller_ class will be attached to, so we could dynamically access it, by finding that component, or we can just apply it via a serialized field. In this case, we will use the latter. 

1. Add the field for the UI Document

```csharp
[Header("Assign UIDocument GameObject")]
public UIDocument UIDocument;
```

#

### Step 4: Add the Button Field 
Next, we need a reference to all the buttons we will want to control. 

private Button _playButton; 
private Button _optionsButton;
private Button _quitButton;

# 

### Step 5: Query the Buttons in Awake
When the UI Document first loads, we want to search (query) the document for all the buttons and assign them to our fields. To do this we first need a reference to the `rootVisualElement` of the doucment. 

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







