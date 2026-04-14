# DatabaseSettings (Enums)

The `DatabaseSettings` file serves as the configuration backbone for the `CSG.DataManagement` namespace. Rather than a functional class, this file defines the Enums that dictate the behavior of the `SerializationFactory`, `FileUtility`, and various strategy classes. By centralizing these definitions, the system ensures that all components use a unified language for data handling.

## Overview
-   Namespace: `CSG.DataManagement`
-   Type: `Enum` (Enumeration)
-   Core Purpose: To provide a fixed set of options for storage locations and file formats, reducing "magic strings" and preventing logic errors across the data pipeline.
    
## Enum Reference Table
| Enum Name | Value | Description | When to Use |
| :--- | :--- | :--- | :--- |
| **DatabaseType** | `Undefined` | The default state; indicates no format has been selected yet. | Use as a safety check to ensure a database has been properly initialized. |
| | `JSON` | JavaScript Object Notation format. | Use for complex, nested data or when using Unity's `JsonUtility`. |
| | `CSV` | Comma-Separated Values format. | Use for large, flat datasets (like item lists) that need to be edited in Excel. |
| | `XML` | Extensible Markup Language format. | Use when working with legacy systems or specific web-based data structures. |
| **DatabaseSaveLocation** | `StreamingAssets` | Points to the `Assets/StreamingAssets` folder. | Use for read-only data that ships with the game (e.g., base weapon stats). |
| | `PersistentData` | Points to the OS-specific persistent storage path. | Use for player-specific data that must be saved during gameplay (e.g., progress). |

## Key Features & System Role

### 1\. Driving the Strategy Pattern

The `DatabaseType` enum is the primary input for the `SerializationFactory`. By changing a single enum value in your game's configuration, the entire backend can switch from saving in CSV to JSON without needing to rewrite any file-handling code.

### 2\. Path Redirection

The `DatabaseSaveLocation` enum is used by `FileUtility` to resolve absolute file paths.

-   StreamingAssets: Generally treated as Read-Only once the game is built.
    
-   PersistentData: The only reliable location for writing data across all platforms (Windows, Mobile, Console).
    

### 3\. Maintainability

Because these are Enums, your IDE (like Visual Studio or Rider) will provide autocomplete and compile-time checking. If you rename a format or add a new one, the compiler will instantly point out every location in your project that needs to be updated.

---
