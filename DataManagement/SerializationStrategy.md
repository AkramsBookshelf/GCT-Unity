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
