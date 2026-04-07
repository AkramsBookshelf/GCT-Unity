d


| Class / Interface | Description | How It Works | Associated With |
|------------------|-------------|--------------|----------------|
| ISerializableToCSV | An interface that defines the contract for any data object that needs to be saved to or loaded from a CSV file. | It requires implementing classes to provide methods for getting a unique ID, generating a CSV header, converting the object to a CSV line, and parsing CSV fields back into object properties. | AbstractDataEntity, DataLoader<T>, DataSaver<T> |
| AbstractDataEntity | A base class for data objects (ScriptableObjects) that can be serialized to CSV. | It implements ISerializableToCSV, providing automatic unique ID generation (e.g., Category_Name_001) and standard logic for handling entity names and save status. | ISerializableToCSV, CSVUtility, FileUtility |
| CSVUtility | A static utility class for formatting and parsing CSV data. | It contains logic to safely "escape" strings (handling commas and quotes), "unescape" them, and split full CSV lines into individual data fields accurately. | AbstractDataEntity, DataLoader<T>, DataSaver<T> |
| FileUtility | A static utility class for managing file paths and directory structures within Unity. | It provides methods to get paths for StreamingAssets, create files with headers, and ensure specific folders exist within the Unity project's Assets directory. | DataLoader<T>, DataSaver<T>, AbstractDataEntity |
| DataLoader<T> | A generic class responsible for reading CSV files and converting them into ScriptableObject assets. | It reads a CSV file from StreamingAssets, uses CSVUtility to split lines, and then instantiates and saves new ScriptableObject assets into the project folder. | ISerializableToCSV, CSVUtility, FileUtility |
| DataSaver<T> | A generic class responsible for saving ScriptableObject data back into a CSV file. | It takes an object, converts it to a CSV string using the object's ToCSVLine method, and either updates an existing row in the CSV (based on ID) or appends a new one. | ISerializableToCSV, CSVUtility, FileUtility |
