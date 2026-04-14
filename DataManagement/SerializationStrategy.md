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

The `CsvStrategy<T>` is a concrete implementation of the `ISerializationStrategy<T>` interface. It is specifically designed to handle the conversion of `IDataEntity` objects into standard CSV (Comma-Separated Values) rows and back again. It relies on the `CSVUtility` class to ensure data integrity, especially when dealing with fields that contain commas or quotes.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public class`
    
-   Inherits: `ISerializationStrategy<T>`
    
-   Constraints: `where T : IDataEntity`
    
-   Core Purpose: To provide a standardized CSV format for all game entities, ensuring that every record begins with an `EntityID` and `Name`, followed by custom data specific to the entity type.
    

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

## Key Features & Logic

### 1\. Standardized Data Format

This strategy enforces a specific column order to ensure consistency across the project:

1.  Index 0: `EntityID` (The unique identifier)
    
2.  Index 1: `Name` (The display name)
    
3.  Index 2+: `Custom Fields` (Data unique to the specific subclass)
    

### 2\. Flexible Extensions

While it enforces a base structure (ID and Name), it uses `entity.GetDataFields()` and `entity.SetDataFields()` to handle any number of additional variables. This allows a "Player" entity and an "Enemy" entity to use the same strategy even if they have different amounts of data.

### 3\. Collision-Safe Searching

In the `FindEntryByID` method, the code checks for `entityID + ","`. This is a critical safety feature that prevents "Partial ID Collisions."

-   Without the comma: Searching for ID `1` might accidentally return the row for ID `10` or `100`.
    
-   With the comma: It ensures that only an exact match for the ID is returned.
    

### 4\. Integration with CSVUtility

By delegating the heavy lifting of parsing and row creation to `CSVUtility`, this class remains focused strictly on the mapping between C# objects and the CSV structure, adhering to the Single Responsibility Principle.

---

# Documentation: JSONStrategy<T> Class

The `JSONStrategy<T>` class is a concrete implementation of the `ISerializationStrategy<T>` interface. It leverages Unity's built-in `JsonUtility` to convert `IDataEntity` objects into a structured JSON format. Unlike row-based formats like CSV, JSON stores data as an object tree, making it excellent for complex data structures and ScriptableObjects where hierarchy matters more than column order.

## Overview
-   Namespace: `CSG.DataManagement`
-   Access: `public class`
-   Inherits: `ISerializationStrategy<T>`
-   Constraints: `where T : DataEntityBase<T>`
-   Core Purpose: To provide a single-block serialization method that handles nested data more naturally than a flat table.


## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GetHeader** | *None* | `string` | Returns a fixed label ("JSON_DATA_BLOB"). | Use as a placeholder since JSON is schema-less and doesn't require column headers. |
| **Serialize** | `T data` | `string` | Converts the entire object (base fields + custom fields) into a JSON string with pretty-printing. | Use when saving data to a file where readability or object hierarchy is important. |
| **Deserialize** | `T entity`, `string[] data` | `void` | Uses `JsonUtility.FromJsonOverwrite` to map values from a JSON string directly into an existing object. | Use when loading an entity to ensure the specific instance is updated with saved data. |
| **ParseRawEntry** | `string rawEntry` | `string[]` | Returns the raw string wrapped in a single-element array. | Used internally to satisfy the interface contract while treating JSON as a single field. |
| **IsMatch** | `string rawEntry`, `T data` | `bool` | Performs a string-based search to see if the JSON contains the specific Entity ID. | Use when searching for a specific record within a larger collection of JSON entries. |

## Key Features & Logic

### 1\. Object Overwriting

The `Deserialize` method uses `JsonUtility.FromJsonOverwrite`. This is a powerful Unity feature that takes an existing object in memory and fills its fields with the data found in the JSON string. This is much more memory-efficient than creating a brand-new object instance every time you load data.

### 2\. Flat vs. Structured

While the interface supports `ParseRawEntry` (splitting lines), this JSON strategy intentionally returns a single-item array. This is because a JSON "entry" is a self-contained object. It doesn't need to be split into columns because the keys (like `"name": "Value"`) act as the column identifiers.

### 3\. Identity Matching

The `IsMatch` method uses a formatted string check:

-   Logic: `rawEntry.Contains("\"_entityID\": \"" + data.EntityID + "\"")`
    
-   Why: Since we don't want to fully parse a massive JSON string just to check an ID, this string-matching approach provides a fast way to identify the correct data block before committing to a full `Deserialize` operation.
    

### 4\. Unity JsonUtility Limitations

Since this class uses Unity's native `JsonUtility`, it inherits certain constraints:

-   Fields only: It serializes `public` fields or `private` fields marked with `[SerializeField]`.
    
-   No Dictionaries: It cannot serialize `Dictionary<K, V>` types directly.
    
-   No Top-Level Arrays: The JSON string must represent a single object (enclosed in `{ }`).
    

> Note: Because this strategy uses `T : DataEntityBase<T>`, it ensures that the objects being saved are compatible with Unity's internal serialization system.





































