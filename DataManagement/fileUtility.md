# FileUtility Class

The `FileUtility` class is a static utility designed for the Unity engine to provide a centralized, safe, and consistent way to handle file paths and directory management. It abstracts the complexity of working with Unity-specific paths (like Persistent Data and StreamingAssets) and ensures that operations like saving, loading, and backing up files are performed with proper validation.

## Overview

-   Namespace: `CSG.DataManagement`
    
-   Access: `public static`
    
-   Core Purpose: Simplifies cross-platform path building, ensures directories exist before writing, and provides safety utilities like file backups and persistent data clearing.

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
