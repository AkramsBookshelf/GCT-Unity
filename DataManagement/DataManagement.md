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

The `DataLoader<T>` class is the primary import engine of the data management system. Its job is to bridge the gap between external text files (like CSV or JSON) and Unity’s native ScriptableObject system. It handles reading raw data, delegating the parsing to a serialization strategy, and ensuring that the data is correctly converted into permanent, standardized assets within the Unity Project.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public`
    
-   Constraints: `where T : DataEntityBase<T>`
    
-   Core Purpose: To transform raw external strings into fully populated, persistent Unity ScriptableObject assets.
    

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **LoadSingleEntity** | `T entity`, `string fileName`, `DatabaseSaveLocation loc`, `ISerializationStrategy<T> strategy` | `void` | **Static:** Finds a record by ID, deserializes it into the provided instance, and enforces naming rules. | Use to "rehydrate" or refresh a single existing ScriptableObject from the database. |
| **LoadAllData** | `string fileName`, `DatabaseSaveLocation loc`, `ISerializationStrategy<T> strategy` | `List<T>` | Reads an entire file, configures the header schema, and initiates batch processing. | Use as the main entry point to load an entire database (e.g., all weapons or all quests). |
| **ProcessEntries** | `string[] rawEntries`, `ISerializationStrategy<T> strategy` | `List<T>` | **Private:** Iterates through raw rows (skipping header) to generate the final list of entities. | Called internally by `LoadAllData` to handle the conversion loop. |
| **CreateEntityFromEntry** | `string entry`, `ISerializationStrategy<T> strategy` | `T` | **Private:** Instantiates a new ScriptableObject and uses the strategy to map raw fields. | Called internally to transform a single line of text into a memory-resident object. |
| **SaveAsset** | `T dataEntry` | `T` | **Editor Only:** Writes the ScriptableObject to disk or updates an existing one using `CopySerialized`. | Called internally during import to ensure data persists as a `.asset` file in the project. |
| **FinalizeLoad** | `int count` | `void` | **Editor Only:** Saves the AssetDatabase and refreshes the Project window. | Called after a batch import to make newly created assets visible in the Unity Inspector. |

## Key Features & Pipeline Logic
## Key Features & Pipeline Logic

### 1\. The Transformation Pipeline

The `DataLoader` follows a strict sequence to ensure data integrity:

1.  Read: Fetches the raw string array from the resolved `FileUtility` path.
    
2.  Configure: Passes the header row to the `ISerializationStrategy` to define the data structure.
    
3.  Instantiate: Creates a "clean" `ScriptableObject` instance in memory.
    
4.  Populate: Delegates to the strategy to `Deserialize` the raw text into object fields.
    
5.  Persist: (Editor Only) Resolves the directory and saves the object as a physical `.asset` file.
    

### 2\. Format Agnosticism

By delegating all `ParseRawEntry` and `Deserialize` calls to the `ISerializationStrategy`, the `DataLoader` remains completely agnostic of the file format. Whether you are loading a CSV or a JSON file, this class uses the same logic.

### 3\. Smart Asset Syncing (Editor)

To prevent breaking existing scene references, the `SaveAsset` method is non-destructive:

-   Update: If an asset with the same name already exists, it uses `EditorUtility.CopySerialized` to overwrite values while keeping the internal GUID intact.
    
-   Create: If no asset exists, it uses `AssetDatabase.CreateAsset` to generate a new file.
    

### 4\. Direct Entity Refresh

The updated `LoadSingleEntity` is now static. This allows a specific data entity to trigger its own refresh logic by passing itself into the loader. It ensures that the Unity asset name is updated to match the database state immediately.

## Technical Details

### Asset Path Management

When the `DataLoader` is initialized via its constructor, it stores a target `_assetDirectoryPath`. During processing, it uses `FileUtility.GetAssetDirectoryPath` to verify and, if necessary, recursively create the folder structure inside the Unity project.

### Conditional Compilation

Asset management logic (saving, dirtying, and refreshing) is wrapped in `#if UNITY_EDITOR` blocks. This ensures that the loader functions as a data importer during development but remains lightweight and safe for runtime-only file reading in built games.

---
# DataSaver<T> Class

The `DataSaver<T>` class is the final stage of the data management pipeline. Its primary role is to take memory-resident ScriptableObject entities and translate them back into persistent text files (like CSV or JSON).

This refactored version optimizes the Batch Save logic: instead of blindly overwriting the entire file, it now performs an intelligent merge, checking for existing records and updating them while preserving other data in the file.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public`
    
-   Constraints: `where T : ScriptableObject, IDataEntity`
    
-   Core Purpose: To provide a standardized, format-agnostic way to write data entities to disk using intelligent row-matching and insertion logic.
    

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **SaveData** | `T data`, `string fileName`, `DatabaseSaveLocation loc`, `ISerializationStrategy<T> strategy` | `void` | **Static:** Performs an incremental save. It searches for an existing record to update or appends a new one. | Use for real-time updates where you only want to change one specific entity. |
| **SaveBatch** | `IEnumerable<T> dataObjects`, `string fileName`, `DatabaseSaveLocation loc`, `ISerializationStrategy<T> strategy` | `void` | **Static:** Rewrites the dataset by merging a collection of entities into the existing file. | Use when synchronizing a list of new assets (like from the Registration List) into the main database. |
| **UpdateExistingEntry** | `List<string> entries`, `T data`, `string newEntry`, `ISerializationStrategy<T> strategy` | `bool` | **Private:** Iterates through file rows and uses the strategy's `IsMatch` logic to find and replace a record. | Called internally by both save methods to prevent duplicate IDs in the same file. |

## Key Features & Logic

### 1\. Intelligent Merging (Refactored)

Unlike previous versions that simply wiped the file clean, the new `SaveBatch` implementation follows a "Smart Merge" workflow:

-   It reads the existing file contents first.
    
-   It iterates through your objects and uses `UpdateExistingEntry` to see if that ID already exists in the file.
    
-   Update: If the ID matches, it replaces that specific line.
    
-   Insert: If the ID is new, it appends it to the bottom.
    

### 2\. Strategy-Driven Serialization

The `DataSaver` remains completely format-agnostic. It delegates all technical formatting to the `ISerializationStrategy`:

1.  Serialize: Turns the object fields into a string row.
    
2.  GetHeader: Provides column names if the file is being created for the first time.
    
3.  IsMatch: Identifies which line in a CSV or JSON block belongs to which unique ID.
    

### 3\. Automatic Directory Handling

Before writing, `DataSaver` calls `FileUtility.ValidateDirectory`. This ensures the target path exists, preventing crashes if you try to save to a subfolder that hasn't been created yet.

### 4\. Safety Checks

Both methods include guards to prevent illegal operations. If you attempt to save an empty collection, the system simply returns, ensuring you don't accidentally corrupt or erase an existing database file with an empty operation.

## Technical Details

### Workflow Summary

1.  Path Resolution: Locate the absolute file path via `FileUtility`.
    
2.  Existing Check: Load current file lines into memory.
    
3.  Header Logic: If the file is new, grab the header string from the strategy.
    
4.  Serialization: Convert Unity objects into strings via `strategy.Serialize`.
    
5.  Sync/Merge: Search for matching entries to replace; otherwise, append.
    
6.  Disk Write: Commit the updated list of strings to storage.
    
7.  Asset Refresh: (Editor Only) Trigger `AssetDatabase.Refresh()` to update the Project window.
    

### Conditional Compilation

The `UnityEditor.AssetDatabase.Refresh()` call is wrapped in `#if UNITY_EDITOR`. This is critical as the `AssetDatabase` class is excluded from final game builds (Windows, Mac, mobile). Without this wrap, your game would fail to compile during the build process.


































