# 📜 Singleton Base Class
> By: Akram Taghavi-Burris | © 2026

In a previous lesson, we covered the **Singleton Pattern** in detail, including why it can be useful in Unity **when used appropriately and sparingly**.
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


# ⚒️ Creating a Singleton Base Class
> By: Akram Taghavi-Burris | © 2026

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Project Managment | 15 minutes        |   Unity, IDE  |

</details>

# 
# ✅ Step 1 — Create a Singleton Base Script

1. In Unity, go to your **Scripts** folder
2. Right-click → **Create Script**
3. Name it:

**Singleton**

4. Open it in your IDE

---

# ✅ Step 2 — Replace the Code with a Generic Singleton

Replace your script contents with the following:

```csharp
using UnityEngine;

public class Singleton<T> : MonoBehaviour where T : MonoBehaviour
{
    // Static instance that holds the reference to the Singleton
    public static T Instance { get; private set; }

    [SerializeField]
    [Tooltip("Is the game object persistent through scenes")]
    private bool _isPersistent = true;

    // Awake is called once on initialization (before Start)
    protected virtual void Awake()
    {
        CheckForSingleton();
        CheckForPersistence();
    } //end Awake()


    /// <summary>
    /// Ensures that only one instance of the Singleton exists
    /// </summary>
    private void CheckForSingleton()
    {
        if (Instance == null)
        {
            Instance = this as T;
        }
        else
        {
            Destroy(gameObject);
        }

        Debug.Log(Instance);

    }//end CheckForSingleton()


    /// <summary>
    /// Checks is the object does not destroy on load
    /// </summary>
    private void CheckForPersistence()
    {
        if (_isPersistent)
        {
            if (transform.parent != null)
            {
                //Detach from parent object
                transform.SetParent(null);
            }

            //Mark this GameObject as not to be destroyed
            DontDestroyOnLoad(gameObject);
        }

    }//end CheckForPersistence()


}//end Singleton

```

# 🧩 Understanding This Singleton

Let’s break down what this is doing.

## ✅ Generic Singleton (Why `<T>`?)

This line is the core of the pattern:

`public class Singleton<T> : MonoBehaviour where T : MonoBehaviour`

This means:

-   `Singleton<T>` is a **generic base class**
    
-   `T` must be a Unity component (`MonoBehaviour`)
    
-   Any class that inherits from it becomes a singleton automatically
    

Example:

`public class GameManager : Singleton<GameManager>`

## ✅ The Instance Property

`public static T Instance { get; private set; }`

This creates a **global access point**:

`GameManager.Instance`

And because it has a `private set`, nothing else can overwrite it.

## ✅ Why Awake()?

Unity calls `Awake()` before `Start()`.

That makes it perfect for singleton initialization because:

-   We want our manager to exist before anything else runs
    
-   We want to destroy duplicates immediately
    

## ✅ CheckForSingleton()

This is the rule enforcement:

`if (Instance == null) {     Instance = this as T; } else {     Destroy(gameObject); }`

Meaning:

-   The first one becomes the singleton
    
-   Any additional ones destroy themselves
    

## ✅ Persistence Across Scenes

This part:

`DontDestroyOnLoad(gameObject);`

Makes the singleton survive when you load new scenes.

> \[!NOTE\]
> 
> ## Why do we detach from the parent?
> 
> If your singleton object is nested inside another object (like a UI Canvas),  
> Unity may destroy it when the parent is destroyed.
> 
> Detaching ensures the singleton is at the root of the scene hierarchy and safe.

# 🧪 Step 3 — Testing the Singleton

You should always test patterns immediately.

## Create a Quick Test Script

1.  Create a new script named:
    

**SingletonTester**

1.  Paste this:
    

`using UnityEngine;  public class SingletonTester : Singleton<SingletonTester> {     protected override void Awake()     {         base.Awake();         Debug.Log("SingletonTester Awake() ran!");     } }`

## Add it to a GameObject

1.  Create an empty GameObject in your scene
    
2.  Name it:
    

**SingletonTester**

1.  Add the `SingletonTester` script to it
    

## Duplicate the Object (On Purpose)

1.  Duplicate the object (Ctrl+D)
    
2.  Press Play
    

You should see:

-   One object survives
    
-   The duplicate destroys itself
    
-   The console logs confirm which one became the Instance
    

# 🎉 New Achievement: Singleton Base Class Created!

You now have a reusable generic Singleton base class that can be used for any manager system in your game.

This is the foundation for our next lesson, where we build the **GameManager** and finally begin controlling our game states properly.

## 🛡️ Checkpoint

Key takeaways from this lesson:

-   A **Singleton** ensures only one instance of a class exists.
    
-   A Singleton provides a global access point using `ClassName.Instance`.
    
-   A **generic singleton** (`Singleton<T>`) lets you reuse the same pattern for multiple managers.
    
-   `Awake()` is used because it runs early and is ideal for initialization.
    
-   `_isPersistent` allows you to control whether the singleton survives scene loads.
    
-   This pattern is the setup for our next tutorial: **GameManager + State Handling**.
    

`If you want, I can immediately write **Tutorial 2: Building the GameManager (Replace + Push/Pop States)** so it connects directly to this lesson without any gaps.`
