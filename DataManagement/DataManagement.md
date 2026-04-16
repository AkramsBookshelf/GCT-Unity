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

# DatabaseManager<T> Class

The `DatabaseManager<T>` is a generic controller that serves as the central hub for your data pipeline in Unity. It coordinates the lifecycle of game data—from loading external files into a runtime cache to managing in-memory sync status and saving data back to disk. By sitting between the file system and your game logic, it ensures that your data remains organized, searchable, and consistent across both Runtime and the Unity Editor.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public abstract` (Must be inherited by a specific manager, e.g., `InventoryManager`)
-   Inherits: `MonoBehaviour`
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a high-level coordinator for managing collections of game entities while remaining agnostic of the underlying file format (CSV, JSON, etc.) via the Strategy Pattern.
    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **LoadFromFile** | `string fileName`, `DatabaseSaveLocation loc`, `DatabaseType type` | `void` | Reads external content, replaces the in-memory cache, and marks all entities as synced. | Call during `Awake()` or `Start()` to initialize the game state from a database file. |
| **ClearDataListCache** | *None* | `void` | Wipes the current `_dataList` to prepare for a fresh data state. | Used internally during reloads or when switching databases. |
| **MarkAsSaved** | `List<T> dataList` | `void` | **Private:** Iterates through entities and sets their internal sync status to `true`. | Automatically called after a successful load to ensure UI/Editor state reflects reality. |
| **SaveToDatabase** | *None* | `void` | Persists the registration list to disk, clears the queue, and refreshes the runtime cache. | Call this when you need to commit newly registered ScriptableObjects to the permanent file. |
| **ValidateAssetPath** | *None* | `void` | **Editor Only:** Strips "Assets/" prefixes from path strings to prevent file utility errors. | Automatically called via `OnValidate` to ensure Inspector settings are correctly formatted. |

## Key Features & Logic

### 1\. Refactored Data Flow

The manager acts as the "brain" of the following synchronized workflow:

1.  Load: `DataLoader` reads a file → Converts text to objects → `DatabaseManager` caches them in `_dataList` and marks them as Synced.
    
2.  Register: Developer adds new assets to the `_assetRegistrationList` (the "pending" queue).
    
3.  Save: `DataSaver` writes the pending list to the file via the selected `Strategy`.
    
4.  Refresh: The manager clears the old cache and reloads from the newly written file to ensure memory and disk are perfectly aligned.
    

### 2\. Strategy Decoupling (Refactored)

The class is now more strictly decoupled from serialization logic. It uses a Lazy Loaded property for the `Strategy`. It doesn't instantiate the CSV or JSON logic until the moment of the first file operation, then caches that instance for performance.

### 3\. Sync Status Management

With the latest update, the `DatabaseManager` now takes responsibility for the "handshake" between the file and the objects. By calling `entry.SetSyncStatus(true)` upon loading, it allows individual ScriptableObjects to visually or logically indicate that they match the data stored on disk.

### 4\. Safety & Validation

-   Asset Registration Guard: The `SaveToDatabase` method includes a check to prevent "empty saves." If no new assets are in the registration list, the operation is aborted to avoid unnecessary file I/O.
    
-   Path Cleaning: The `ValidateAssetPath` method automatically corrects user input in the Unity Inspector, ensuring that folder paths don't include redundant "Assets/" prefixes that would break the `Path.Combine` logic in `FileUtility`.
    
-   Timestamping: For debugging, the manager records the `LastModified` time of the database file at the exact moment of loading, which is visible as a read-only field in the Inspector.

---

# DataLoader<T> Class

The `DataLoader<T>` class is the primary import engine of the data management system. Its job is to bridge the gap between external text files (like CSV or JSON) and Unity’s native ScriptableObject system. It handles reading raw data, delegating the parsing to a serialization strategy, and ensuring that the data is correctly converted into permanent assets within the Unity Project.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public`
    
-   Constraints: `where T : DataEntityBase<T>`
    
-   Core Purpose: To transform raw external strings into fully populated, persistent Unity ScriptableObject assets.
    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **LoadSingleEntity** | `T entity`, `string fileName`, `DatabaseSaveLocation loc`, `ISerializationStrategy<T> strategy` | `void` | Finds a specific record by ID in a file and populates an existing object instance. | Use when you only need to refresh or load a single specific item rather than the whole database. |
| **LoadAllData** | `string fileName`, `DatabaseSaveLocation loc`, `ISerializationStrategy<T> strategy` | `List<T>` | Reads an entire file, configures headers, and initiates the batch conversion process. | Use as the main entry point to load an entire database (e.g., all weapons or all quests). |
| **ProcessEntries** | `string[] rawEntries`, `ISerializationStrategy<T> strategy` | `List<T>` | **Private:** Iterates through raw rows, creates object instances, and triggers asset saving. | Called internally by `LoadAllData` to handle the conversion loop. |
| **CreateEntityFromEntry** | `string entry`, `ISerializationStrategy<T> strategy` | `T` | **Private:** Instantiates a new ScriptableObject and uses the strategy to populate its fields. | Called internally to transform a single line of text into a memory-resident object. |
| **SaveAsset** | `T dataEntry` | `T` | **Editor Only:** Writes the ScriptableObject to the Project folder or updates an existing one. | Called internally during import to ensure data persists as a `.asset` file in the Unity Editor. |
| **FinalizeLoad** | `int count` | `void` | **Editor Only:** Saves the AssetDatabase and refreshes the Project window. | Called after a batch import to make newly created assets visible in the Unity Inspector. |

## Key Features & Pipeline Logic

### 1\. The Transformation Pipeline

The `DataLoader` follows a strict sequence to ensure data integrity:

1.  Read: Fetches the raw string or array of lines from a file.
    
2.  Configure: Passes the header (first row) to the strategy to define the data schema.
    
3.  Instantiate: Creates a "clean" `ScriptableObject` instance in memory.
    
4.  Populate: Asks the `ISerializationStrategy` to fill that object with data.
    
5.  Persist: (Editor Only) Saves that object as a physical `.asset` file in the project.
    

### 2\. Strategy Delegation

The `DataLoader` does not know how to read CSV or JSON. It simply holds a reference to an `ISerializationStrategy`. This means you can use the same `DataLoader` code for any file format, as long as you provide the correct strategy.

### 3\. Smart Asset Sync (Editor)

The `SaveAsset` method is designed to be non-destructive:

-   If an asset with the same name already exists in your folder, it uses `EditorUtility.CopySerialized` to update the values without breaking existing references in your scenes.
    
-   If the asset doesn't exist, it creates a brand new one.
    

### 4\. Standardized Naming

The loader uses `DataEntityBase<T>.GenerateStandardizedName` to ensure that assets created from the database follow a consistent naming convention (e.g., `Item_001_IronSword`), making the project hierarchy much easier to navigate.

## Technical Details

### Asset Path Management

When the `DataLoader` is initialized, it is given an `assetPath`. This path is automatically validated by the `FileUtility` to ensure it exists within the Unity `Assets/` folder.

### Conditional Compilation

Large portions of this class (specifically asset creation and saving) are wrapped in `#if UNITY_EDITOR` blocks. This ensures that the loader functions correctly as a data importer during development, but doesn't try to perform illegal "save to project" operations in a compiled game build.

---
# DataSaver<T> Class

The `DataSaver<T>` class is the final stage of the data management pipeline. Its primary role is to take memory-resident ScriptableObject entities and translate them back into persistent text files (like CSV or JSON). It provides both incremental saving for individual items and batch saving for entire databases, ensuring that data is synchronized accurately between Unity and external storage.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public`
    
-   Constraints: `where T : ScriptableObject, IDataEntity`
    
-   Core Purpose: To provide a standardized, format-agnostic way to write data entities to disk, either by updating existing records or rewriting full datasets.
    

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **SaveData** | `T data`, `string fileName`, `ISerializationStrategy<T> strategy`, `DatabaseSaveLocation location` | `void` | **Static:** Performs an incremental save. It searches for an existing record to update or appends a new one. | Use for real-time updates where you only want to change one specific entity without rewriting the entire file. |
| **SaveBatch** | `IEnumerable<T> dataObjects`, `string fileName`, `ISerializationStrategy<T> strategy`, `DatabaseSaveLocation location` | `void` | **Static:** Rewrites the entire file using a provided collection of entities. | Use when exporting large datasets or performing a full database synchronization. |
| **UpdateExistingEntry** | `List<string> entries`, `T data`, `string newEntry`, `ISerializationStrategy<T> strategy` | `bool` | **Private:** Iterates through file rows and uses the strategy's `IsMatch` logic to find and replace a record. | Called internally by `SaveData` to determine if an entry needs to be overwritten or added as new. |

## Key Features & Logic

### 1\. Incremental vs. Batch Saving

-   Incremental (`SaveData`): This is "smart" saving. It reads the current file, uses the `ISerializationStrategy` to find the line that matches the entity's ID, and swaps that specific line out. This prevents duplicating data if you save the same item twice.
    
-   Batch (`SaveBatch`): This is "clean slate" saving. It completely overwrites the target file with the current collection. This is generally faster for large initialization tasks or total database resets.
    

### 2\. Strategy-Driven Serialization

Just like the `DataLoader`, the `DataSaver` does not know the difference between a comma or a curly brace. It asks the `ISerializationStrategy` to:

1.  Serialize: Turn the object into a string.
    
2.  GetHeader: Provide the top-row column names (if required).
    
3.  IsMatch: Identify which existing line in the file belongs to which object.
    

### 3\. Automatic Directory Handling

Before any file operation, `DataSaver` calls `FileUtility.ValidateDirectory`. This ensures that if the target folder (e.g., `Saves/User1/`) doesn't exist, it is created automatically, preventing "Path Not Found" exceptions.

### 4\. Safety Checks

The batch saver includes a null/empty check. If you attempt to save an empty list, the system logs a warning and aborts rather than deleting your existing data file by overwriting it with nothing.

## Technical Details

### Workflow Summary

1.  Identify: Locate the file path via `FileUtility`.
2.  Serialize: Convert the `ScriptableObject` fields into a string based on the chosen format.
3.  Synchronize: Check if the record exists; if so, replace the line. If not, append it.
4.  Write: Commit the list of strings to the physical disk.
5.  Refresh: (Editor Only) Trigger `AssetDatabase.Refresh()` so the changes are immediately visible in the Unity project view.
   

### Conditional Compilation
The `UnityEditor.AssetDatabase.Refresh()` call is wrapped in `#if UNITY_EDITOR`. This is essential because the `AssetDatabase` class does not exist in build players (Windows, Android, etc.), and including it would cause the game build to fail.





































