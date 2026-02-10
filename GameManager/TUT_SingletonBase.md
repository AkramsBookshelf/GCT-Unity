# 📜 Singleton Base Class
> By: Akram Taghavi-Burris | © 2026

In a previous lesson, we covered the **[Singleton Pattern](../DesignPatterns/Singleton.md)** in detail, including why it can be useful in Unity **when used appropriately and sparingly**.
Since the singleton implementation is basically the same for every manager class, we don’t want to rewrite that same code in **every** class that implements this design pattern. 

>[!TIP]
> Follow the **DRY** (“Don’t Repeat Yourself”) principle: instead of copying the same singleton logic into every manager class, we write it once and reuse it everywhere.
>

So rather than copy/pasting singleton logic over and over, we’ll create a **generic Singleton base class** that other classes can inherit from.
This gives us a solution that is:
- **Modular**
- **Reusable**
- **Consistent**
- Easy to maintain as our project grows


# ⚒️ Tutorial: Creating a Singleton Base Class
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Game Architecture | 15 minutes        |   Unity, IDE  |

</details>

> [!NOTE]
> Before starting this tutorial, you may want to review the **[Singleton Pattern](../DesignPatterns/Singleton.md)**
> 

### Step 1: Create a Singleton Base Script
1. Open your project in the Unity Editor
2. In the **Project** window, right-click on the **Scripts** folder
3. Create a new sub-folder named **Core**
4. Create a **new script** file using your **script template**
    - Name it: **Singleton**
5. Open it in your IDE

# 

### Step 2: Class Declaration

1. Declare the Singleton Class 
-   Use a **generic type parameter `<T>`** so this base class can work with any class (GameManager, AudioManager, etc.).
-   Add the constraint **`where T : MonoBehaviour`** to ensure **`T`** is a Unity component, which lets us safely use Unity-specific methods like **`DontDestroyOnLoad()`**.

```csharp
using UnityEngine;

public class Singleton<T> : MonoBehaviour where T : MonoBehaviour
{

}//end Singleton

```

    
**📝 Task:** Save the script. No errors should appear.

# 

### Step 3: Add the Static Instance

1. **Add the Static Instance Property**

-   Use **`static`** so this instance is **shared globally** and accessible anywhere.
-   Add **`private set`** to prevent other classes from overwriting it.

```csharp

public static T Instance { get; private set; }

```


#

### Step 4: Check for Singleton
1. Create a custom method named **`ValidateInstance()`**
2. This method will validate if this object can be the Singleton `Instance`. 
   - if true, it will set `Instance` to **this** object's **type**
   - else, it will **destroy**, `this` **GameObject**
3. The method should look like the following:

```csharp

private void ValidateInstance()
{
    // Check if there is no instance
    if (Instance == null)
    {
        // Set this class type as the instance
        Instance = this as T;
    }
    else
    {
        // Otherwise destroy this game object
        Destroy(gameObject);
    }
    
    Debug.Log(Instance);
    
}//end ValidateInstance()
```
#

### Step 5: Add Singleton Check to Awake()
1. Declare **`Awake()`** as **protected virtual** so that **child classes can extend the behavior** without breaking the singleton base logic.
2.  The **`ValidateInstance()`** method should be called in the **`Awake()`** method

```csharp
protected virtual void Awake()
{
    ValidateInstance();

}//end Awake()

```
> [!IMPORTANT]
> Virtual methods in C# can be **completely overridden** in a child class.
>
> When overriding **`Awake()`** in a **Singleton** child class, it’s **critical to call `base.Awake()`** to preserve the singleton behavior and persistence logic. Failing to do so could result in **multiple instances** or a Singleton that **does not persist across scenes**.
>
> Requirements like this should always be **documented in your technical documentation** so that everyone on the team is aware of these dependencies.
> 

# 
### Step 6: Make the Singleton Persistent Across Scenes

Singletons are often designed to **persist throughout the entire game**, meaning they are **not destroyed when switching scenes**. However, there may be cases where you don’t want a particular singleton to persist.

Since we are creating a **base class for all singletons**, we provide a **flexible option** to make a singleton persistent or not, depending on the needs of the specific manager.

1. Add a **boolean** to control if the singleton **is persistant**
   - Set this field to be **serialized** so that it can be configuredin the **Inspector**
   - Provided a **Tool Tip** to clarify how the field is used

```csharp
    [SerializeField]
    [Tooltip("Is the game object persistent through scenes")]
    private bool _isPersistent = true;

```
# 

2. Create a method named **MakePersistent()**
3. Add a **`DontDestroyOnLoad(gameObject)`**
    
    -   This is a built-in Unity method that keeps a GameObject alive (persistent) **when changing scenes**.
    -   Without this, singletons would be destroyed whenever a new scene loads.
  
4. **Unparent** the game object first
    -   `DontDestroyOnLoad` **only works on objects in the root of the hierarchy**.  
    -   If your singleton is nested inside another GameObject (even an empty one), Unity would **still destroy it when the parent is destroyed**.
    -   To prevent this, we check if the object has a parent at runtime (`transform.parent != null`) and **detach it from the parent**.
    -   This doesn’t affect the hierarchy in the Editor; it only changes the object hierarchy temporarily at runtime.

```csharp
private void MakePersistent()
{
    // Detach from parent if this object is nested
    if (transform.parent != null)
    {
        transform.SetParent(null);
    } 

    // Prevent Unity from destroying this GameObject when loading a new scene
    DontDestroyOnLoad(gameObject);

} // end MakePersistent()

```

With this safeguard, your singleton will reliably persist across scenes, no matter where it is placed in the hierarchy.

#

### Step 7: Check Persistence in Awake
1. In the `Awake()` method check if the singleton is _not_ persistent
   - If true, exit the `Awake()` method
   - else, call the `MakePersistent()` method
  
```csharp
protected virtual void Awake()
{
    CheckForSingleton();

    if (!_isPersistent) return;

    MakePersistent();

}//end Awake()

```
---


# 🎉 New Achievement: Singleton Base Class Created!

You now have a reusable generic Singleton base class that can be used for any manager system in your game.

This is the foundation for our next lesson, where we build the **GameManager** and finally begin controlling our game states properly.

```csharp

using UnityEngine;

public class Singleton<T> : MonoBehaviour where T : MonoBehaviour
{

    public static T Instance { get; private set; }

    [SerializeField]
    [Tooltip("Is the game object persistent through scenes")]
    private bool _isPersistent = true;

    protected virtual void Awake()
    {
        ValidateInstance();

        if (!_isPersistent) return;
    
        MakePersistent();
    
    }//end Awake()

    private void ValidateInstance()
    {
        // Check if there is no instance
        if (Instance == null)
        {
            // Set this class type as the instance
            Instance = this as T;
        }
        else
        {
            // Otherwise destroy this game object
            Destroy(gameObject);
        }
    
        Debug.Log(Instance);
    
    }//end ValidateInstance()
    
    
    private void MakePersistent()
    {
        // Detach from parent if this object is nested
        if (transform.parent != null)
        {
            transform.SetParent(null);
        } 
    
        // Prevent Unity from destroying this GameObject when loading a new scene
        DontDestroyOnLoad(gameObject);
    
    } // end MakePersistent()

}//end Singleton

```


## 🛡️ Checkpoint

Key takeaways from this lesson:

-   A **Singleton** ensures only one instance of a class exists.
-   A Singleton provides a global access point using `ClassName.Instance`.
-   A **generic singleton** (`Singleton<T>`) lets you reuse the same pattern for multiple managers.
-   `Awake()` is used because it runs early and is ideal for initialization.
-   `_isPersistent` allows you to control whether the singleton survives scene loads.
    
    
