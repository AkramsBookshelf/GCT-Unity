# Designing a Scalable Data Management System for Games

## 1\. Introduction: The Inventory Problem

In game development, one of the first systems most designers encounter is the **inventory system**. At its simplest, an inventory is a structured collection of **items** that a player can obtain, store, and use during gameplay.

Each item might include a range of properties such as:

-   A unique identifier
-   A display name
-   A description
-   Gameplay attributes (damage, healing, defense, value, etc.)
-   Visual references (icons, models, effects)
-   Rules (stack size, rarity, usability conditions)

While a single item is easy to define, an inventory system quickly becomes a **data-heavy system**. Modern games often include dozens, hundreds, or even thousands of items. As a result, the challenge is no longer just defining items, but managing them efficiently, consistently, and at scale.

This leads us to a key design question:

> How do we structure game data so that it is easy to create, edit, load, and save—without becoming unmanageable as the project grows?

## 2\. Scriptable Objects as a Data Foundation

In Unity-based development, a common solution for representing structured data is the **Scriptable Object** system.

Scriptable Objects provide several advantages:

-   They exist as persistent assets inside the Unity project
-   They allow designers to edit values in the Inspector
-   They decouple data from scene objects
-   They can be reused across multiple systems without duplication

For an inventory system, this means each item can be represented as a standalone asset:

> Sword.asset  
> HealthPotion.asset  
> IronShield.asset

At first, this approach seems ideal. However, as the number of items increases, a new problem emerges: **scalability of creation and maintenance**.

Manually creating and updating large numbers of Scriptable Object assets becomes inefficient and error-prone. Additionally, designers often prefer working in external tools such as spreadsheets for bulk data editing.

This motivates the need for a more scalable data pipeline.

## 3\. External Data and the Need for Structured Import

To improve scalability, game data is often stored externally using formats such as **CSV (Comma-Separated Values)**.

A CSV file allows data to be represented in a tabular format:
```csv
ID,Name,Damage,Rarity,Value  
001,Sword,10,Common,25  
002,Axe,15,Uncommon,40  
003,Bow,8,Rare,60
```

This approach provides several advantages:

-   Data can be edited in tools like Excel or Google Sheets
-   Large datasets can be generated quickly
-   Designers can work outside of Unity
-   Data can be version controlled like code

However, this introduces a new challenge:

> Unity does not natively understand CSV data as game objects.

Therefore, we need a system capable of **importing external data into Unity assets**, and just as importantly, **exporting Unity data back into external files** when changes are made.

## 4\. The Bidirectional Data Pipeline

Once external data is introduced, the system must support two complementary workflows:
#

### Import Pipeline (External → Unity)

-   Read CSV file from disk
-   Parse raw text into structured data
-   Convert rows into Scriptable Object instances
-   Register or update assets in the project

#

### Export Pipeline (Unity → External)

-   Read Scriptable Object data from memory or assets
-   Convert structured data into a flat file format
-   Write updated CSV or JSON files back to disk

This creates a **bidirectional synchronization system** between Unity and external data sources.

At this stage, the system is no longer just an inventory system—it is a **general-purpose data management pipeline**.

## 5\. Scaling the Problem: Why a Generic System Is Required

As soon as we introduce external data pipelines, it becomes clear that inventory items are only one type of data we may need to manage.

Other systems may include:

-   Player save data (JSON)
-   Quest systems (CSV or JSON)
-   Dialogue systems
-   Character stats
-   World state data

Each of these systems may use different file formats and structures, but they all share the same core operations:

-   Load data
-   Parse data
-   Convert data
-   Save data

Without a shared architecture, each system would require its own custom loader and saver. This leads to:

-   Repeated code across systems
-   Inconsistent file handling logic
-   Difficult maintenance and debugging
-   Limited extensibility

To solve this, we apply the **DRY principle (Don’t Repeat Yourself)** and design a **generic data management architecture**.

### 6\. Defining a Common Data Contract

To allow different data types to be processed uniformly, we define a shared interface 'IDataEntity.`

#

### [IDataEntity](DataEntity.md#idataentity-interface)

This interface establishes a **contract** that all data objects must follow. Regardless of whether the object represents an item, quest, or character stat, it must expose a consistent structure.

This allows the data system to treat all entities in a uniform way, without needing to know their specific implementation details.

This is a critical architectural decision: it decouples the **data definition** from the **data processing pipeline**.

## 7\. Base Implementation: DataEntityBase<T>

To support Unity integration, we introduce an abstract base class:
#
### [DataEntityBase<T>](DataEntity.md#dataentitybase-class)

This class extends ScriptableObject and provides shared functionality such as:

-   Identity management (unique IDs)
-   Naming synchronization between assets and data
-   Lifecycle hooks for loading and saving
-   Common serialization behavior

By centralizing this logic, we ensure that every data type behaves consistently within the Unity environment.

> [!NOTE]
> Using abstract generics makes use of the **Template Pattern**
> 

## 8\. Central Coordination: Database Manager

To manage the overall flow of data, we introduce a central controller:

### [DatabaseManager<T>](DataManagement.md#databasemanager-class)

This class acts as the **orchestrator of the entire data pipeline**. It is responsible for:

-   Loading data into memory
-   Maintaining runtime caches
-   Coordinating import and export operations
-   Ensuring data consistency across systems

Importantly, the DatabaseManager does not need to know the details of the data itself. It operates generically, relying on the shared interface and abstraction layers.

This reflects a key architectural principle:

> [!NOTE]
> High-level systems should not depend on low-level implementation details.
> 

### 9\. Separating Responsibilities: Loader and Saver

To maintain clean separation of concerns, the system divides responsibilities into two components:

-   **[DataLoader<T>](DataManagement.md#dataloader-class)** → Responsible for importing external data into Unity
-   **[DataSaver<T>](DataManagement.md#datasaver-class)** → Responsible for exporting Unity data back to external storage

This separation ensures that reading and writing operations remain independent, making the system easier to extend and debug.

Together, they form the core of the data pipeline.

### 10\. Extensibility Through the Factory Pattern

At this point, the system must support multiple file formats. While CSV is useful for structured tabular data, other formats, such as JSON, are better suited for hierarchical or nested data structures.

To avoid hardcoding format-specific logic, we introduce the **Factory Pattern**.

> [!NOTE]
> A **factory** centralizes object creation logic and returns the appropriate implementation based on a given configuration.
> 

In this system, the **SerializationFactory** is responsible for selecting the correct serialization strategy at runtime.

For example:

-   CSV → CSVStrategy
-   JSON → JSONStrategy

This design ensures that the system is:

-   Open for extension
-   Closed for modification

New formats can be added without altering existing system logic.

### 11\. The Strategy Pattern: Flexible Serialization Behavior

To support interchangeable serialization formats, we use the **Strategy Pattern**.

The core interface:

#
### [ISerializationStrategy<T>](SerializationStrategy.md#iserializationstrategy-interface)

defines a consistent contract for:

-   Serializing data into storage format
-   Deserializing data back into objects

Concrete implementations include:

-   CSVStrategy<T> → flat row-based structure
-   JSONStrategy<T> → hierarchical structured data

A shared base class, **[SerializationStrategyBase<T>](SerializationStrategy.md#serializationstrategybase-class)**, reduces duplication and enforces consistency across implementations.

This design allows the system to dynamically switch formats without modifying the data pipeline itself.

### 12\. File Handling and Data Utilities

At the lowest level of the architecture, we isolate platform-specific and format-specific logic into utility classes.

-   **[FileUtility](fileUtility.md#fileutility-class)** handles:
    -   Cross-platform file paths
    -   Directory validation
    -   Safe read/write operations
-   **[CSVUtility](fileUtility.md#csvutility-class)** handles:
    -   Parsing CSV formatting rules
    -   Escaping special characters
    -   Writing structured rows efficiently

By isolating these concerns, the system avoids scattering low-level logic across multiple classes.

### 13\. System Overview

When combined, the full architecture can be understood as a layered pipeline:

1.  **Scriptable Object Layer**  
    Defines game data assets
2.  **Data Contract Layer (IDataEntity)**  
    Standardizes structure
3.  **Management Layer (DatabaseManager)**  
    Coordinates system behavior
4.  **Pipeline Layer (Loader / Saver)**  
    Handles import/export flow
5.  **Serialization Layer (Factory + Strategy)**  
    Converts between formats
6.  **Utility Layer (File + CSV utilities)**  
    Handles low-level operations

### 14\. Conclusion

Although this system was motivated by a simple inventory system, it evolves into a **general-purpose data management architecture**.

Its key strengths are:

-   Scalability across multiple data types
-   Support for multiple file formats
-   Strong separation of concerns
-   Extensibility through design patterns
-   Reduced duplication through generic programming

Ultimately, this architecture demonstrates an important principle in game development:

> [!IMPORTANT]
>  Systems should be designed not just for the problem you are solving now, but for the systems you will need in the future.
> 


