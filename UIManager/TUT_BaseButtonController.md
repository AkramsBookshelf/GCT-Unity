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
2. Ensure that the **Awake()** method is `virtual` allowing child classes to inject their own behaviors.

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















