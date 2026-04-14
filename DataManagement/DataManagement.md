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

The `DatabaseManager<T>` is a generic controller that serves as the central hub for your data pipeline in Unity. It coordinates the lifecycle of game data—from loading external files into a runtime cache to merging new assets and saving them back to disk. By sitting between the file system and your game logic, it ensures that your data remains organized, searchable, and consistent.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public abstract` (Must be inherited by a specific manager, e.g., `InventoryManager`)
-   Inherits: `MonoBehaviour`
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a high-level API for managing collections of game entities while remaining agnostic of the underlying file format (CSV, JSON, etc.) via the Strategy Pattern.
    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **LoadFromFile** | `string fileName`, `DatabaseSaveLocation loc`, `DatabaseType type` | `void` | Uses a `DataLoader` to read an external file and populate the internal `_dataList` cache. | Call this during `Awake()` or `Start()` to initialize your game data from a file. |
| **RegisterAndSave** | *None* | `void` | Merges objects in the `_assetRegistrationList` into the main cache and triggers a save. | Use when you have created new ScriptableObjects in the editor and want to add them to the permanent database. |
| **SaveToDatabase** | *None* | `void` | **Private:** Orchestrates the actual write operation using `DataSaver` and the active `Strategy`. | Called internally by `RegisterAndSave` to commit the current memory cache to disk. |
| **ValidateAssetPath** | *None* | `void` | **Editor Only:** Cleans up the folder path string to ensure it is relative to the "Assets" folder. | Automatically called via `OnValidate` to prevent common pathing errors in the Inspector. |

## Key Features & Logic

### 1\. The Data Pipeline

The `DatabaseManager` acts as the "brain" of the following workflow:

1.  Load: `DataLoader` reads a file → Converts text to objects → `DatabaseManager` stores them in `_dataList`.
    
2.  Modify: Developer adds new assets to the `_assetRegistrationList`.
    
3.  Save: `DatabaseManager` merges lists → `DataSaver` converts objects back to text via `Strategy` → File is written.
    

### 4\. Lazy Strategy Initialization

The manager uses a "Lazy Loaded" property for the `Strategy`. It doesn't create the CSV or JSON strategy until the exact moment it is needed. Once created, it caches it for the remainder of the session to improve performance.

### 3\. Safety & Validation

-   Duplicate Prevention: When merging new assets, the manager checks if the object already exists in the cache to prevent double-entries in your database.
    
-   Empty Save Guard: If the database is empty, the manager aborts the save process to prevent accidentally overwriting a valid data file with a blank one.
    
-   Timestamping: It records the exact time the file was last modified during a load, providing a "Last Modified" read-out in the Inspector for debugging.

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






































