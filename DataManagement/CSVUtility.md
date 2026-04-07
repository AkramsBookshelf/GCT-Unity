### **1\. `EscapeForCSV(string input)`**

**What it does:**  
Prepares a string so it can safely go into a CSV file.

-   Wraps the string in quotes (`" "`).
-   Doubles any internal quotes (`"Hello"` → `"Hello""World"`).
-   Ensures commas don’t break CSV formatting.

**Example:**

Input

Output

`Hello`

`"Hello"`

`Hello, World`

`"Hello, World"`

`She said "Hi"`

`"She said ""Hi"""`

**Explanation:**  
CSV files use commas to separate values. If your text has commas or quotes, this method makes it safe for CSV.

### **2\. `UnescapeFromCSV(string input)`**

**What it does:**  
Takes a CSV-encoded string and returns the original string.

-   Removes the wrapping quotes.
-   Converts doubled quotes back to single quotes.

| Input               | Output          |
| ------------------- | --------------- |
| `"Hello"`           | `Hello`         |
| `"Hello, World"`    | `Hello, World`  |
| `"She said ""Hi"""` | `She said "Hi"` |


**Explanation:**  
This is the reverse of `EscapeForCSV()`. Useful when reading CSV data back into the program.

### **3\. `SplitCSVLine(string line)`**

**What it does:**  
Splits a line of CSV into an array of values, correctly handling quotes and commas inside quotes.

**Example CSV line:**
```
"Name","Age","Quote"  
"Akram","35","Hello, world"
```
**Usage:**
```csharp
string\[\] fields \= CSVUtility.SplitCSVLine("\\"Akram\\",\\"35\\",\\"Hello, world\\"");
```
**Returns:**
```
fields\[0\] \= "Akram"  
fields\[1\] \= "35"  
fields\[2\] \= "Hello, world"
```
**Explanation:**  
Without handling quotes, a CSV parser would think `"Hello, world"` is two separate values (`"Hello"` and `" world"`). This method avoids that mistake.

### **4\. `GetColumnIndexFromHeader(string headerLine, string columnName)`**

**What it does:**  
Finds the **position** of a column by its name in a CSV header line.

**Example CSV header:**
```
"Name","Age","Quote"
```
**Usage:**
```csharp
int index \= CSVUtility.GetColumnIndexFromHeader("\\"Name\\",\\"Age\\",\\"Quote\\"", "Quote");
```
**Returns:**

2

**Explanation:**  
CSV columns are like an array of values. This tells you which index a column is so you can read that column from each row.

### **5\. `IsValidFieldCount(string[] fields, int expectedCount)`**

**What it does:**  
Checks if a CSV row has the **right number of fields**.

**Example:**
```chsharp
string\[\] fields \= new string\[\] { "Akram", "35", "Hello, world" };  
bool valid \= CSVUtility.IsValidFieldCount(fields, 3);
```
**Returns:**

true

**Explanation:**  
If a row is missing a value or has extra values, this method can detect it. Good for validating CSV integrity.

### **6\. `WriteToCSVFile(string filePath, IEnumerable objects, string headerLine = null)`**

**What it does:**  
Writes a list of objects to a CSV file.

-   Optionally writes a header row first.
-   Converts each object to a CSV line.

**Example Objects:**
```csharp
var people \= new List<Person\>  
{  
    new Person { Name \= "Akram", Age \= 35, Quote \= "Hello, world" },  
    new Person { Name \= "Sam", Age \= 28, Quote \= "Hi there" }  
};  
CSVUtility.WriteToCSVFile("people.csv", people, "\\"Name\\",\\"Age\\",\\"Quote\\"");
```

**Output CSV File (`people.csv`):**
```
"Name","Age","Quote"  
"Akram","35","Hello, world"  
"Sam","28","Hi there"
```
**Explanation:**  
Easily save object data into a CSV file for Excel or other programs.

### **7\. `ObjectToCSVLine(object obj)`**

**What it does:**  
Converts a single object into a CSV line.

-   Uses the public properties of the object.
-   Escapes each value for CSV safety.

**Example Object:**

var person \= new Person { Name \= "Akram", Age \= 35, Quote \= "Hello, world" };  
string csvLine \= CSVUtility.ObjectToCSVLine(person);

**Returns:**
```
"Akram","35","Hello, world"
```
**Explanation:**  
This is a helper method used by `WriteToCSVFile()`. Converts object data into a CSV-ready string.

## Summary Table
| Method                     | What it does                           | Example Input                      | Example Output                  |
| -------------------------- | -------------------------------------- | ---------------------------------- | ------------------------------- |
| `EscapeForCSV`             | Makes a string safe for CSV            | `She said "Hi"`                    | `"She said ""Hi"""`             |
| `UnescapeFromCSV`          | Gets original string from CSV          | `"She said ""Hi"""`                | `She said "Hi"`                 |
| `SplitCSVLine`             | Splits a CSV line into fields          | `"Akram","35","Hello, world"`      | `["Akram","35","Hello, world"]` |
| `GetColumnIndexFromHeader` | Finds column index by name             | `"Name","Age","Quote"` , `"Quote"` | `2`                             |
| `IsValidFieldCount`        | Checks if field count matches expected | `["Akram","35","Hello"]`, 3        | `true`                          |
| `WriteToCSVFile`           | Writes objects to CSV file             | List of Person objects             | CSV file with header and rows   |
| `ObjectToCSVLine`          | Converts object to CSV line            | Person object                      | `"Akram","35","Hello, world"`   |
