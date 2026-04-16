# SerializationFactory Class

The `SerializationFactory` is a static factory class that serves as the central decision-making point for the system's data layer. It implements the Factory Pattern to instantiate and return the correct `ISerializationStrategy<T>` (e.g., CSV or JSON) based on the provided `DatabaseType`.

This abstraction allows the rest of your project to request a "Save Strategy" without needing to know the specific implementation details or class names of the underlying formats.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public static`
-   Pattern: Factory Method / Strategy Pattern
-   Core Purpose: To decouple the selection of a file format from the logic that saves or loads data.

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GetStrategy<T>** | `DatabaseType type` | `ISerializationStrategy<T>` | Examines the `DatabaseType` enum and returns a new instance of the corresponding strategy class. | Use whenever you are initializing a Data Manager or Database and need to define how files should be parsed. |

## Key Features & Logic

### 1\. Centralized Management

Without this factory, you would have to write `if (type == JSON) { strategy = new JSONStrategy<T>(); }` everywhere in your code. The factory centralizes this logic into one place. If you decide to add a new format (like XML or Binary) in the future, you only need to update this one `switch` expression.

### 2\. Type Safety with Generics

The method uses a generic type parameter `<T>` with the constraint `where T : DataEntityBase<T>`. This ensures that the factory only produces strategies for valid data entities, maintaining strict type safety across the entire data management pipeline.

### 3\. The "Fail-Fast" Mechanism

The factory includes a default switch case (`_ => throw ...`). If a developer adds a new type to the `DatabaseType` enum but forgets to implement the strategy in the factory, the system will throw an immediate and descriptive `ArgumentException`. This prevents the game from failing silently or crashing in unpredictable ways later during a save operation.

## Example Usage
```csharp
// Example of how the factory is used to grab a strategy dynamically
DatabaseType selectedType = DatabaseType.CSV;

// The factory handles the logic of whether to give you a CSVStrategy or JSONStrategy
ISerializationStrategy<PlayerData> myStrategy = SerializationFactory.GetStrategy<PlayerData>(selectedType);

Debug.Log($"Active Strategy: {myStrategy.GetType().Name}");
```
---

# ISerializationStrategy<T> Interface

The `ISerializationStrategy<T>` is a generic interface that defines a consistent contract for converting data entities into and out of stored formats (such as CSV, JSON, or XML). It is designed using the Strategy Pattern, allowing the data management system to swap serialization logic without changing the underlying data structures or manager logic.

Each implementation is responsible for defining the "how" of data transformation—turning an object into a string for saving and reconstructing that string back into an object for loading.


## Overview
-   Namespace: `CSG.DataManagement`
-   Type: `interface`
-   Constraints: `where T : IDataEntity`
-   Core Purpose: To decouple the data layer from specific storage formats and parsing logic, ensuring high flexibility and maintainability.    

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **ConfigureHeaders** | `string headerLine` | `void` | Processes the header row to define the expected data structure for the current load operation. | Called during initialization to map column order for row-based formats. |
| **GetHeader** | *None* | `string` | Builds a header row or schema label describing the serialized structure. | Used when creating new files to ensure the top-row metadata is correct. |
| **Serialize** | `T entity` | `string` | Converts a concrete data entity into its serialized string representation. | Use when preparing an object instance to be written to a file. |
| **Deserialize** | `T entity`, `string[] data` | `void` | Populates an existing entity instance with values from a serialized field array. | Use when loading data from a storage source back into Unity memory. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | Converts a single raw line of stored data into an array of structured fields. | Use as the first step of loading to break down raw text into manageable pieces. |
| **IsMatch** | `string entry`, `T entity` | `bool` | Determines if a raw serialized entry matches a specific entity (typically via ID). | Use during incremental saves to find the correct record to update. |
| **FindEntryByID** | `string allData`, `string entityID` | `string` | Scans a full dataset to find the specific entry string associated with a unique ID. | Use for targeted lookups when you do not wish to parse the entire database file. |

## Key Features & Architectural Role

### 1\. The Strategy Pattern

This interface is the "contract" for the Strategy Pattern. It allows the `DatabaseManager` to remain format-agnostic. The manager simply calls `Serialize()` or `Deserialize()`, and the specific strategy (e.g., `CSVStrategy` or `JSONStrategy`) handles the format-specific heavy lifting.

### 2\. Standardized Reconstruction

The combination of `ParseRawEntry` and `Deserialize` ensures that data reconstruction is a two-step, predictable process. By breaking raw strings into arrays first, the system can perform validation (checking `MinimumFieldCount`) before attempting to apply values to an object, preventing runtime crashes due to malformed data.

### 3\. Targeted Lookups

The `FindEntryByID` method allows implementations to optimize how they search through data. For example, a `CSVStrategy` can use prefix-matching logic to find a row in a large text file much faster than a generic parser that would have to deserialize every object in the list.

### 4\. Format-Agnostic Headers

While some formats (like CSV) use explicit headers and others (like JSON) are self-describing, the `ConfigureHeaders` and `GetHeader` methods ensure that any format-specific "metadata" is handled gracefully by the strategy rather than the entity itself.

---

# SerializationStrategyBase<T> Class

The `SerializationStrategyBase<T>` is an abstract base class that provides a common foundation for all concrete serialization strategies (like `JSONStrategy` or `CSVStrategy`). It implements the `ISerializationStrategy<T>` interface and serves a dual purpose: it reduces code duplication by providing "safe defaults" for optional features and enforces a strict structural contract through abstract methods.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public abstract`
    
-   Implements: `ISerializationStrategy<T>`
    
-   Constraints: `where T : DataEntityBase<T>`
    
-   Core Purpose: To act as a template for serialization logic, allowing developers to implement new file formats quickly by only focusing on what is unique to that format.

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **ConfigureHeaders** | `string headerLine` | `void` | **Virtual:** Provides a default no-op implementation for header configuration. | Override only for formats (like CSV) that require specific column mapping. |
| **FindEntryByID** | `string allData`, `string id` | `string` | **Virtual:** Returns an empty string by default for direct lookup operations. | Override if your format supports optimized searching without full parsing. |
| **Serialize** | `T data` | `string` | **Abstract:** Defines how a data entity is converted into a string format. | Must be implemented to handle the specific syntax of the target format. |
| **Deserialize** | `T entity`, `string[] data` | `void` | **Abstract:** Defines how an entity is reconstructed from parsed fields. | Must be implemented to map raw string values back to object properties. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | **Abstract:** Defines how a raw entry string is split into structured fields. | Must be implemented to handle format delimiters (commas, braces, etc.). |
| **IsMatch** | `string rawEntry`, `T data` | `bool` | **Abstract:** Defines the logic to check if a record belongs to a specific entity. | Must be implemented to facilitate incremental updates and row replacement. |
| **GetHeader** | *None* | `string` | **Abstract:** Defines the header or schema string for the format. | Must be implemented to provide the top-level metadata for new files. |

## Key Features & Architectural Role

### 1\. Reducing Boilerplate through Safe Defaults

Not every serialization format requires a header or optimized direct lookup. By providing virtual methods for `ConfigureHeaders` and `FindEntryByID`, this base class allows formats like JSON to ignore these features entirely without being forced to implement empty methods, keeping child classes clean.

### 2\. Enforcing a Consistent Foundation

By marking core methods as abstract, the base class ensures that every strategy—regardless of the file type—follows the exact same functional pipeline. This allows the `DatabaseManager` and `DataLoader` to interact with any strategy interchangeably, fulfilling the Strategy Pattern.

### 3\. Strict Type Safety

The class uses the constraint `where T : DataEntityBase<T>`. This ensures that strategies are only applied to valid data entities within your specific project hierarchy, preventing errors where a strategy might attempt to serialize an incompatible object.

## Technical Summary

This class sits at the heart of the serialization hierarchy, bridging the interface contract with practical, reusable code.

1.  Shared Foundation: All strategies share the same method signatures.
    
2.  Modular Extension: Adding a new format (e.g., `XMLStrategy`) simply requires inheriting from this base and filling in the abstract logic.
    
3.  Predictable Lifecycle: The base class guarantees that the `DataLoader` and `DataSaver` can always call the same sequence of methods to process data.

---

# CSVStrategy<T> Class

The `CSVStrategy<T>` is a concrete implementation of the `SerializationStrategyBase<T>`. It specializes in converting `IDataEntity` objects into a row-based CSV (Comma-Separated Values) format. This refactored version utilizes C# Reflection to automatically identify and map fields, significantly reducing the manual setup required for derived classes while maintaining a strict identity structure.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public class`
-   Inherits: `SerializationStrategyBase<T>` (Implements `ISerializationStrategy<T>`)
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a robust, automated tabular serialization method that handles complex encoding and dynamic field mapping.
    

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GetHeader** | *None* | `string` | Returns the current CSV header. It is automatically generated from field names during the first serialization. | Use when writing a new CSV file to provide column definitions. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | Delegates to `CSVUtility` to split a CSV row while respecting quotes and commas. | Use to convert a raw file line into a usable array of strings. |
| **GetPersistentFields**| `T entity` | `FieldInfo[]` | **Private:** Uses Reflection to find ID, Name, and all `[SerializeField]` or `public` fields. | Called internally to establish the "source of truth" for column order. |
| **Serialize** | `T entity` | `string` | Dynamically reads field values and converts them into a CSV-safe row. | Use to turn a ScriptableObject instance into a text line for saving. |
| **Deserialize** | `T entity`, `string[] data` | `void` | Automatically converts raw strings into the correct types (int, float, Enum, etc.) for the entity. | Use to populate an object with data loaded from a file. |
| **IsMatch** | `string entry`, `T entity` | `bool` | Checks if a row matches an entity based on its ID or Name. | Use when searching for specific records to update or replace. |
| **FindEntryByID** | `string allData`, `string id` | `string` | Scans a large block of text for a specific row starting with the provided ID. | Use for high-performance retrieval of a single record. |

## Key Features & Logic

### 1\. Automated Reflection Mapping

The strategy no longer requires you to manually define indices. It uses Reflection to look at your class and find:

-   Base Fields: Private `_entityID` and `_name` from the base class.
    
-   Custom Fields: Any field in your class that is either `public` or marked with `[SerializeField]`.
    

### 2\. Intelligent Type Conversion

During `Deserialize`, the strategy uses a `try-catch` safety net to handle data conversion:

-   Standard Types: Automatically converts strings to `int`, `float`, `bool`, etc., using `Convert.ChangeType`.
    
-   Enums: Specifically handles `Enum.Parse` to restore selection states.
    
-   Unity Objects: Currently stores Unity Object references (like Sprites or Prefabs) by name but skips them during automated loading to prevent broken reference errors.
    

### 3\. Header Auto-Generation

The `_header` is built the first time an entity is serialized. It takes the variable names (e.g., `_playerHealth`) and cleans them up for the CSV (e.g., `playerHealth`), ensuring your Excel files are readable and well-organized.

### 4\. Collision-Safe Indexing

The `FindEntryByID` implementation uses a strict prefix check: `lines[i].StartsWith(entityID + ",")`. This ensures that searching for ID "1" never accidentally returns ID "10" or "100".

## Technical Summary of Data Flow

1.  Field Discovery: The strategy maps out the "Column Order" by looking at the class variables.
    
2.  Serialization: `fields[i].GetValue(entity)` extracts the current values into a row.
    
3.  Deserialization: `fields[i].SetValue(entity, convertedValue)` pushes text data back into the object.
    
4.  Error Handling: If the CSV data doesn't match the variable type (e.g., text in a number field), a `Debug.LogWarning` is issued, and the system moves to the next field instead of crashing.

   
---

# JSONStrategy<T> Class

The `JSONStrategy<T>` class is a concrete implementation of the `SerializationStrategyBase<T>`. It leverages Unity's native `JsonUtility` to convert `IDataEntity` objects into a structured JSON format. Unlike row-based formats (like CSV), this strategy treats each entity as a self-contained, hierarchical data block, making it ideal for complex data structures, nested objects, and ScriptableObject persistence.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public class`
-   Inherits: `SerializationStrategyBase<T>` (Implements `ISerializationStrategy<T>`)
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a streamlined serialization path that preserves object hierarchy and strictly follows Unity’s internal serialization rules.
    

## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GetHeader** | *None* | `string` | Returns the placeholder string `"JSON_DATA_BLOB"`. | Use when the file system expects a schema label, even though JSON is schema-less. |
| **Serialize** | `T data` | `string` | Uses `JsonUtility.ToJson` with pretty-printing enabled. | Use to convert an entity into a human-readable JSON string for external storage. |
| **Deserialize** | `T entity`, `string[] data` | `void` | Uses `JsonUtility.FromJsonOverwrite` to update an existing object instance. | Use when loading data into an existing ScriptableObject to minimize memory overhead. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | Wraps the raw JSON string into a single-element array. | Use to satisfy the interface contract while treating the JSON block as one unit. |
| **IsMatch** | `string rawEntry`, `T data` | `bool` | Performs a fast string-containment check for the specific `_entityID` key. | Use to identify if a JSON block belongs to an entity without expensive full parsing. |

## Key Features & Logic

### 1\. Unified Serialization

By inheriting from `SerializationStrategyBase<T>`, this class fulfills the system-wide contract for data handling. It specifically utilizes `JsonUtility`, ensuring that any field marked `[SerializeField]` or set as `public` is automatically captured without manual mapping.

### 2\. Fast Identity Matching

The `IsMatch` implementation is optimized for performance. Instead of deserializing a complex JSON string just to check an ID, it performs a targeted string search for the format used by Unity's serializer: `"_entityID": "VALUE"`. This allows for rapid filtering of large data files.

### 3\. Memory Efficiency via "Overwrite"

A key advantage of this strategy is the use of `JsonUtility.FromJsonOverwrite`. Instead of creating a new object and discarding the old one, it "pours" the saved data into the fields of the object already sitting in memory. This is significantly more efficient for Unity’s garbage collector, especially when reloading many assets.

### 4\. Alignment with Unity Rules

This strategy is bound by Unity’s specific serialization constraints rather than generic JSON standards:

-   No Dictionaries: Standard dictionaries are not natively supported by `JsonUtility`.
    
-   Fields vs. Properties: Only fields are serialized; properties with `{ get; set; }` are ignored.
    
-   Single Object Focus: The input must represent a single object (enclosed in braces `{ }`) rather than a top-level array.


## Comparison: JSON vs. CSV Behavior

| Feature | JSONStrategy | CSVStrategy |
| :--- | :--- | :--- |
| **Data Structure** | Hierarchical / Nested | Flat / Tabular |
| **Header Requirement** | None (Placeholder) | Required (Column Names) |
| **Parsing Style** | Single Data Block | Delimiter Split (Commas) |
| **Unity Integration** | High (JsonUtility) | Manual (Reflection/Utility) |
| **Flexibility** | Better for complex objects | Better for simple databases |

### Architectural Role

The `JSONStrategy` ensures that the project remains flexible. If a specific data type (like a complex Quest system) requires nested logic that doesn't fit well into a CSV spreadsheet, the developer can simply switch the `DatabaseType` to `JSON` for that specific entity while keeping the rest of the pipeline identical.





























