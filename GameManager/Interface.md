# What is an Interface?

An **interface** is like a **contract** that a class agrees to follow. It specifies **what methods and properties a class must have**, but **does not provide any implementation**. Think of it as a checklist: any class that signs the contract must include the items on the list, but the way it fulfills them is up to the class itself.

In C#, interface methods are **always public**. This makes sense because the whole point of an interface is to allow other classes to interact with your object in a consistent way, without needing to know the details of its implementation.

---

##⚡ ENCOUNTER: Interactable Objects
Imagine a player exploring a game world full of interactive objects. Some objects can be picked up, others opened, and some consumed. For example:

- A **Box** that can be picked up and thrown.
- A **Chest** that can be opened to reveal treasure.
- A **Door** that can be opened or locked.
- A **Magic Potion** that can be picked up and consumed.

To implment these interactions, the beginner approach would be to have the `Player` class for example, check what it is interacting with and then run the approiate method. For example: 

```csharp
void OnTriggerEnter(Collider other)
{
    swithc(other){
        case Box : 
            other.PickUp();
        case Chest: 
            other.Open();
        case Door: 
            other.Toggle();
        case Potion: 
            other.Consume();
        case Defult:
            break;
    }
```

While this approach works, it requires that we know the **class** types of objects we could potentially interact with and the **specfic method** that they need to call. Furthermore as the number of interactable objects grows we are faced with the following challenges: 

-   The code is **long and hard to maintain**.
    
-   Adding a new object requires **modifying the player code**.
    
-   Each object’s behavior is **scattered across multiple places**.

A more modular solution is implmenting an **IInteractable** interface. 

We can define a contract for all interactable objects:

```csharp
public interface IInteractable
{
    void Interact();
}
```

- The interface declares the Interact method, but does not define its behavior.
- Each object implements the interface in its own way

```csharp
public class Box : IInteractable
{
    public void Interact()
    {
        // Pick up and throw the box
    }
}

public class Chest : IInteractable
{
    public void Interact()
    {
        // Play chest opening animation and reveal treasure
    }
}
```

The player code becomes clean and generic:

```csharp
void OnTriggerEnter(Collider other)
{
    IInteractable interactable = other.GetComponent<IInteractable>();
    if(interactable != null)
    {
        interactable.Interact();
    }
}
```
Now the player can interact with any object that implements IInteractable, without checking its specific type.

---