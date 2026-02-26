# 📜 Command Pattern
> By: Akram Taghavi-Burris | © 2026

When designing flexible systems, we often encounter situations where the same trigger can produce very different outcomes depending on context.

Pressing a button might make: 
- A weapon fire
- A spell cast
- A character jump

The input is the same, but the behavior changes.

Now, you might be thinking:

> “Couldn’t we just use the **Observer pattern**?”

For example, a UI button could broadcast a simple event like:
```csharp
OnButtonPressed?.Invoke();
```
Any system listening to that event could respond. One listener might call `Jump()`. Another might call `FireWeapon()`.

But now imagine a more complex scenario:
-   You want the action to happen later, not immediately.
-   You want to store a history of actions so you can undo them.
-   You want to dynamically swap which action executes without changing the button.
-   You want to queue actions, like a series of moves for an AI character.
  
The Observer pattern can’t do that. It only announces that something happened; the logic of what to do lives entirely with the subscribers. There’s no built-in way to treat the action itself as a first-class object.

This is exactly the problem the **Command Pattern** solves. By turning the action into an object (i.e, a command), you can store it, pass it around, queue it, delay it, or undo it. The button doesn’t just broadcast “pressed.” It executes a packaged instruction, giving you far more control and flexibility over your game’s actions.

> [!TIP]
> Observer tells, Command instructs. Observer works for notifying events. Command works when the action itself needs to be handled, stored, or manipulated.
>

# 

## Understanding the Command Pattern
At its core, the Command Pattern introduces a **middle layer between the object that triggers an action and the object that carries it out**. This middle layer, the **command**, encapsulates both the instructions for what should happen and the receiver that will execute them. You can think of it as three distinct roles:
-   **Invoker / Trigger:** The object that initiates _when_ the action happens, like a button press or player input.
-   **Command / Instruction:** A self-contained object representing _what_ should happen. This object can be stored, queued, delayed, or swapped dynamically.
-   **Receiver / Performer:** The object that knows _how_ to carry out the action when the command is executed.

# 

### Amazon Order
You can think of the Command Pattern like placing an order on **Amazon**. As a customer, you decide you want a product, so you place an order. You don’t need to know how the product is made, packaged, or shipped — you just submit the request.
-   In this analogy, **you are the invoker** — the one who triggers the action by placing the order.
-   **The order itself is the command** — it contains the instructions for what needs to be done and identifies the vendor who should carry it out.
-   **The vendor is the receiver** — they know exactly how to fulfill the order. The vendor opens the package, processes the request, and delivers the product.
  
The key idea is that the **command doesn’t actually perform the work itself**; it merely acts as a messenger. This separation allows Amazon to handle millions of orders efficiently, route them to different vendors, and even swap or queue orders without the customer needing to change anything.

#

### Command Pattern in Game
Similarly, the command pattern can be used in a game. For exmaple we might have an _attack_ command object that tells a weapon to perform an action. Since the action may be different for different weapons, the actual logic for how the action happens is on the weapon, not the command object. This makes the system **flexible, modular, and easily extendable**, just like Amazon can handle different vendors and products using the same ordering system.

Breaking this down further, we imagine a player pressing a button (the command object) to fire a weapon in a game:
1.  **Player presses the fire button** → the **invoker** signals “I want to fire.”
2.  **FireCommand is created** → it knows **which weapon** to act on and **what action** to perform (`Fire()`).
3.  **Invoker calls `Execute()` on the command** → the command triggers the action.
4.  **Weapon receives the call from the command** → it performs its own logic and actually fires the gun.

#
### The Command Interface
The **Command interface** is the foundation of the Command Pattern. It defines the **contract that every command must follow**, typically a single method like `Execute()`. By standardizing commands around this interface:
1.  **The invoker can handle all commands uniformly** — it doesn’t need to know what the command actually does or which receiver it talks to.
2.  **Concrete commands encapsulate instructions and target receivers** — each command object knows _what action to trigger_ and _on which object_, but not _how the action happens_.
3.  **Receivers carry out the actual logic** — the weapon, spell, or menu system executes the behavior, keeping the invoker and commands decoupled.

Every weapon our player has (e.g., a pistol, a shotgun, a bow, a sword, or a knife) will need the excute the attack command in the same way. Thefore the commands all inherit from the `ICommand` Interface.

```csharp
public interface ICommand
{
    void Execute();
}


#

### One Command, Multiple Receivers
In the Command Pattern, a **command object** encapsulates an action type and the receiver it should act on. The command **does not implement the action itself**; it only tells the receiver to execute its logic. This decouples the invoker from the details of the action. For example, we might have a `FireCommand` shooting ranged weapons. 

For example, consider a `FireCommand`:
```csharp
public class FireCommand: ICommand
{
    private Weapon _weapon; // receiver

    public FireCommand(Weapon weapon)
    {
        _weapon = weapon;
    }

    public void Execute()
    {
        _weapon.Fire(); // delegate actual logic to the receiver
    }
}
```
The `FireCommand` doesn't actually perform the logic for firing, just calls the action, which the weapon in turn runs. At any point, we could define **multiple types of weapons**, the `Excute()`, the `FireCommand`.

```csharp

ICommand pistolFire = new FireCommand(pistol);
ICommand rocketFire = new FireCommand(rocketLauncher);

// Preformed on action, such as a key press, and might check which weapon is equipped
pistolFire.Execute();   
rocketFire.Execute();   
```
Once the command is excuted the weapon will run the corresponding action: 
```csharp
public class Pistol: MonoBehaviour
{
    public void Fire()
    {
        play.FireClip();
        Shoot(bullet);
    }
}

public class RocketLauncher : MonoBehaviour
{
    public void Fire()
    {
      //Waits for the cool down to expire before launch
       if(coolDownTime == 0)
      {

        play.LaunchClip();
        Launch(rocket);
      } 
}
```
This demonstrates the **power of reusability:** one command can control many receivers without changing its own code.
# 
### Swapping Commands for Different Actions

The next level of flexibility comes when you want the **invoker to trigger different types of actions**, not just fire. For instance, a melee weapon like a sword or dagger doesn’t fire; it swings. We can create a `SwingCommand`:

```csharp
public class SwingCommand: ICommand
{
    private Weapon _weapon;

    public SwingCommand(Weapon weapon)
    {
        _weapon = weapon;
    }

    public void Execute()
    {
        _weapon.Swing();
    }
}
```
Now the invoker can swap commands depending on the action type:

```csharp

ICommand currentCommand;

// Using a firearm
attackCommand = new FireCommand(pistol);
attackCommand.Execute();  // pistol fires

// Switching to a sword
attackCommand = new SwingCommand(sword);
attackCommand.Execute();  // sword swings

```

