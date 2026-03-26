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

## Naming as Structure 
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
## Namespaces as Invisible Organization

Folders help us organize files, but they do not fully solve the problem of organization within the code itself. As projects grow, it becomes increasingly common for different systems to define classes with similar or identical names. A `Controller` in one system may have nothing to do with a `Controller` in another.

Without additional structure, these naming collisions lead to confusion, ambiguity, and eventually errors.

**Namespaces** provide a solution by acting as a form of “invisible folder structure” within the code. A **namespace** is a grouping of related classes, interfaces, and other types, used to organize code and prevent naming conflicts. It allows for the code to be categorized and managed more effectively.

### Benefits of Namespaces 
- Organize Code: Group related classes and functions together logically.
- Avoid Naming Conflicts: Prevent naming collisions between classes, especially when integrating third-party libraries or assets.
- Improve Readability: Make it clear where a class or function belongs within the project structure.
- Modularity: Namespaces offer the ability to maintain a modular codebase that is easier to manage and update.

In C#, classes can be accessed within a namespace by specifying the namespace when declaring or using the class. This is typically done with the `using` keyword at the top of a script, which simplifies referencing classes without needing to use their fully qualified names. For example, to use Unity's `MonoBehaviour` class, you include using `UnityEngine`; at the top of your script.

> [!NOTE]
> **Groups of Code** 
> **Namespaces** should be thought of as groups of related code within a library. A library, on the other hand, is a collection of code that may include multiple namespaces. For example, **.NET framework** is a collection of libraries that
includes many namespaces, like `System.Collections`, which groups related types for handling collections of objects.
> 

### Naming Convention for Namespaces
A well-structured namespace typically reflects the same hierarchy as the project folders. For example, a script located within an inventory feature’s UI layer might belong to a namespace such as:'ProjectName.Inventory.UI'

This approach creates a direct relationship between where a file exists and how it is referenced in code. It also ensures that systems remain logically grouped, even when viewed outside the context of the file browser.

More importantly, namespaces reinforce modular thinking. A system is not just defined by where its files are stored, but by how it is conceptually separated from the rest of the project.

General Guidelines

-   **Hierarchical Structure**: Use a hierarchical structure to group related classes and functionality. Each level in the hierarchy represents a more specific category within a broader context.
    
-   **Company and Project name**: Start with your company or organization name, followed by project name. Use **General** for grouping generic classes that will be used by various projects.
    
-   **Descriptive and Clear**: Choose names that clearly describe the purpose or functionality of the classes within the namespace. This makes it easier for developers to understand the role of the code.
    
-   **Pascal Case**: Use PascalCase (where each word starts with a capital letter) for namespace names. This is consistent with C# naming conventions and improves readability.

A namespace cencapsulates the class.

Putting using directives inside or outside the namespace does not change how the code is compiled or how much memory it uses. However, it is most common to place using directives outside of the namespace. 









