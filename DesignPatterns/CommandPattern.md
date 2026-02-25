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
    
For example, imagine a player pressing a button to fire a weapon in a game:
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

## Command Instructions
Each weapon 
- 


If, for example, our player has multiple weapons: a pistol, a shotgun, a bow, a sword, or a knife. Each weapon can perform one or more types of actions, such as shooting a bullet, launching a rocket, shooting an arrow, swinging a sword, or dagger. When the command is invoked, it would simply call the weapon to run the appropriate logic. 


Instead of creating a unique command for every weapon-action pair, we can **create commands that represent the type of action**:
-   `FireCommand` → represents a generic _firing_ action.
-   `ShootCommand` → represents a generic _shooting_ action.
-   `SwingCommand` → represents a generic _swinging_ action.

When the player presses the fire button, the invoker creates a command corresponding to the desired action type and passes in a reference to the currently equipped weapon:

```csharp
// Pistol is the currently equipped weapon
ICommand attack = new FireCommand(pistol); 

// Somewhere we trigger the attack command, such as responding to player input
attack.Execute();
```
Simliarly we could create a swing command for the sword.

```csharp
ICommand attack = new SwingCommand(sword);

attack.Execute();
```

This structure allows the invoker to remain **completely generic**:
-   It calls `Execute()` on the command without knowing the details of the weapon.
-   It can dynamically swap commands when the player changes weapons or actions.
-   The system can scale easily — new weapons or actions can be added by creating new receivers or new command types, without modifying the invoker.

```csharp
switch(myWeapon){
    case pistol:
        ICommand attack = new FireCommand(pistol);
        break;
    case sword:
        ICommand attack = new SwingCommand(sword);
        break;
}

// Execution logic is the same for all commands
attack.Execute();

```
# 

### How the Command Works

A command doesn’t implement the action itself; it **only encapsulates the instruction and the target receiver**. The receiver is responsible for the actual logic of the action. This separation lets the invoker remain generic while making the system flexible and modular.
```csharp
// FireCommand delegates the action to the receiver
public class FireCommand : ICommand
{
    private Weapon _weapon; // The receiver

    public FireCommand(Weapon weapon)
    {
        _weapon = weapon;
    }

    public void Execute()
    {
        // The command tells the receiver to perform its action
        _weapon.Fire();
    }
}
```

Each receiver implements its own action logic:
```csharp
public class Pistol : MonoBehaviour
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
        play.LaunchClip();
        Launch(rocket);
    }
}
```
Notice that the command doesn’t need to know the details of how a pistol or rocket launcher fires;  it just tells the weapon to “fire itself.” This is decoupling in action: the invoker (player input) doesn’t care which weapon is equipped, and the command can work with any weapon that supports the action.

This design also enables **substitution**: the same button could execute a FireCommand, a SwingCommand, or any other action command, simply by swapping the command object assigned to the invoker.

---

## Order Up: The Command Pattern

If we recall from our lesson on Interfaces, they define a contract that can be recognized by others. Think of walking into a fast-food joint: you don’t need to know each employee’s name to get lunch. You spot them by their uniform. Because all employees wear the same uniform, you instantly know who has the "contract" to take your order.

In the same vein, we use Interfaces as the "uniform" for the Command Pattern. We create a contract that simply says: "This object can execute a command." 

Imagine that we are back at the fast-food joint and you order a "Cheeseburger." The employee at the counter doesn't need to know how to grill beef or slice pickles; they just write the order on a slip. That employee puts the slip into a queue (a list of jobs to be done). Eventually, the Cook, the only one who actually knows how to use the stove, picks up that slip and follows the specific instructions written on it, like "no onions" or "extra pickles."

#

### The Three Key Players
-   The Command (The Order Slip): This is the piece of paper. It is an object that holds all the specific details (like "no mayo"). It knows what needs to be done, but it doesn't have the tools to do the cooking itself.
-   The Invoker (The Counter Worker): This is the person who takes your slip and puts it in the line. They trigger the process, but they stay clean; they never have to touch the ingredients.
-   The Receiver (The Cook): This is the person with the skills. They receive the slip and run the instructions. They are the only ones who actually "execute" the heavy lifting.


#

### Putting it Together

#### 1. The Interface (The "Slip" Format)
Every order slip needs to follow a standard format so that anyone can read it. This "contract" ensures that no matter what the order is, it will always have an `Execute()` method.

```csharp
public interface ICommand
{
    void Execute();
}
```
# 

#### 2. The Receiver (The Cook)
The **Cook** is the logic specialist. They have the actual tools and knowledge to perform the work, like grilling or prepping ingredients.

```csharp
public class Cook: MonoBehaviour
{
    public void CookCheeseburger()
    {
        Debug.Log("Chef: Grilling patty, adding cheese... Order ready!");
    }
}
```
#
#### 3. The Command (The Specific Order)

This object acts as the "middleman." It wraps the Cook’s specific action into a single package. It stores the reference to the Cook so that it knows who to call when it's time to work.

```csharp
public class BurgerCommand : ICommand
{
    private Cook _cook;

    // The Constructor: We tell the slip which Cook will handle this.
    public BurgerCommand(Cook cook)
    {
        _cook = cook;
    }

    // The Execution: When the slip is processed, it triggers the Cook's logic.
    public void Execute()
    {
        _cook.CookCheeseburger();
    }
}
```
# 

#### 4. The Invoker (The counter clerk)
The **Clerk** doesn't know how to cook and doesn't care what is on the slip. Their only job is to hold the slip (`SetOrder`) and submit it when the time is right (`PlaceOrder`).

```csharp
public class Clerk
{
    private ICommand _orderSlip;

    public void SetOrder(ICommand order)
    {
        _orderSlip = order;
    }

    public void PlaceOrder()
    {
        Debug.Log("Waiter: Handing slip to the kitchen...");
        _orderSlip.Execute();
    }
}
```


#### 5. The Manager (The Matchmaker)
In Unity, the `RestaurantManager` represents the Restaurant's Workflow. It’s the logic that defines how an order moves from a customer's brain to a finished meal. It ensures that the right "On Duty" employees are connected at the right time.

When a customer triggers an order, the system follows these internal steps:

```csharp
public class RestaurantManager : MonoBehaviour
{
    // The system knows who is currently staffed.
    public Cook cookOnDuty; 
    private Clerk _clerkOnDuty = new Clerk();

    void Update()
    {
        // A customer initiates a request (Pressing 'B').
        if (Input.GetKeyDown(KeyCode.B)) 
        {
            // The system generates a formal "Order Slip." 
            // It automatically links the order to the Cook currently at the station.
            ICommand newOrder = new BurgerCommand(cookOnDuty);

            // The system routes that slip through the Clerk's station.
            _clerkOnDuty.SetOrder(newOrder);
            
            // The system triggers the Clerk to "Process" the order, 
            // which eventually reaches the Cook for execution.
            _clerkOnDuty.PlaceOrder();
        }
    }
}
```
#

### The Power of the Command Pattern
The Command Pattern takes a specific action (like "Cook a Burger") and puts it into a container (the Order Slip). Instead of just calling a function immediately, you are turning that "call" into an object that can be moved, stored, or delayed.

When you think of it as a Workflow, the Command Pattern becomes the Standard Operating Procedure (SOP):
-   **Decoupling:** The "Front of House" (Clerk) doesn't need to be wired directly to the "Back of House" (Cook). They both just need to know how to handle the "Paperwork" (The Interface).
-   **Consistency:** Every order follows the same 4-step process, whether it's a burger, a soda, or a complex custom meal.
-   **Automation:** Because the `RestaurantManager` handles the "packaging," you can change who is "On Duty" (the variables) at any time, and the system won't break.
    
This way, your code isn't just a bunch of people talking; it’s a predictable pipeline where data (the Order) flows through specific checkpoints.

---

## Strict Architecture vs Practical Coding
In a strict implementation of the Command Pattern, every unique action is its own dedicated class. If your menu has 50 items, you create 50 C# scripts.
-   The Workflow: You create `BurgerCommand.cs`, `PizzaCommand.cs`, `SaladCommand.cs`, etc.
-   The Benefit: Each file is "Isolated." If you need the `BurgerCommand` to play a specific "Sizzling" sound effect that no other food uses, you have a dedicated place to put that logic.
-   The Cost: High "Boilerplate." You end up writing the same constructor and class structure over and over again, which can lead to a cluttered project folder.

```csharp
// Strict: Every meal needs its own class file
public class BurgerCommand : ICommand 
{
    private Cook _cook;
    public BurgerCommand(Cook cook) => _cook = cook;
    public void Execute() => _cook.MakeBurger();
}

public class PizzaCommand : ICommand 
{
    private Cook _cook;
    public PizzaCommand(Cook cook) => _cook = cook;
    public void Execute() => _cook.MakePizza();
}
```
# 

### Practical Application
Alternatively, we can be clever by creating a Parameterized Command. Instead of making a new class for every meal, you make one "Universal" class that carries the specific instruction (the Action) as a variable.
 - The Workflow: You create one file called `MealRequestCommand.cs`.
 - The Benefit: You only write the "Command Logic" once. To create new menu items, you simply pass a different method (recipe) into the constructor.
 - The Result: You can handle 100 meal requests using only one command class.

```csharp
// One command executes the entire menu
public class MealRequestCommand : ICommand
{
    private Action _cookMeal; 

    public MealRequestCommand(Action cookMeal)
    {
        _cookMeal = cookMeal;
    }

    public void Execute()
    {
        // The Command just "sends" the instruction to cook
        _cookMeal?.Invoke(); 
    }
}

```

The `RestaurantManager` would now define a new command for each menu item. 
```csharp

public class RestaurantManager : MonoBehaviour
{
    // The system knows who is currently staffed.
    public Cook cookOnDuty; 
    private Clerk _clerkOnDuty = new Clerk();

    void Update()
    {
        // A customer initiates a request (Pressing 'B').
        if (Input.GetKeyDown(KeyCode.B)) 
        {
            // The system generates a formal "Order Slip." 
            // It automatically links the order to the Cook currently at the station.
            ICommand newOrder = nnew MealRequestCommand(cookOnDuty.CookCheeseburger);

            // The system routes that slip through the Clerk's station.
            _clerkOnDuty.SetOrder(newOrder);
            
            // The system triggers the Clerk to "Process" the order, 
            // which eventually reaches the Cook for execution.
            _clerkOnDuty.PlaceOrder();
        }
    }
}
```
The **Cook** then runs the invoked action

```csharp
public class Cook: MonoBehaviour
{
    public void CookCheeseburger()
    {
        Debug.Log("Chef: Grilling patty, adding cheese... Order ready!");
    }

    public void CookHotdog(){
        Debug.Log("Chef: Grilling hot dog, adding chili... Order ready!") 

}

}
```

We've eleimanated the need for 100s of meal commands, instead the instructions for each meal is on the  Cook. However, this could still cause the issues of moving 100s of classes into 1000 of lines of code in the cook. If we only have say 10 meal options, this mehtod if fine, but if we ahve more, we could break this out furhter, by seprating meal types into their own clases. 

```csharp
public class GrillStation {
    public void CookBurger() => Debug.Log("Grilling...");
    public void CookSteak() => Debug.Log("Searing...");
}

public class SaladStation {
    public void PrepCaesar() => Debug.Log("Chopping Romaine...");
    public void PrepGreek() => Debug.Log("Adding Feta...");
}
```

The Cook would need to create a new instance of these stations: 

```csharp
public class Cook : MonoBehaviour {
    public GrillStation grill = new GrillStation();
    public SaladStation prep = new SaladStation();
    
    // The Cook just routes the request to the right station
}
``` 

Our resturant manager now just needs to pass the action on to the cook and speficic station 

```csharp

 if (Input.GetKeyDown(KeyCode.B)) 
        {
            // The system generates a formal "Order Slip." 
            // It automatically links the order to the Cook currently at the station.
            ICommand newOrder = nnew MealRequestCommand(cookOnDuty.gril.CookCheeseburger);

            // The system routes that slip through the Clerk's station.
            _clerkOnDuty.SetOrder(newOrder);
            
            // The system triggers the Clerk to "Process" the order, 
            // which eventually reaches the Cook for execution.
            _clerkOnDuty.PlaceOrder();
        }
    }

```



