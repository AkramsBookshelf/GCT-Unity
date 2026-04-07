# **FileUtility Cheatsheet**

Namespace: `CSG.DataManagement`  
Purpose: Helper methods to work with file paths and directories in Unity.

## **1\. `GetFilePath(string fileName)`**

**What it does:**  
Combines the **StreamingAssets folder path** with a file name to give the full path.

**Example:**
```
string path \= FileUtility.GetFilePath("data.csv");
```
**Imagine:**

-   `Application.streamingAssetsPath = "C:/MyUnityProject/Assets/StreamingAssets"`

**Returns:**
```
"C:/MyUnityProject/Assets/StreamingAssets/data.csv"
```
**Use case:**  
Easily get the full path to a file in StreamingAssets without typing the whole path manually.

## **2\. `CheckAssetDirectory(string assetDirectory)`**

**What it does:**

-   Checks if a folder exists under **Assets**.
-   Creates any missing folders along the path.
-   Only works in **Unity Editor**.

**Example:**
```
string folderPath \= FileUtility.CheckAssetDirectory("Resources/Data/Collectables");
```
**What happens:**

-   If `Assets/Resources/Data/Collectables` doesn’t exist, Unity will create it.
-   Always returns the **full path**:

"Assets/Resources/Data/Collectables"

**Use case:**  
Ensures required folders exist before saving assets.

## **3\. `GetStreamingAssetPath(string fileName)`**

**What it does:**  
Same as `GetFilePath()`. Combines StreamingAssets folder with a file name.

**Example:**
```
string fullPath \= FileUtility.GetStreamingAssetPath("level1.json");
```
**Returns:**
```
"C:/MyUnityProject/Assets/StreamingAssets/level1.json"
```
**Use case:**  
Quick way to get a file path for reading/writing JSON, CSV, or other data files in StreamingAssets.

## **4\. `CreateFileWithHeader(string filePath, string header)`**

**What it does:**

-   Creates a new file at the specified path.
-   Writes the first line (header) to the file.
-   **Overwrites** the file if it already exists.

**Example:**
```
string filePath \= FileUtility.GetStreamingAssetPath("data.csv");  
FileUtility.CreateFileWithHeader(filePath, "Name,Age,Score");
```
**What happens:**

-   Creates `data.csv` in StreamingAssets.
-   First line of the file:

Name,Age,Score

**Use case:**  
Start a CSV file with a proper header before writing data.

## Summary Table
| Method                  | What it does                                                | Example Input                   | Example Output / Behavior                                           |
| ----------------------- | ----------------------------------------------------------- | ------------------------------- | ------------------------------------------------------------------- |
| `GetFilePath`           | Combines StreamingAssets path + file name                   | `"data.csv"`                    | `"C:/MyUnityProject/Assets/StreamingAssets/data.csv"`               |
| `CheckAssetDirectory`   | Ensures folder exists under Assets, creates missing folders | `"Resources/Data/Collectables"` | `"Assets/Resources/Data/Collectables"` (folders created if missing) |
| `GetStreamingAssetPath` | Combines StreamingAssets path + file name                   | `"level1.json"`                 | `"C:/MyUnityProject/Assets/StreamingAssets/level1.json"`            |
| `CreateFileWithHeader`  | Creates a new file and writes header line                   | filePath + `"Name,Age,Score"`   | Creates/overwrites file with first line: `Name,Age,Score`           |


# **Tips for Students / Teaching Notes**

1.  **StreamingAssets folder**:
    -   Unity keeps files here safe from builds stripping them out.
    -   Good for CSV, JSON, or any data files your game needs at runtime.
2.  **CheckAssetDirectory**:
    -   Helps avoid errors like “folder doesn’t exist” before saving assets.
    -   Only works in Editor, not at runtime on devices.
3.  **CreateFileWithHeader**:
    -   Always include the newline `\n` at the end so the next line of data can be written properly.
