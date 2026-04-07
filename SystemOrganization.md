# Moving Beyond Assets: Designing for Systems

Up to this point, we’ve focused on organizing projects by **asset type**, keeping models, scripts, audio, and scenes in their own folders. This is a great starting point, especially for smaller projects or when you're still learning how everything fits together.

However, as projects grow, something starts to break down.

You’re no longer just managing _files_—you’re managing **systems**.

A player system touches scripts, UI, animations, and data. An inventory system might involve prefabs, logic, sound effects, and menus. When these are scattered across asset folders, understanding _how something works_ becomes much harder.

This is where we shift from thinking in terms of **assets** to thinking in terms of **features**.

---

## Structuring Around Responsibility

A useful way to think about large projects is in terms of responsibility. Not every piece of code or content serves the same role, and treating everything as equal leads to tightly coupled systems and unclear boundaries.

To address this, we divide the project into three conceptual layers.

### 1. Core - The Foundation
At the foundation is **_Core**, which contains the fundamental patterns and utilities that the rest of the project depends on. These are not features in themselves, but rather the building blocks from which features are constructed. A state machine base class, a singleton pattern, or shared math utilities all belong here. They define _how_ systems behave at a structural level, independent of any specific gameplay feature.

#

### 2. Shared - The Bridge
Above this sits **_Shared**, which acts as a bridge between otherwise independent systems. As projects grow, direct dependencies between features can quickly become problematic. If one system must know too much about another, changes in one area ripple unpredictably through the project. Shared elements, such as event channels, interfaces, or common data definitions, enable systems to communicate without becoming tightly coupled. In this sense, _Shared does not implement gameplay; it enables systems to coordinate.

#

### 3. Features - The Game 
Finally, we arrive at **Features**, which represent the actual content of the game. Each feature encapsulates a distinct piece of functionality: a player controller, an inventory system, an ability system, and so on. Unlike the asset-based approach, where scripts and prefabs are separated by type, a feature-based approach groups everything needed for that system into a single location. This allows a developer to open one folder and immediately understand how that system is constructed.

Over time, this separation creates a project that is easier to reason about. Core systems remain stable and reusable, shared components manage communication, and features evolve independently.

--- 

## Folder Naming as Structure 
As a game project scales, establishing a consistent naming structure becomes even more important. 

In small projects, inconsistent naming is often an inconvenience. In larger projects, it becomes a _source of confusion and error_. For this reason, we adopt a consistent naming convention across folders and scripts.

To ensure clarity and consistency across the entire project, the following naming standards should be used.

#

### Naming Convention Standards
| Rule                       | Description                                                                                               | ✅ Good Examples                 | ❌ Bad Examples              |
| -------------------------- | --------------------------------------------------------------------------------------------------------- | -------------------------------- | --------------------------------------- |
| **No Spaces**              | File and folder names should never contain spaces to avoid issues with version control and build systems. | `InventorySystem`                | ❌ `my inventory system`                   |
| **PascalCase**             | Use PascalCase (UpperCamelCase) for all folders, scripts, and major assets.                               | `InventoryUI`                    | ❌ `inventory_ui`                          |
| **Descriptive Names**      | Names should clearly describe the purpose of the asset or system. Avoid vague or overly short names.      | `InventoryManager`, `PlayerData` | ❌ `Manager`, `Data`                       |
| **Consistent Terminology** | Use the same word consistently across the project for the same concept.                                   | `Item` (used everywhere)         | ❌ `Item`, `Object`, `Thing` (mixed usage) |

# 
### Special Folder Conventions
| Convention                   | Purpose                                                                                    | Example                               |                     
| ---------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------- |
| **Leading Underscore (`_`)** | Forces foundational folders to the top of the project window for visibility and priority.  | `_Core`, `_Shared`                    |
| **Feature Naming**           | Feature folders should represent complete systems, not asset types.                        | `InventorySystem`, `PlayerController` |
| **Avoid Redundancy**         | Do not repeat unnecessary words if the context is already clear from the folder structure. | `InventorySystem/InventoryScripts`    |

---

## Separating the Game from the Tools

As development progresses, another distinction becomes increasingly important: the difference between the game itself and the tools used to build it.

In Unity, this distinction is formalized through the separation of **Runtime** and **Editor** code.

- **Runtime code** represents everything that will exist in the final game. This code is compiled into the final build and must function independently of the development environment. This would include:
    -  gameplay logic
    -  UI behavior
    -  systems the player directly interacts with.
      
- **Editor code** solely exists to support the developer. These scripts enhance productivity within the Unity Editor but are not intended to be part of the final product. This includes:
    -  custom inspectors
    -  debugging tools
    - Level design utilities  

Because of this, Unity treats these two categories differently. Editor code is excluded from builds, meaning it must be carefully isolated. Placing Editor scripts alongside Runtime code can lead to build failures, as the system cannot include tools that are not meant for the player.

By separating these concerns into distinct folders, we maintain a clear boundary between **what the player experiences** and **what the developer uses to create that experience**.

#

### Folder Convention Standards

```
Assets/
  ├── _Core/ (Abstract classes)
  │    └── Runtime/
  │         └── Patterns/
  │              └── Singleton.cs
  │
  ├── _Shared/ (Concrete Common classes)
  │    └── Channels/
  │         └── StateChangeChannel.asset
  │
  └── Features/
       └── InventorySystem/
            ├── Runtime/
            │    ├── Scripts/
            │    │    └── InventoryController.cs
            │    │
            │    └── UI/
            │         └── InventoryUIBridge.cs
            │
            └── Editor/
                 └── InventoryInspector.cs
```

---

# Namespaces as Invisible Organization

Folders help us organize files, but they do not fully solve the problem of organization within the code itself. As projects grow, it becomes increasingly common for different systems to define classes with similar or identical names. A `Controller` in one system may have nothing to do with a `Controller` in another.

Without additional structure, these naming collisions lead to confusion, ambiguity, and eventually errors.

**Namespaces** solve this problem by acting as an _invisible layer of organization_ within your code.

A namespace is a way of grouping related classes, interfaces, and other types so that they exist within a clearly defined context. While folders organize files visually, namespaces organize how those files are understood and referenced in code.

#

## Why Namespaces Matter

Namespaces do more than prevent errors—they shape how a project is understood.

They allow you to:
-   **Group related systems together** so their purpose is immediately clear
-   **Avoid naming conflicts** between systems or external libraries
-   **Improve readability** by showing where a class belongs
-   **Support modular design**, where systems can evolve independently
  
In larger projects, namespaces become just as important as folder structure.

---

## From Folders to Namespaces
A strong convention is to align your namespaces with your folder structure—but it’s important to understand that they are not the same thing.

A **folder structure** is physical. It organizes files on disk and helps you navigate your project in the editor.

A **namespace** is logical. It organizes how code is grouped, referenced, and understood by the compiler and by other developers.

In other words:

-   Folders answer: _“Where is this file located?”_
-   Namespaces answer: _“What system does this code belong to?”_

Because of this distinction, it is possible for a script to be placed in one folder but belong to a completely different namespace. While this is technically allowed, it often leads to confusion, since the visual structure and the code structure no longer match.

---

## Namespace Naming Conventions
A well-designed namespace communicates both ownership and purpose. A common namespace pattern is made up of : 
`RootIdentifier.Feature.Category` 

#### The Root Identifier 
The **root identifier** in a namespace is typically represented by the **project name**, **company name**, or **developer**. This root acts as a top-level label that groups all code under a single, consistent identity and ownership. It also helps prevent naming conflicts when integrating third-party libraries or sharing code across multiple projects.

-   For **small projects** one off projets the root is usually the **project name**:'CampCraft.Inventory.UI`
-   For **reusable or modular systems** that might be copied across projects, the root can be a **company or developer name**:`CSG.GameManager.States`

Both approaches provide a clear, top-level identifier, establishing **ownership** while maintaining **modularity**.

#### Features and Categories

After the **root identifier**, namespaces usually include:
1.  **Feature** – the system or major component the code belongs to.
2.  **Category** – a more specific subsystem or type within that feature.

For example, consider the following folder structure:
```
\_Core/  
  └── Runtime/  
       └── Patterns/  
            └── Singleton.cs  
```

The namespace for the `Singleton` class would be **`CSG.Core`** 

In this instance: 
-   `CSG` → root identifier
-   `Core` → feature or system (foundational patterns)
-   `Patterns` → **not included** in the namespace because it is a purely organizational folder, not a conceptual category

> **Note:** Only meaningful structural elements are included in the namespace. Folders like `Runtime` are ignored, since they serve an organizational purpose in the editor but do not define the conceptual grouping of the code.

However, if the folder does represent a separate conceptual category, it should be included. 

For example, let's say our InventorySystem has the following folder structure: 
```
 └── Features/
       └── InventorySystem/
            ├── Runtime/
            │    ├── Scripts/
            │    │    └── InventoryController.cs
            │    │       
            │    │
            │    └── UI/
            │         └── InventoryUIBridge.cs
            │           
            │
            └── Editor/
                 └── InventoryInspector.cs

```
Our classes would have the following namespaces: 
- `InventoryController` = `CSG.Inventory`
- `InventoryUIBridge` = `CSG.Inventory.UI`
- `InventoryInspector` = `CSG.Inventory.Editor`

Here, both **UI** and **Editor** represent meaningful conceptual subdivisions, so they are included in the namespace.

#

### Using Directives and Namespaces

A namespace encapsulates the classes, interfaces, and other types it contains, helping to organize and prevent naming conflicts. When working with namespaces, you will often include `using` directives to reference other namespaces.

> **Key Point:** Whether you place a `using` directive **inside** or **outside** the namespace, it does **not** affect how the code is compiled or how much memory it uses.

The common convention is to place `using` directives **outside** the namespace for two reasons:
1.  **Readability** – It makes it immediately clear which external namespaces a file depends on, without needing to inspect the namespace itself.
2.  **Consistency** – Most C# projects and style guides follow this pattern, which helps maintain a uniform codebase when collaborating with others.

**Example:**
```csharp
using UnityEngine;   // Outside the namespace (common convention)  
using System.Collections.Generic;  
  
namespace CSG.Inventory.UI  
{  
    public class InventoryUIBridge : MonoBehaviour  
    {  
        // Class implementation  
    }  
}
```

This keeps namespaces **focused, readable, and aligned with the logical structure** of your code, rather than the exact layout of every folder.

# 

### Namespace Design Guidelines 
The following guidelines should be used when defining namespaces in your projects and design documents:

| Rule                      | Guideline                                                                                        |
| ------------------------- | ------------------------------------------------------------------------------------------------ |
| **Base Structure**        | `RootIdentifier.Feature.Category`                                                                |
| **Root Identifier**       | Use your project name for single projects, or company/developer name for reusable systems        |
| **Hierarchy**             | Organize from **general → specific** (left to right)                                             |
| **Folder Alignment**      | Namespace should reflect meaningful folder structure (not every folder)                          |
| **Feature Isolation**     | Each feature/system should have its own namespace                                                |
| **Clarity & Consistency** | Use clear, descriptive names that match your project’s terminology                               |
| **Case Style**            | Use **PascalCase** for all namespace segments                                                    |
| **Using Directives**      | Place `using` statements **outside** the namespace (common convention; no impact on performance) |

#### Example
| Scenario         | Namespace              |
| ---------------- | ---------------------- |
| Core systems     | `CSG.Core`             |
| Shared systems   | `CSG.General`          |
| Feature root     | `CSG.Inventory`        |
| Feature UI layer | `CSG.Inventory.UI`     |
| Editor tools     | `CSG.Inventory.Editor` |

### Design Rules

-   Do not place unrelated systems in the same namespace
-   Avoid vague names like `Utils` unless they are truly broad and reusable
-   Do not mirror every folder—only include **conceptually meaningful layers**
-   Do not mix multiple features within a single namespace

--- 

# A Cohesive Structure

When these ideas are combined, the project begins to take on a more cohesive form. Foundational systems reside in \_Core, communication pathways exist in \_Shared, and each feature encapsulates its own logic, assets, and tools. Within each feature, Runtime and Editor folders further distinguish between player-facing systems and development utilities.

The result is not simply a cleaner project, but a more understandable one.

A developer navigating the project can move from high-level concepts—such as identifying a feature—to low-level implementation details without losing context. Systems become easier to modify, extend, and debug because their boundaries are clearly defined.

This structure also prepares the project for more advanced workflows. In particular, it aligns closely with the use of **assemblies**, which allow different parts of the codebase to be compiled and managed independently. Without a clear separation of systems, introducing assemblies can be difficult and error-prone. With this structure in place, however, the transition becomes much more natural.

In this way, folder organization is not just about cleanliness; it is about laying the groundwork for scalability.


