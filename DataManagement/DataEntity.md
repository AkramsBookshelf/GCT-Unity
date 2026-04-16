# IDataEntity Interface
The `IDataEntity` interface is the essential blueprint for all data objects within the `CSG.DataManagement` system. It defines the "contract" that any game object (such as an item, quest, or character stat) must follow to be compatible with the automated loading and saving pipelines.

By standardizing how an object identifies itself, this interface allows the `DataLoader` and `DataSaver` to process any data type without needing to know its specific class details. In this refactored version, the interface is focused strictly on the identity and validation properties required for the persistence pipeline to function.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Type: `interface`
    
-   Core Purpose: To provide a unified structure for data identification and a baseline for field validation across all storage formats.
    

## Method and Property Reference Table
| Member | Type | Access | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **EntityID** | `string` | `get; set;` | The unique primary key for the object (e.g., "SWD_001"). | Used by strategies to find specific records in a file. |
| **Name** | `string` | `get; set;` | The human-readable name of the entity. | Used for asset naming and displaying information in the Unity Inspector. |
| **MinimumFieldCount** | `int` | `get;` | The minimum number of data fields required for valid reconstruction. | Used as a validation check during loading to prevent data corruption errors. |

## Key Roles in the Data System

### 1\. Unified Identification

Every data entity is guaranteed to have an `EntityID` and a `Name`. This consistency is what allows the `CSVStrategy` and `JSONStrategy` to perform fast lookups. Because the system knows exactly where the ID is located, it can update or find specific records without guessing which field is the "primary key."

### 2\. Standardized Foundation

The interface forms the foundation of the entire data pipeline. It ensures that regardless of whether you are creating an inventory item or a complex game configuration record, the persistence logic can always count on these three properties being available. This makes the system modular, allowing you to add new data types without ever modifying the core `DataLoader` or `DataSaver` logic.

### 3\. Safety and Validation

By providing a `MinimumFieldCount`, the interface allows the system to verify data integrity before a crash happens. If a raw data entry (like a line in a CSV) doesn't have enough fields to satisfy the entity's requirements, the system can catch the error, log a warning, and skip the corrupted entry rather than throwing an "Index Out of Range" exception.

---

# DataEntityBase<T> Class

The `DataEntityBase<T>` is the foundational abstract ScriptableObject that powers the persistence system. It manages the identity, naming consistency, and synchronization lifecycle for all game data assets (items, quests, etc.). This refactored version delegates the heavy lifting of file I/O to static utility classes, keeping the entity focused on its data state.


## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public abstract`
-   Inherits: `ScriptableObject`, `IDataEntity`
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a standardized, automated way to handle Unity assets that need to stay in sync with an external database.

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GenerateEntityID** | *None* | `void` | Generates a unique, stable ID based on class type and Unity GUID. | Automatically called to ensure the object is matchable in a database. |
| **GenerateStandardizedName** | `Type type`, `string raw` | `string` | **Static:** Formats a string into a safe file name (e.g., `Item_Sword_Data`). | Used to maintain project-wide naming conventions for assets. |
| **SetSyncStatus** | `bool status` | `void` | Updates the internal boolean tracking if the asset matches the disk file. | Use when manually forcing a sync state or after custom I/O operations. |
| **SaveEntityData** | *None* | `void` | Delegates saving to `DataSaver` using the entity's specific strategy and location. | Call to "push" local Inspector changes to the external database. |
| **LoadEntityData** | *None* | `void` | Delegates loading to `DataLoader` to refresh the asset with data from disk. | Call to "pull" the latest values from the database into the Unity asset. |
| **GetCoreFieldNames** | *None* | `string[]` | Returns the fundamental fields used across all entities (`_entityID`, `_name`). | Used by serialization strategies to define the base schema. |
| **GetDatabaseFileName**| *None* | `string` | Builds a filename based on the class type and format (e.g., `ItemDatabase.csv`). | Used to resolve which file this specific asset belongs to. |

## Key Features & Logic

### 1\. Advanced Sync Tracking

The class now features a robust synchronization state:

-   `IsDataSynced`: A read-only property that tracks if the Inspector data matches the external file.
    
-   `MarkAsUnsaved`: Automatically resets the sync status to `false` whenever a value is changed in the Inspector (via `OnValidate`).
    
-   Freshness Guard: Uses a private `_isFreshlyLoaded` flag to prevent the asset from immediately marking itself as "unsaved" during the initialization/loading process.
    

### 2\. Identity & GUID Persistence

The `EntityID` is generated once and stored. It combines the stripped type name (e.g., "Item") with the Unity Asset GUID. This ensures that even if you move the asset file or change its display name, its Database Identity remains immutable.

### 3\. Automated Editor Naming

To prevent project clutter, the class enforces a naming convention: `Type_Name_Data`.

-   Standardization: It automatically removes spaces and ensures the correct prefix and suffix exist.
    
-   Syncing: If the internal `_name` field is changed, the `RenameAssetFile` method (Editor only) renames the actual file on disk to match.
    

### 4\. Strategy & Location Delegation

The entity is completely format-agnostic. It defines what its format is (CSV, JSON, etc.) and where it should live, but delegates the how to the `SerializationFactory`:

-   `GetSerializationStrategy()`: Dynamically fetches the correct CSV or JSON logic at runtime.
    
-   `SaveLocation`: Determines if the asset belongs in the read-only `StreamingAssets` or the writable `PersistentData`.
