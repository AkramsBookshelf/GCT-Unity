# 📜 BaseButtonController Class

> By: Akram Taghavi-Burris | © 2026

Now that we are working with **UI Toolkit**, we need a clean and reusable way to manage button interactions.

The **BaseButtonController** is an **abstract foundation class** that automatically:

-   Finds all **Buttons** inside a UXML document
-   Stores them in a **dictionary**
-   Registers both **mouse** and **keyboard/gamepad** callbacks
-   Routes all button presses through a single method
    
Instead of writing repetitive button registration logic in every UI script, we centralize the system here.

> [!IMPORTANT]
> This class does **not** define what buttons do. Child classes are responsible for implementing the actual behaviors.
>

---
# ⚒️ Tutorial: Creating the Base Button Controller
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| UI Toolkit        | 10 minutes        | Unity, IDE |

</details>

> [!NOTE]
> Before starting this tutorial:
> - Make sure you completed [UI Documents tutorial](TUT_UIDoc.md)
> - Ensure you are on the **UIManager** branch
>   

---
### Step 1: Create the GameManager Script

1. Open your project in the Unity Editor
2. In the **Project** window iniside the **UI/Scripts** folder
   - Create a new **new script** file using your **script template**
   - Name it: **BseButtonController**
5. Open it in your IDE

#

### Step 2: Add Required Namespaces
1. At the top of the script, add:

```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UIElements;

```
We will need the following: 
- `Collections.Generic` for creating a Dictionary
-  `UnityEngine.UIElements` to access UI Toolkit types like `Button`, `VisualElement`, and events

# 

### Step 3: Require a UIDocument Component
1. At the top of the class, enforce the presence of a `UIDocument`.

```csharp
[RequireComponent(typeof(UIDocument))]
```

> [!TIP]
> This helps prevent runtime errors by ensuring the required component is automatically attached.
> 

#

### Define the Abstract Class and Core Variables

1. Ensure that the class is defined as **abstract**
   
```csharp
public abstract class BaseButtonController : MonoBehaviour
{
```

2. Add the following fields:

```csharp
// Reference to the UIDocument component attached to this GameObject.
protected UIDocument _uiDocument;

// The root VisualElement of the UI Document.
private VisualElement _root;

// Dictionary storing all discovered buttons.
protected Dictionary<string, Button> _buttonDictionary = new();
```
#### Why Use a Dictionary
To store our buttons, we are using a dictionary that will set the: 
- Key = UXML button name (string)
- Value = Button reference

This will provide us with a fast lookup method using the button name and will prevent duplicate registration. 

# 
### Step 5: Initialize in Awake()
1. In the **Awake()** method retrieves the UI document and its root element.
2. Ensure that the **Awake()** method is `virtual`, allowing child classes to inject their own behaviors.

```csharp
  // Awake is called once on initialization (before Start)
    protected virtual void Awake()
    {
        // Retrieve the UIDocument component attached to this GameObject.
        _uiDocument = GetComponent<UIDocument>();
        
        // Get the root visual element of the UI.
        _root = _uiDocument.rootVisualElement;
        
        //If the UXML file is not found
        if(_root == null)
        {
            Debug.LogError("No visual element root found, ensure UXML document is set.");
            return;
        }

    } //end Awake()
```

# 

### Step 6: Build a Button Map

Before we can handle UI events, we need a way to quickly access all buttons in the UI.  
To do this, we will create a `BuildButtonMap()` method that scans the UXML hierarchy, finds all `Button` elements, and stores them in a dictionary by their UXML name.
    
1. Create `BuildButtonMap()`

```csharp
    // Queries buttons and maps references 
    private void BuildButtonMap()
    {
        // Create a list of all buttons in the UXML hierarchy
        List<Button> buttons = _root.Query<Button>().ToList();
        
        // If no buttons are found, log an error and return
        if (buttons.Count == 0)
        {
            Debug.LogError("No Buttons Found");
            return;
        }

        // Loop through each discovered button
        foreach (var button in buttons)
        {
            // Get the button name assigned in the UXML.
            string buttonName = button.name;
            
            // Store the button reference in the dictionary using its UXML name.
            // Using [buttonName] will overwrite any existing entry with the same name, preventing duplicates.
            _buttonDictionary[buttonName] = button;
            
        }//end foreach
        
    }//end BuildButtonMap()
```
> [!NOTE\]  
> Using the square brackets (`_buttonDictionary[buttonName] = button`) ensures that if a button with the same name already exists, it will be replaced. This prevents duplicate keys without manually checking.
>

# 
### Step 7: Register Button Callbacks
Once buttons are mapped, we need to **attach event handlers** so user interactions trigger our logic.

The `RegisterButtonCallbacks()` method will loop through the dictionary and register callbacks for both **mouse clicks** and **keyboard/gamepad submit events**.

When a callback is registered, it **triggers an event** whenever the user interacts with the button. UI Toolkit events are objects derived from `EventBase`, carrying information such as:
-   Which button was clicked
-   Which pointer, keyboard key, or gamepad button triggered it
-   Mouse position or other event-specific details
  
These callbacks will **reference a method** to handle the event, which we will define later as `HandleButtonTriggered()`.

1. Create `RegisterButtonCallbacks()`

```csharp
// Register the necessary UI Toolkit callbacks to each Button
    private void RegisterButtonCallbacks()
    {
        // Loop through all Button objects in the dictionary
        foreach (Button button in _buttonDictionary.Values)
        {            
            // Register callback for mouse clicks.
            // ClickEvent fires when the button is clicked with a pointer device.
            button.RegisterCallback<ClickEvent>(HandleButtonTriggered);
            
            // Register callback for navigation submit events.
            // NavigationSubmitEvent fires when pressing Enter or gamepad submit.
            button.RegisterCallback<NavigationSubmitEvent>(HandleButtonTriggered);
            
        }//end foreach
        
    }//end RegisterButtonCallbacks()
```

> [!WARNING]
> At this stage, `HandleButtonTriggered()` has not yet been defined. This method will receive the event object and forward the button interaction to `OnClickButton()`.  We will implement it in **Step 9**.
> 

#

### Step 8: Unregister Button Callbacks
To avoid **duplicate events** or **dangling references**, we must unregister callbacks whenever the UI is disabled.  
`UnregisterButtonCallbacks()` removes all previously registered callbacks for each button.

1. Create `UnregisterButtonCallbacks()`

```csharp
    // UnregisterButtonCallbacks removes all previously registered callbacks from each Button.
    private void UnregisterButtonCallbacks()
    {
        foreach (var button in _buttonDictionary.Values)
        {
            button.UnregisterCallback<ClickEvent>(HandleButtonTriggered);
            button.UnregisterCallback<NavigationSubmitEvent>(HandleButtonTriggered);
            
        }//end foreach
        
    }//end UnregisterButtonCallbacks()
```

# 

### Step 9: Handle Button Triggered
As discussed in Step 7, when a callback is registered, it **triggers an event** whenever the user interacts with a button.

To handle this, we implement `HandleButtonTriggered()`.
-   The method must **accept an `EventBase` parameter**, because UI Toolkit requires callbacks to receive event objects containing all interaction data (like which button was pressed, input device, and position).
-   Inside the method, we **extract the actual Button** that triggered the event from `evt.target`.
-   We then forward the **button’s name** to the abstract `OnClickButton()` method.

**Why pass the button’s name?**
-   The base class doesn’t know what each button should do — it only provides the mechanism for handling interactions.
-   By passing the button name, the **child class can implement `OnClickButton()`** and define the specific behavior for each button.
-   This keeps the base class **generic, reusable, and extensible**.   

1. Create the `HandleButtonTriggered()`

```csharp
// HandleButtonTriggered is the central callback handler for all button events.
    private void HandleButtonTriggered(EventBase evt)
    {
        //Check if the target that triggered the event is a button
        if (evt.target is Button button)
        {
            // Forward the button's name to the abstract OnClickButton method
            // for the child class to handle specific behavior.
            OnClickButton(button.name);
        }
        
    }//end HandleButtonTriggered

```

# 

### Step 10: Define the Abstract `OnClickButton()` Method
The base button controller provides all the infrastructure to **find buttons, register callbacks, and handle events**.
However, the **specific behavior** for each button (i.e., what happens when it is clicked)  is **application-specific**. We don’t want the base class to know about individual button logic.

To enforce this, we define an **abstract method** `OnClickButton()`:
-   The method takes a **button name** as a parameter so the child class can determine which button was clicked.
-   Child classes **override** this method to implement the desired behavior for each button.
-   This is an example of the **Template Method Pattern**: the base class defines the workflow, and the child class fills in the specific action.

1. Create the `OnClickButton()`

```csharp
   //Child classes define the OnClickButton behavior
    protected abstract void OnClickButton(string buttonName);
```





