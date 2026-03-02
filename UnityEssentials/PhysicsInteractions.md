## 📜 Understanding Physics: Colliders & Rigidbodies

> By: Akram Taghavi-Burris | © 2026

In Unity, physics behavior is controlled primarily by two components: **Colliders** and **Rigidbodies**. The way these two components are combined determines how an object behaves during a collision.

###  What Is a Collider?

A **Collider** defines the physical shape of an object. It is an invisible boundary that tells Unity where the object “exists” in physical space. When two colliders touch, Unity detects a collision.

It is important to note that a collider does not make an object move; instead, it allows objects to: 
- block other objects
- detect collision

Without a collider, objects would pass straight through each other. There would be no physical interaction at all.

#### Trigger Colliders
A collider can also be marked as a Trigger. Trigger colliders do not block objects, but they can detect when other colliders enter, exit, or stay inside them. Triggers are useful for zones, pickups, or interactive areas where you want to detect overlap without affecting physics movement.

# 
### What Is a Rigidbody?
A **Rigidbody** is what brings an object into the physics simulation.
When you add a Rigidbody, you’re telling Unity, _this object should respond to forces._

An object with a Rigidbody can:
-   Fall due to gravity
-   React to forces by other objects (e.g., push, bounce, roll)     
-   Transfer force to other objects

Without a Rigidbody, the object remains part of the environment. With a Rigidbody, it becomes part of the simulation.

--- 

## How Objects Behave
In a game world, not every object should behave the same way.

Some objects are part of the environment. They should block movement but never move themselves, like walls, trees, or buildings. Other objects need to move in a controlled way. A moving platform, for example, should follow a scripted path and not fall due to gravity. Moreover, there may be objects that need to fully react to physics. A ball should roll when pushed. A crate should fall if dropped.

In Unity, these three behaviors are known as **static**, **kinematic**, and **dynamic** objects. The way we combine Colliders and Rigidbodies determines which category an object belongs to.

- **Static Objects** are part of the game world.
  - A static object has a Collider, but no Rigidbody.
 
> [!NOTE]
> The Static checkbox in the Inspector tells Unity that an object will never move during gameplay. It is used for engine optimizations like lighting, navigation, and occlusion culling.
>  While static objects should usually have this box checked, it is not what makes them physically static;  the **absence of a Rigidbody does**.
>
 
- **Kinematic Objects** are objects with script-controlled movement
   - A kinematic object has a Collider
   - A kinematic object has a Rigidbody with **Is Kinematic** enabled
   - A kinematic object is not affected by gravity and will not respond to physical forces. You cannot push it, and it will not fall.

- **Dynamic Objects** are objects that simulate physics.
   - A dynamic object has a collider
   - A dynamic object has a Rigidbody
   - A dynamic object reacts to gravity and collisions

--- 
## Physics Movement 

By default, if two objects with Rigidbodies are placed above each other in the scene, gravity immediately takes effect, and they will fall naturally. However, Unity’s physics system does much more than just apply **gravity**. Once a Rigidbody is added, Unity automatically simulates collisions, momentum, forces, and mass-based interactions.

- **Mass** determines how resistant an object is to movement and how strongly it affects other objects during collisions.
    - A light object is easier to push and will be moved more by impacts.
    - A heavy object requires more force to move and will push lighter objects aside more easily.

- **Collisions:** When two Rigidbody objects collide, Unity calculates how they should bounce, slide, or stop based on their mass, velocity, and drag. 
   - A heavy ball hitting a lighter cube will push the cube more than the other way around.

- **Momentum:** Moving objects carry momentum, which affects how they interact during collisions. A fast-moving object will have a stronger impact than a slow one.

- **Forces and Drag:** Objects can be affected by forces like wind, explosions, or friction. Drag (air resistance) slows them down naturally over time.

- **Continuous Interaction:** Physics objects automatically respond to each other and the environment. For instance, stacking boxes will cause them to settle realistically, topple over, or slide if one is pushed.

Once a Rigidbody is added, Unity simulates all of these physics behaviors automatically, based on the settings applied in the Rigidbody. This allows for realistic interactions between objects without requiring you to calculate collisions or momentum manually.

#
### Rigidbody Component 
The Rigidbody is what tells Unity that an object should participate in the physics simulation. The settings inside this component control exactly how that object behaves.

#### Movement & Weight
- **Mass** controls how heavy the object is and how strongly it reacts to forces and collisions.
- **Linear Damping** controls how quickly the object slows down when moving (like air resistance).
- **Angular Damping** controls how quickly the object stops rotating.

These three directly influence how the object feels physically.

#### Gravity & Simulation Mode
- **Use Gravity** if enabled, the object falls automatically.
- **Is Kinematic** if enabled, the object ignores physics forces and must be moved by script or animation.

#### Stability & Interpolation
- **Interpolate** smooths visual movement between physics updates.  
   - Useful for player characters or cameras to reduce jitter.
- **Collision Detection** controls how Unity detects collisions:
    - Discrete (default)
    - Continuous (better for fast-moving objects)
- **Constraints** lock movement or rotation on specific axes.
    - Freeze Position (X, Y, Z)
    - Freeze Rotation (X, Y, Z)

#### Advanced Settings
- **Automatic Center of Mass** allows Unity to calculate balance automatically.
- **Automatic Tensor** controls rotational inertia (advanced physics behavior).

#### Layer Overrides
Include Layers / Exclude Layers determines what this Rigidbody is allowed to collide with.

#

### Physics Layer Matrix
As your projects grow, you may not want every object to collide with every other object.

For example:
-   The player’s trigger zones shouldn’t collide with the ground.
-   Enemy bullets shouldn’t collide with other enemies.
-   Decorative objects may not need to interact with physics at all.

The Physics Layer Matrix is a global collision filter that determines which layers are allowed to collide with each other, and can be accessed from **Edit > Project Settings > Physcis**

---

# ⚒️ Tutorial: Physics Interactions

<details>
<summary><strong><em>Tutorial Details</em></strong></summary>

|📝 Topic          | 🕑 Estimated Time | 🧰 Requirements   |
| :---------------: | :---------------: | :---------------: |
| Colliders & Physics Interaction   | 45 minutes       |   GitHub Desktop, Unity  |
</details>

> [!NOTE]
> Before starting this tutorial, ensure you have :
>  - Completed **[LightingTutorial](Lighting.md)**
>  - That you are on the **Interactions** branch in GitHub Desktop.
>

## Understanding Colliders 
Colliders are **invisible shapes** that define the boundaries of an object for physics interactions in Unity. They allow objects to detect **collisions** and react to forces.

In games, there are two types of colliders: 
-   **Static Colliders**: Objects that **do not move** (e.g., terrain, walls, benches).
-    **Dynamic Colliders**: Objects that **move and react** to physics (e.g., balls, boxes).
      -   Require **Rigidbody** for physical interaction.

> \[!TIP\]  
> Colliders can be primitive shapes (Box, Sphere, Capsule) or Mesh Colliders for complex models. Use primitives whenever possible for performance.
>

#

### Step 1 — Static Colliders
1. In the **Hierarchy**, select any object that does not move to be static, for example
   - Locate the Tree prefab in the **Hierarchy**
   - In the **Inspector** window **check Static** in the top right-hand corner
   - Click on the **Overrides** drop down and choose **Appy to All** to update the prefab
     
>[!NOTE]
> If prompted to apply static to child objects, go ahead and press **Yes**. This ensures that both child and parent objects are static.
>

2. Repeat step one on all static objects


### Step 2 - Dynamic Colliders 
1. In the **Project** window located the **SM_Ball_01** prefab from the **3rd Party/Playground Low Poly** folder
2. Place the ball prefab somewhere in the park scene accessible to your player's amerture.
3. Select the **Ball** in the hierarchy.
4. In the **Inspector**, check that it already has a **Sphere Collider** component.   
    -   If it doesn’t, click **Add Component → Physics → Sphere Collider**
5.  Adjust the **Radius** to match the visual size of the ball.
    
> \[!WARNING\]  
> If an object does not have at least one Collider, it **cannot participate in physics interactions**.
    
        



