# Documentation: IDataEntity Interface

The `IDataEntity` interface is the essential blueprint for all data objects within the `CSG.DataManagement` system. It defines the "contract" that any game object (such as an item, quest, or character stat) must follow to be compatible with the automated loading and saving pipelines. By standardizing how an object identifies itself and how it exposes its internal variables, this interface allows the `DataLoader` and `DataSaver` to process any data type without needing to know its specific class details.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Type: `interface`
    
-   Core Purpose: To provide a unified structure for data identification and a standardized way to "flatten" objects into raw data arrays for external storage.
    

## Method and Property Reference Table
| Member | Type | Access | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **EntityID** | `string` | `get; set;` | The unique primary key for the object (e.g., "SWD_001"). | Used by strategies to find specific records in a file. |
| **Name** | `string` | `get; set;` | The human-readable name of the entity. | Used for asset naming and displaying information in the Unity Inspector. |
| **MinimumFieldCount** | `int` | `get;` | The minimum number of columns/data points expected. | Used as a validation check during loading to prevent data corruption errors. |
| **GetFullHeaderString** | `string` | `Method` | Returns the column names for the CSV or schema labels for the file. | Called by the DatabaseManager when creating or initializing a new data file. |
| **GetDataFields** | `object[]` | `Method` | Converts the object's specific properties into a flat array of values. | Called by the Strategy during the save process to gather data for serialization. |
| **SetDataFields** | `void` | `Method` | Takes a raw array of data and maps it back to the object's properties. | Called by the Strategy during the load process to restore the object's state. |

## Key Roles in the Data System

### 1\. Unified Identification

Every data entity is guaranteed to have an `EntityID` and a `Name`. This consistency is what allows the `CSVStrategy` and `JSONStrategy` to perform fast lookups. Because the system knows exactly where the ID is located, it can update or find specific records without guessing which field is the "primary key."

### 2\. Standardized Transformation

The interface acts as a translator between complex C# objects and simple data arrays:

-   Saving: The system asks the object for `GetDataFields()`. The object hands over an array like `[ "Iron Sword", 15, 100 ]`. The system then turns that array into a CSV line or JSON block.
    
-   Loading: The system reads a line, converts it into an array, and hands it to the object via `SetDataFields(fields)`. The object then decides which index in that array belongs to which internal variable (e.g., "Index 1 is my damage value").
    

### 3\. Safety and Validation

By providing a `MinimumFieldCount`, the interface allows the system to verify data integrity before a crash happens. If a CSV row only has 2 columns but the entity requires 5, the system can catch the error, log a warning, and skip the corrupted entry rather than throwing an "Index Out of Range" exception.

---

# DataEntityBase<T> Class

The `DataEntityBase<T>` is the foundational abstract ScriptableObject that powers the entire persistence system. Every piece of game data—whether it’s a sword, a quest, or a player stat—should inherit from this class.

It manages the "Identity" of an object (making sure it has a unique ID), its "Naming" (keeping the file name in sync with the data), and its "Lifecycle" (handling how it saves to and loads from external files like CSV or JSON).

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public abstract`
-   Inherits: `ScriptableObject`, `IDataEntity`
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a standardized, automated way to handle Unity assets that need to stay in sync with an external database.

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GenerateEntityID** | *None* | `void` | Generates a unique, stable ID based on the class type and Unity GUID. | Automatically called; ensures the object can be found in a database even if renamed. |
| **GenerateStandardizedName** | `Type type`, `string raw` | `string` | **Static:** Formats a string into a safe file name (e.g., `Item_Sword_Data`). | Used to ensure all assets in your project follow the same naming convention. |
| **SaveToDatabase** | *None* | `void` | Triggers the external save process to sync the asset with a file. | Call this when you want to "push" changes from the Unity Inspector to the database file. |
| **LoadEntityData** | *None* | `void` | Uses a `DataLoader` to pull external file data back into this specific asset. | Call this to "rehydrate" or update an asset from the master database file. |
| **GetFullHeaderString** | *None* | `string` | Combines base headers (ID, Name) with custom fields from the child class. | Used by savers to write the top row of a CSV or define a JSON schema. |
| **GetDataFields** | *None* | `object[]` | **Abstract:** Converts child class variables into a raw array for saving. | **Required override:** Define which of your custom variables should be saved. |
| **SetDataFields** | `object[] fields` | `void` | **Abstract:** Maps a raw array of loaded values back to child class variables. | **Required override:** Define how to map loaded text back to your variables. |

## Key Features

### 1\. Identity & GUID Mapping

The class generates a unique `EntityID` by combining the type name with the first 8 characters of Unity's internal GUID. This ensures that even if you move the asset to a different folder or rename it, the database still recognizes it as the same entity.

### 2\. Automated Naming Convention (Editor Only)

To keep your project clean, `DataEntityBase` automatically renames the asset file to match the format: `[Type]_[Name]_Data`.

-   Example: An `ItemData` asset named "Iron Sword" becomes `Item_IronSword_Data`.
    

### 3\. Data Synchronization Tracking

The `_isDataSynced` boolean tracks whether the data you see in the Unity Inspector matches what is saved in the external CSV/JSON file. If you change a value in the Inspector, the system marks the asset as "unsynced" until you save it.

### 4\. Format Agnostic

Because this class uses the `SerializationFactory`, a child class can choose to be a CSV entity or a JSON entity simply by changing a single property. The underlying logic for saving and loading remains identical.
