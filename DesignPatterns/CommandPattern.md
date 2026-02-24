# 📜 Command Pattern
> By: Akram Taghavi-Burris | © 2026

When designing flexible, scalable systems, we often encounter situations where **the same action can have different effects** depending on context. The **Command Pattern** is a design pattern that formalizes this by **encapsulating an action and its execution into a command object**, which can be assigned dynamically to an invoker.

## **What Is the Command Pattern?**
The Command Pattern separates **who calls an action** from **how the action is executed**:
-   **Invoker:** The generic _template_ for execution; it controls **when** to perform a command.
-   **Command:** A self-contained object that contains **instructions** for **how** to execute a specific action.
-   **Receiver:** The object or system that actually performs the work.

The key idea is **substitution**: the invoker always performs the same “trigger” action, but the command it executes can be swapped dynamically at runtime.

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
---

## Why this is powerful in Unity:
The Command Pattern takes a specific action (like "Cook a Burger") and puts it into a container (the Order Slip). Instead of just calling a function immediately, you are turning that "call" into an object that can be moved, stored, or delayed.

When you think of it as a Workflow, the Command Pattern becomes the Standard Operating Procedure (SOP):
-   **Decoupling:** The "Front of House" (Clerk) doesn't need to be wired directly to the "Back of House" (Cook). They both just need to know how to handle the "Paperwork" (The Interface).
-   **Consistency:** Every order follows the same 4-step process, whether it's a burger, a soda, or a complex custom meal.
-   **Automation:** Because the `RestaurantManager` handles the "packaging," you can change who is "On Duty" (the variables) at any time, and the system won't break.
    
This way, your code isn't just a bunch of people talking; it’s a predictable pipeline where data (the Order) flows through specific checkpoints.


---

## Strict Architecture vs Practical Coding
Typically, the Command Pattern defines that every unique action is a class. But you can be clever about how you build those classes so you aren't writing 100 files for 100 buttons.



