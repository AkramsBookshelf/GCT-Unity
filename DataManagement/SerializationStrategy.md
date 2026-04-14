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

The `ISerializationStrategy<T>` is a generic interface that defines a consistent contract for converting data entities to and from stored formats (such as CSV, JSON, or XML). By utilizing the Strategy Pattern, this interface allows developers to swap serialization methods without altering the core logic of the data entities themselves.

## Overview
-   Namespace: `CSG.DataManagement`
-   Type: `interface`
-   Constraints: `where T : IDataEntity`
-   Core Purpose: To decouple data storage logic from the data objects, ensuring that how an object is saved (serialized) and loaded (deserialized) can be changed independently of the data structure.
    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **ConfigureHeaders** | `string headerLine` | `void` | Parses and stores the header configuration to map data fields correctly. | Use during the initialization of a load operation to identify column/field order. |
| **GetHeader** | *None* | `string` | Returns a string representing the top-level structure (e.g., column names for CSV). | Use when creating a new save file to ensure the header matches the data format. |
| **Serialize** | `T entity` | `string` | Converts a single data entity object into its string-based representation. | Use when saving a specific object to a file or database. |
| **Deserialize** | `T entity`, `string[] data` | `void` | Populates the fields of an existing entity using an array of parsed string data. | Use when loading data from a storage source back into an active object. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | Breaks a raw line of text from storage into an array of individual data fields. | Use to convert a raw file line into a format that the `Deserialize` method can understand. |
| **IsMatch** | `string entry`, `T entity` | `bool` | Compares a raw entry string against an entity to check for a match (usually by ID). | Use when performing updates or deletions to identify the correct row in a dataset. |
| **FindEntryByID** | `string allData`, `string entityID` | `string` | Searches a block of text for a specific entry that matches the provided Unique ID. | Use when you need to retrieve a single specific record from a large text-based dataset. |

## Key Features & Strategy Implementation

### 1\. The Strategy Pattern

This interface is the "Strategy" in the design pattern. You can create different classes that implement this interface (e.g., `CSVSerializationStrategy`, `JSONSerializationStrategy`) and swap them at runtime. This allows your project to support multiple file types with minimal code changes.

### 2\. Generic Constraints

The use of `<T> where T : IDataEntity` ensures that this interface can only be used with classes that implement the `IDataEntity` interface. This provides type safety and ensures that any object being serialized has the necessary properties (like an ID) to be handled correctly.

### 3\. Decoupled Parsing

The inclusion of `ParseRawEntry` and `ConfigureHeaders` ensures that the strategy handles the "flavor" of the data. For example:

-   A CSV Strategy would split by commas.
    
-   A Tab Strategy would split by tabs.
    
-   A Fixed-Width Strategy would split by character count.
    

The data entity itself remains blissfully unaware of these storage details, focusing only on its own properties.

---

# SerializationStrategyBase<T> Class

The `SerializationStrategyBase<T>` is an abstract base class that provides a common foundation for all concrete serialization strategies (like JSON or CSV). It implements the `ISerializationStrategy<T>` interface and serves two main purposes: reducing code duplication by providing "safe defaults" for optional methods and enforcing a strict contract for required serialization logic via abstract methods.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public abstract`
    
-   Implements: `ISerializationStrategy<T>`
    
-   Constraints: `where T : DataEntityBase<T>`
    
-   Core Purpose: To provide a template for serialization logic, allowing specific formats to only implement what is unique to them while inheriting shared behavior.    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **ConfigureHeaders** | `string headerLine` | `void` | **Virtual:** A placeholder for formats like CSV that need header setup. | Override this only if your specific format requires pre-parsing of column names. |
| **FindEntryByID** | `string allData`, `string id` | `string` | **Virtual:** A placeholder for optimized ID searching within a raw dataset. | Override this if your format supports fast lookup without parsing the entire file. |
| **Serialize** | `T data` | `string` | **Abstract:** Forces child classes to define how an entity becomes a string. | Must be implemented by the child (e.g., calling `JsonUtility` or `CSVUtility`). |
| **Deserialize** | `T entity`, `string[] data` | `void` | **Abstract:** Forces child classes to define how to fill an entity from parsed data. | Must be implemented to map raw strings back to object properties. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | **Abstract:** Forces child classes to define how to split a raw line into fields. | Implement this to handle the specific delimiters (commas, tabs, etc.) of the format. |
| **IsMatch** | `string rawEntry`, `T data` | `bool` | **Abstract:** Forces child classes to define identity matching logic. | Implement to allow the system to find specific records for updates or deletions. |
| **GetHeader** | *None* | `string` | **Abstract:** Forces child classes to provide a schema or header string. | Implement to define the "top line" of the data file for your specific format. |

## Key Features & Architectural Role

### 1\. Reducing "Boilerplate" Code

Not every serialization format needs a header (JSON doesn't, but CSV does). By providing a virtual `ConfigureHeaders` method that does nothing by default, the `JSONStrategy` can simply ignore it, while the `CSVStrategy` can choose to override it. This keeps child classes clean and focused.

### 2\. Enforcing Consistency

By marking the core serialization methods as abstract, this base class guarantees that any new format added to the system (like XML or Binary) will have the exact same functional footprint. This ensures that the `SerializationFactory` and your Data Managers can treat all strategies the same way.

### 3\. Type Safety

The constraint `where T : DataEntityBase<T>` ensures that this base class is only ever used with entities that follow your project's specific data model. This prevents the strategy from being applied to incompatible objects.


---

# CSVStrategy<T> Class

The `CSVStrategy<T>` class is a concrete implementation of the `SerializationStrategyBase<T>`. It specializes in converting `IDataEntity` objects into a row-based CSV (Comma-Separated Values) format. By inheriting from the base strategy class, it enforces a standardized data structure where every entry begins with an `EntityID` and a `Name`, followed by any custom data fields defined by the specific entity.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public class`
-   Inherits: `SerializationStrategyBase<T>` (Implements `ISerializationStrategy<T>`)
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a robust, tabular serialization method that handles complex CSV encoding rules while allowing for flexible entity extension.
    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **ConfigureHeaders** | `string header` | `void` | Stores a custom comma-separated string to be used as the CSV header. | Use when initializing the strategy to define the "top row" of your CSV file. |
| **GetHeader** | *None* | `string` | Returns the header string previously set via `ConfigureHeaders`. | Use when writing a new CSV file to disk to provide the column definitions. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | Delegates to `CSVUtility` to split a CSV row into an array of individual strings. | Use when reading a file line-by-line before passing data to the Deserialize method. |
| **Serialize** | `T entity` | `string` | Combines an entity's ID, Name, and custom data fields into a single CSV-safe string. | Use when converting an active object into a text row for saving. |
| **Deserialize** | `T entity`, `string[] data` | `void` | Validates the field count and populates an entity's base and custom properties from string data. | Use when loading data from a file back into an existing object instance. |
| **IsMatch** | `string entry`, `T entity` | `bool` | Checks if a CSV row matches an entity based on its ID (primary) or Name (fallback). | Use when searching for an existing record to update or delete within a dataset. |
| **FindEntryByID** | `string allData`, `string entityID` | `string` | Scans a large block of text to find the specific row starting with the provided ID. | Use to retrieve a single record from a loaded file without parsing the entire dataset. |

### Key Features & Logic

### 1\. Tabular Structure Enforcement

This strategy ensures that every CSV file created by the system is predictable. It forces the first two columns to always be the `EntityID` and the `Name`, which makes searching and debugging significantly easier across different entity types.

### 2\. Intelligent Data Mapping

When saving (Serialize), the strategy pulls custom data from the entity. When loading (Deserialize), it intelligently skips the first two columns (ID and Name) and passes the remaining "custom" array back to the entity. This keeps the serialization logic separate from the specific properties of the game objects.

### 3\. Collision-Safe Indexing

The implementation of `FindEntryByID` includes a vital safety check: it looks for `entityID + ","`.

-   The Problem: If you search for ID `10` in a file, a simple search might find ID `101`.
    
-   The Solution: By appending the comma, the strategy ensures it only matches the exact ID column, preventing data corruption or incorrect lookups.
    

### 4\. Robust Parsing

By overriding `ParseRawEntry` to use `CSVUtility`, this class can handle "dirty" data, such as a player description that contains its own commas or line breaks, without breaking the file structure.

---

# JSONStrategy<T> Class

The `JSONStrategy<T>` class is a concrete implementation of the `SerializationStrategyBase<T>`. It specializes in converting `IDataEntity` objects into a structured JSON format using Unity's native `JsonUtility`. Unlike row-based formats (like CSV), this strategy treats each entity as a self-contained, hierarchical data block, making it ideal for complex data or ScriptableObject persistence.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public class`
-   Inherits: `SerializationStrategyBase<T>` (Implements `ISerializationStrategy<T>`)
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a streamlined serialization path that preserves object hierarchy and supports Unity’s internal serialization rules.
    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GetHeader** | *None* | `string` | Returns the placeholder string `"JSON_DATA_BLOB"`. | Use when a file system expects a schema label, even though JSON is schema-less. |
| **Serialize** | `T data` | `string` | Uses `JsonUtility.ToJson` with pretty-printing enabled. | Use to convert an entity into a human-readable JSON string for saving. |
| **Deserialize** | `T entity`, `string[] data` | `void` | Uses `JsonUtility.FromJsonOverwrite` to update an existing object's fields. | Use when loading data into an existing instance to minimize memory allocations. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | Wraps the raw JSON string into a single-element array. | Use to satisfy the interface contract where "data fields" are expected as an array. |
| **IsMatch** | `string rawEntry`, `T data` | `bool` | Performs a fast string-containment check for the `_entityID` key. | Use to quickly identify if a specific JSON block belongs to a target entity without full parsing. |

## Key Features & Logic

### 1\. Unified Serialization

Because it inherits from `SerializationStrategyBase<T>`, it fulfills the system-wide contract for data handling. However, it overrides the Abstract methods to specifically use `JsonUtility`, ensuring that any field marked `[SerializeField]` is captured.

### 2\. Fast Identity Matching

The `IsMatch` implementation is optimized for performance. Instead of deserializing the entire JSON string to check an ID, it searches the raw text for the specific key-value pair format used by Unity's serializer: `"_entityID": "YOUR_ID"`.

### 3\. Memory Efficiency

By using `JsonUtility.FromJsonOverwrite`, the strategy avoids creating new object instances during the load process. It simply "pours" the saved data into the fields of the object already sitting in memory.

### 4\. Format Limitations

Developers should be aware that this class is bound by Unity’s `JsonUtility` constraints:

-   No Dictionaries: Nested dictionaries will not be saved.
    
-   Fields Only: Properties with `{ get; set; }` are ignored unless they have a backing field marked `[SerializeField]`.
    
-   Single Object: The input must be a single JSON object, not a top-level array.


## Comparison: JSON vs. CSV Behavior

| Feature | JSONStrategy | CSVStrategy |
| :--- | :--- | :--- |
| **Data Structure** | Hierarchical / Nested | Flat / Tabular |
| **Header Requirement** | None (Placeholder) | Required (Column Names) |
| **Parsing Style** | Single Block | Delimiter Split (Commas) |
| **Unity Integration** | High (JsonUtility) | Manual (CSVUtility) |
| **Best For** | Complex Entities | Item Databases / Lists |






























