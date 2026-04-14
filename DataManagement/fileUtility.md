# FileUtility Class

The `FileUtility` class is a static utility designed for the Unity engine to provide a centralized, safe, and consistent way to handle file paths and directory management. It abstracts the complexity of working with Unity-specific paths (like Persistent Data and StreamingAssets) and ensures that operations like saving, loading, and backing up files are performed with proper validation.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public static`
    
-   Core Purpose: Simplifies cross-platform path building, ensures directories exist before writing, and provides safety utilities like file backups and persistent data clearing.
  
## Method Reference Table

| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **GetSavePath** | `string fileName` | `string` | Returns the full path within `Application.persistentDataPath`. | Use for player save files, settings, or cached data. |
| **GetStreamingAssetPath** | `string fileName` | `string` | Returns the full path within `Application.streamingAssetsPath`. | Use for read-only data included with the build (e.g., config). |
| **GetPath** | `string fileName`, `DatabaseSaveLocation loc` | `string` | Builds a path based on the provided enum location. | Use to toggle between StreamingAssets and Persistent storage. |
| **GetRelativePath** | `string fileName`, `DatabaseSaveLocation loc` | `string` | Converts an absolute path into a Unity-relative path ("Assets/..."). | Use for paths compatible with Unity Editor APIs (AssetDatabase). |
| **GetAssetDirectoryPath** | `string assetDirectory` | `string` | Recursively checks/creates a directory inside the Assets folder. | Use in Editor tools to ensure folder structures exist before saving. |
| **FileExists** | `string filePath`, `bool logError` | `bool` | Checks if a file exists, with an optional Console error log. | Use as a safety check before attempting to read a file. |
| **ValidateDirectory** | `string directory` | `void` | Checks if a directory exists; if not, it creates it. | Call immediately before writing a file to prevent errors. |
| **GetLastModified** | `string filePath` | `DateTime` | Returns the last write time or `DateTime.MinValue` if missing. | Use for version checking or identifying the latest save. |
| **BackupFile** | `string filePath` | `void` | Creates a copy of the specified file with a `.bak` extension. | Use before overwriting critical data to prevent corruption loss. |
| **DeleteAllPersistentData** | *None* | `void` | Deletes and recreates the Persistent Data folder. | Use for "Reset Game" features or clearing data during testing. |
| **OpenDataFolder** | *None* | `void` | (Editor Only) Opens the OS file explorer to the persistent path. | Use via the Unity menu to quickly inspect saves in dev. |

## Key Features & Safety

### 1\. Unified Path Handling

By using `Path.Combine`, this class prevents issues with trailing slashes and ensures compatibility between different operating systems (Windows, Mac, Android, etc.).

### 2\. Editor Integration

The `GetAssetDirectoryPath` method uses `AssetDatabase` inside an `#if UNITY_EDITOR` block. This ensures that when you create folders within the Unity Editor, Unity recognizes them immediately and generates the necessary `.meta` files.

### 3\. Data Integrity

The `BackupFile` method provides a simple "Fail-Safe" mechanism. By calling this before saving, you ensure that if the computer crashes during a write operation, the user still has their previous save file (labeled `.bak`).

---
# CSVUtility Class

The `CSVUtility` class is a static utility designed to handle the complexities of Comma-Separated Values (CSV) data within Unity. It ensures that data remains clean and consistent by managing special characters (like commas and quotes), providing memory-efficient file writing, and offering robust parsing logic to turn raw text back into usable data fields.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public static`
    
-   Core Purpose: To provide safe encoding, decoding, and parsing of CSV data, preventing corruption when fields contain special characters or line breaks.
    

## Method Reference Table
| Method | Parameters | Return | Description | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **EncodeCsvField** | `string input` | `string` | Converts a string into a CSV-safe format by escaping quotes and wrapping in double quotes if needed. | Use when preparing a single data point to be added to a CSV row. |
| **DecodeCsvField** | `string input` | `string` | Removes surrounding quotes and restores escaped double-quotes to their original form. | Use when reading a field parsed from a CSV file to get the original "clean" text. |
| **WriteToCSVFile** | `string filePath`, `IEnumerable<string> lines`, `string header` | `void` | Automatically chooses between Buffered or Streaming write methods based on dataset size. | Use as the primary entry point for saving CSV data to disk. |
| **SplitCSVLine** | `string line` | `string[]` | Splits a single CSV string into an array of fields, correctly ignoring commas inside quoted sections. | Use when reading a CSV file line-by-line to extract individual data values. |
| **GetColumnIndexFromHeader** | `string rawHeader`, `string columnName` | `int` | Searches a header row for a specific column name and returns its index. | Use to find data columns by name rather than hard-coding index numbers (more flexible). |
| **CreateRow** | `params object[] fields` | `string` | Encodes multiple objects and joins them into a single comma-separated string. | Use to quickly generate a formatted CSV line from a list of variables or data points. |
| **IsValidFieldCount** | `string[] fields`, `int min`, `int? expected` | `bool` | Validates that a parsed row has the correct number of fields before processing. | Use as a safety check during loading to prevent "Index Out of Range" errors on malformed rows. |

## Key Features & Logic

### 1\. Smart Encoding/Decoding

Standard CSV format breaks if a user types a comma or a new line inside a field. This utility automatically detects these characters and "escapes" them.

-   Example: `Hello, World` becomes `"Hello, World"`
    
-   Example: `He said "Hi"` becomes `"He said ""Hi"""`
    

### 2\. Adaptive Writing (Streaming vs. Buffered)

Writing files can be intensive. This class uses a Threshold (5,000 rows) to decide how to save:

-   Buffered (`WriteBuffered`): For smaller files, it builds the entire string in memory and saves all at once. This is fast but uses more RAM.
    
-   Streaming (`WriteStreaming`): For large datasets, it writes to the disk line-by-line using a `StreamWriter`. This keeps memory usage low and prevents the app from stuttering.
    

### 3\. Context-Aware Parsing

Unlike a simple `string.Split(',')`, the `SplitCSVLine` method processes text character-by-character. This allows it to distinguish between a comma used as a separator and a comma used inside a quote (like an address or a description).

### 4\. Data Validation

The `IsValidFieldCount` method acts as a gatekeeper. If a row in your CSV is missing data or has too many columns, the utility logs a warning to the Unity Console, allowing you to catch data errors without crashing the game.




























