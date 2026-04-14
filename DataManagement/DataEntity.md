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

