the State Pattern allows an object to change its behavior based on its internal condition (e.g., a Character moving from "Idle" to "Running"), the Strategy Pattern allows an object to change its behavior based on its configuration (e.g., an Item deciding to use "CSV Logic" instead of "JSON Logic").


## Lesson: The "Generic Wall" and the Interface Bridge

In software architecture, specifically within C# and Unity development, we often use **Generic Abstract Classes** to define shared logic for data types. However, generics have a strict "Type Wall"—they require you to know exactly what $T$ is at compile time. 

This lesson explains why we use **Interfaces** as a companion to these classes to gain flexibility and bypass that wall.

---

### 1. The Limitation of Generic Abstract Classes
Imagine you have a base class for all data in your project. You use a generic type $T$ so that different entities can hold different kinds of data (integers, strings, or custom objects).

```csharp
public abstract class DataEntity<T> 
{
    public string EntityName { get; set; }
    public T Data { get; set; }
}
```

**The Problem:** At runtime, `DataEntity<int>` and `DataEntity<string>` are considered **completely different types**. 
If you want to create a list of "all entities" or check if an object is an entity, the code below will fail:

* `List<DataEntity<object>>` cannot hold `DataEntity<int>`.
* `if (item is DataEntity<T>)` requires you to provide a specific $T$.

---

### 2. The Solution: The Non-Generic Interface
To solve this, we create a "Top-Level" interface that contains only the properties or methods that **don't** rely on $T$.



```csharp
// The Bridge: No generics here
public interface IDataEntity 
{
    string EntityName { get; set; }
    void Initialize();
}

// The Implementation: Implements the interface AND uses generics
public abstract class DataEntity<T> : IDataEntity 
{
    public string EntityName { get; set; }
    public T Data { get; set; } // Specific to the implementation

    public abstract void Initialize();
}
```

---

### 3. Why This is Useful (The "Use Cases")

#### A. Heterogeneous Collections
You can now store different types of entities in a single collection. This is vital for systems like Inventory Managers or Save Systems.
```csharp
// This is now possible!
List<IDataEntity> allEntities = new List<IDataEntity>();
allEntities.Add(new PlayerStats : DataEntity<int>());
allEntities.Add(new DialogueNode : DataEntity<string>());

foreach (var entity in allEntities) {
    Debug.Log(entity.EntityName); // Accessing shared data without knowing T
}
```

#### B. The "Is" Check (Type Identification)
When an object enters a system (like a trigger volume or a UI slot), you often just need to know if it's "one of our entities" regardless of what data it carries.
```csharp
public void OnObjectDetected(object obj) 
{
    // We check against the interface, not the generic class
    if (obj is IDataEntity entity) 
    {
        entity.Initialize(); 
    }
}
```

#### C. Decoupling Logic
By using the interface, your "Manager" classes don't need to be generic themselves. A `SaveManager` doesn't need to know if it's saving a `DataEntity<float>` or a `DataEntity<EnemyData>`; it just treats them all as `IDataEntity`.

---

### Summary Table

| Feature | Generic Abstract Class | Non-Generic Interface |
| :--- | :--- | :--- |
| **Purpose** | Defines *how* things work (Implementation). | Defines *what* things can do (Contract). |
| **Type Rigidity** | High (Must know $T$). | Low (Type-agnostic). |
| **Storage** | Can only store same $T$ types together. | Can store any $T$ implementation together. |
| **Logic** | Great for shared code/methods. | Great for identification and "broad" logic. |

> **Key Takeaway:** Use the **Abstract Class** to save yourself from writing the same code twice, but use the **Interface** to allow your systems to talk to those classes without caring about the specific data types they hold.
