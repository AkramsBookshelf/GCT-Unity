# 📜 Singleton Pattern
> By: Akram Taghavi-Burris | © 2026

When discussing the **singleton** design pattern, I can’t help but think of the 1980’s cult classic film and lesser-known TV series, _Highlander_. I know that I am dating myself here, but it’s the best analogy I have.

The story of Highlander is one of immortal beings who exist among humans, each with their own unique powers and abilities. Central to the narrative is the belief that **“There can be only one.”** This implies that these immortals must engage in fierce battles, culminating in the beheading of their rivals until only one remains standing. The last immortal standing is said to receive **The Prize**, a mysterious reward that grants them ultimate power.

In a similar vein, **Singletons** represent the “immortal” objects in your game. Just as the Highlanders cannot coexist peacefully, your Singleton class ensures that only one instance of the class survives throughout the entire game. Any additional instances created are destroyed, just as an immortal would face beheading if they crossed paths with another.

## Using Singletons

In game development a common use case for a singleton is in the creation of a single instance of a manager or controller class (e.g. AudioManager, GameManager, or PlayerControler) where creating multiple instances could lead to conflicts or inefficiencies. By implementing the Singleton pattern, you can ensure that no matter how many times the object is requested or instantiated, only one instance will exist. Use of Singleton Pattern in Unity

Example of how the Singleton pattern can be used inside a class:
