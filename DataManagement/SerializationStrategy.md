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

# CsvStrategy<T> Class

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
