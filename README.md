# AP Assignment 2
## Fleet Management with Collections and File Handling

**Author:** Nalin Gupta  
**Course:** CSE201: Advanced Programming

---

## Introduction

This project extends the Transportation Fleet Management System from Assignment 1 by incorporating the Java Collections Framework and enhanced file I/O capabilities. The system now demonstrates dynamic data management, efficient storage using multiple collection types, advanced sorting mechanisms, and robust persistence features.

---

## Compilation and Execution Instructions

### Prerequisites

- Java Development Kit (JDK) 8 or higher
- Command line terminal/command prompt
- Ensure `vehicles.csv` is in the `src` directory

### Compilation

Navigate to the `src` directory and execute:

```bash
javac *.java */*.java */*/*.java
```

This command compiles all Java files, including those in subdirectories.

### Execution

After successful compilation, run the application from the `src` directory:

```bash
java Main
```

Alternatively, you can also copy all the files in an IntelliJ IDEA project and run the `Main` class from there.

---

## Project Structure

The project follows a well-organized package structure:

```
src/
├── comparators/
│   ├── SortByEfficiencyComparator.java
│   ├── SortByIdComparator.java
│   ├── SortByMaxSpeedComparator.java
│   └── SortByModelComparator.java
├── exceptions/
│   ├── InsufficientFuelException.java
│   ├── InvalidOperationException.java
│   └── OverloadException.java
├── vehicles/
│   ├── interfaces/
│   │   ├── CargoCarrier.java
│   │   ├── FuelConsumable.java
│   │   ├── Maintainable.java
│   │   └── PassengerCarrier.java
│   ├── types/
│   │   ├── AirVehicle.java
│   │   ├── LandVehicle.java
│   │   └── WaterVehicle.java
│   ├── models/
│   │   ├── Airplane.java
│   │   ├── Bus.java
│   │   ├── Car.java
│   │   ├── CargoShip.java
│   │   └── Truck.java
│   └── Vehicle.java
├── FleetManager.java
└── Main.java
```

---

## Collections Framework Implementation

### Collection Types Used

The system demonstrates extensive use of the Java Collections Framework:

#### ArrayList<String>

**Purpose:** Primary storage for the vehicle fleet

**Justification:**
- Provides dynamic resizing capabilities
- Efficient random access for vehicle lookup by index
- Supports all CRUD operations (Create, Read, Update, Delete)
- Allows duplicate vehicle objects (though duplicates are prevented via HashSet)
- Maintains insertion order for predictable iteration

#### HashSet<String>

**Purpose:** Maintains unique vehicle IDs

**Justification:**
- O(1) average time complexity for add and contains operations
- Automatically prevents duplicate IDs
- Efficient for quick ID existence checks before adding vehicles
- No ordering required for ID validation

#### TreeSet<String>

**Purpose:** Stores distinct model names in sorted order

**Justification:**
- Automatically maintains alphabetical ordering of model names
- Ensures uniqueness of model names
- Efficient for generating sorted reports
- O(log n) time complexity for add and remove operations
- Natural ordering eliminates the need for external sorting

### Collection Operations Demonstrated

- **Dynamic Addition:** `fleet.add()`, `vehicleIDs.add()`, `modelNames.add()`
- **Removal:** `fleet.remove()`, `vehicleIDs.remove()`, `modelNames.remove()`
- **Iteration:** Enhanced for-loops to traverse all collections
- **Search:** `searchVehicleById()`, `searchByType()`
- **Uniqueness Checking:** HashSet's `add()` return value validation
- **Clearing:** `fleet.clear()` when loading from file

---

## Sorting and Ordering

### Comparator Implementation

The system implements four custom comparators for flexible sorting:

1. **SortByEfficiencyComparator:** Sorts vehicles by fuel efficiency (descending)
2. **SortByMaxSpeedComparator:** Sorts vehicles by maximum speed (descending)
3. **SortByModelComparator:** Sorts vehicles alphabetically by model name
4. **SortByIdComparator:** Sorts vehicles alphabetically by ID

### Sorting Methods

```java
// Using List.sort() with custom comparators
fleet.sort(new SortByEfficiencyComparator());
fleet.sort(new SortByMaxSpeedComparator());

// Using Collections utility methods
Vehicle fastest = Collections.max(fleet, 
                  new SortByMaxSpeedComparator());
Vehicle slowest = Collections.min(fleet, 
                  new SortByMaxSpeedComparator());
```

### Automatic Ordering

Model names are automatically maintained in alphabetical order through the TreeSet data structure, eliminating the need for explicit sorting operations.

---

## File I/O and Persistence

### CSV File Format Specifications

The persistence system operates with a predefined CSV format for each vehicle type. The system expects and generates CSV files according to the following schemas:

| Vehicle Type | CSV Format |
|--------------|------------|
| **Car** | `Car,ID,Model,MaxSpeed,CurrentMileage,PassengerCapacity,CurrentPassengers,FuelLevel` |
| **Bus** | `Bus,ID,Model,MaxSpeed,CurrentMileage,PassengerCapacity,CurrentPassengers,CargoCapacity,CurrentCargo,FuelLevel` |
| **Truck** | `Truck,ID,Model,MaxSpeed,CurrentMileage,CargoCapacity,CurrentCargo,FuelLevel` |
| **Airplane** | `Airplane,ID,Model,MaxSpeed,CurrentMileage,MaxAltitude,PassengerCapacity,CurrentPassengers,CargoCapacity,CurrentCargo,FuelLevel` |
| **CargoShip (Fuel)** | `CargoShip,ID,Model,MaxSpeed,CurrentMileage,hasSail,CargoCapacity,CurrentCargo,FuelLevel` *(for fuel-powered ships)* |
| **CargoShip (Sail)** | `CargoShip,ID,Model,MaxSpeed,CurrentMileage,hasSail,CargoCapacity,CurrentCargo` *(for sail-powered ships)* |

### Saving Fleet

When the user selects **Menu Option 7**, the current fleet of vehicles is saved to a CSV file using the persistence mechanism. However, when the user terminates the program, the user is prompted to save the fleet of vehicles to a file, in order to prevent accidental loss of data. If the user has already saved the data, they can simply enter **N**, when prompted at the end of the program.

**Method:** `saveToFile(String filename)`

**Implementation:**
- Uses `PrintWriter` for writing data
- Automatically creates a file if it doesn't exist
- Writes each vehicle's data in CSV format
- Uses `try-with-resources` for automatic resource management
- Handles `IOException` gracefully with user-friendly messages

### Load Operation

When the program starts, it automatically loads sample vehicle data from `vehicles.csv`. However, the user can load their own vehicle data by choosing **Menu Option 8**, and entering the file name.

**Method:** `loadFromFile(String filename)`

**Implementation:**
- Uses `BufferedReader` for efficient file reading
- Clears existing fleet before loading
- Parses each line using the `createVehicle()` factory method
- Validates data format and reports errors with line numbers
- Continues processing even if individual lines fail
- Uses `try-with-resources` for automatic file closure
- Comprehensive error reporting for malformed data

### Capabilities

The system implements extensive error detection and handling capabilities across both CSV file processing and CLI operations, ensuring data integrity and user-friendly error reporting.

#### CSV File Validation and Error Detection

The CSV parsing system incorporates sophisticated validation mechanisms that can detect and handle various types of malformed data:

- **Data Type Validation**: The system validates that numeric fields (Double/Integer) contain valid numerical values. Any string values in numeric fields are detected and reported with the specific line number where the error occurred.

- **Capacity Constraint Validation**: The parser verifies that vehicle capacities match predefined default values:
  - Car passenger capacity: 5
  - Bus passenger capacity: 50, cargo capacity: 500 kg
  - Truck cargo capacity: 5000 kg
  - Airplane passenger capacity: 200, cargo capacity: 10000 kg
  - CargoShip cargo capacity: 50000 kg
  
  Any deviation from these defaults is flagged as a data integrity violation.

- **Boolean Field Validation**: For fields requiring boolean values (such as `hasSail` in CargoShip), the system strictly validates that only `true` or `false` values are accepted. Any other values are treated as malformed data.

- **Field Count Validation**: The parser verifies that each CSV line contains the exact number of fields required for the specified vehicle type. Both insufficient and excessive field counts are detected and reported.

- **Vehicle Type Validation**: The system validates that the vehicle type specified in the first field corresponds to a recognized vehicle class (Car, Bus, Truck, Airplane, CargoShip). Invalid vehicle types are rejected with appropriate error messages.

#### Error Reporting and Recovery for CSV File

When malformed data is encountered, the system:
- Logs specific error messages indicating the type of validation failure
- Reports the exact line number where the error occurred
- Continues processing subsequent lines rather than terminating the entire operation
- Provides a comprehensive summary of all errors encountered during the parsing process

#### CLI Input Validation

The command-line interface incorporates robust input validation including:
- Numeric input validation with retry mechanisms for invalid entries
- Menu option validation with clear error messaging
- File operation error handling with user-friendly feedback
- Exception propagation and handling for all fleet operations

---

## Demo Tests

This subsection presents demonstration test cases executed to validate the robust error handling capabilities of the Transportation Fleet Management System. Due to the comprehensive input validation mechanisms implemented, numerous test cases and scenarios can be created to verify system behavior across different error conditions.

### CSV File Error Handling Tests

The following test cases demonstrate the system's ability to detect and handle malformed CSV data:

| Test Case | Input Data | Error Type | Expected Output |
|-----------|------------|------------|-----------------|
| **01** | `Car,C001,Toyota,ABC,1500.00,5,2,45.50` | Data Type Validation | "Incorrect Format for CSV at line: X" |
| **02** | `Bus,B001,Mercedes,120.00,2000.00,60,10,500.00,200.00,80.00` | Capacity Constraint | "Defaults were changed at line: X" |
| **03** | `CargoShip,CS001,Maersk,25.00,5000.00,maybe,50000.00,30000.00` | Boolean Validation | "Incorrect Format for CSV at line: X" |
| **04** | `Car,C002,Honda,150.00` | Field Count | "Incorrect Format for CSV at line: X" |
| **05** | `Motorcycle,M001,Harley,180.00,100.00,2,1,40.00` | Invalid Vehicle Type | "Vehicle Type not found at line: X" |

If the user tries to `load` a CSV File (Using Option 8 in the CLI) with any of the above test cases, the code will display the expected output (All the lines where errors were found) on the `Terminal` and create `Vehicle` objects for all valid CSV lines.

**Example to demonstrate the same:** Suppose we have a CSV File with the following input:

```csv
Car,C001,Toyota,220.00,15000.00,5,2,45.50
Truck,T001,Scania,60.0,100.00,6000.00,2000.0,20.00
CargoShip,CS001,Galaxy,42.60,250000.00,false,50000.00,35000.00,500000.00
```

In the above example, when the user `loads` the CSV File (Using Option 8 in CLI), only the `Car` and `CargoShip` Objects are created. The `Truck` Object has Capacity constraint error, and is thus excluded from Object Creation. The terminal displays the message: `Defaults were changed at line: 2`

### CLI Input Validation Tests

| Test ID | Input Scenario | Error Type | Expected Behavior |
|---------|----------------|------------|-------------------|
| **01** | User enters "ABC" for distance | Numeric Input | Displays "Please enter a number value" and prompts retry |
| **02** | Create Car with 10 passengers | Overload Exception | Shows exception message, returns to menu |
| **03** | Enter "xyz" for boolean field | Boolean Input | Prompts "Please enter True/False value" |
| **04** | Create vehicle with existing ID | Duplicate ID | Shows "The vehicle with same ID already exists." |

---

## CLI Features

The command-line interface provides 25 operations organized into categories:

### Fleet Management
- Add Vehicle (Option 1)
- Remove Vehicle (Option 2)
- Search by Type (Option 9)
- Get Vehicle Details by ID (Option 16)

### Operations
- Start Journey (Option 3)
- Refuel All (Option 4)
- Perform Maintenance (Option 5)
- Board/Disembark Passengers (Options 11, 13)
- Load/Unload Cargo (Options 12, 14)
- Estimate Journey Time (Option 15)

### Sorting and Analysis
- Sort by Efficiency (Option 17)
- Sort by Max Speed (Option 18)
- Sort by Models (Option 19)
- Sort by IDs (Option 20)
- Get Fastest/Slowest Vehicle (Options 21, 22)

### Reporting
- Generate Extensive Report (Option 6)
- Generate Simple Summary (Option 23)
- Get Distinct Model Names (Option 24)
- List Vehicles Needing Maintenance (Option 10)

### Persistence
- Save Fleet (Option 7)
- Load Fleet (Option 8)
- Auto-save prompt on exit

---

## Working Test Scenario: Complete Fleet Management Workflow

This subsection demonstrates the program's features by showcasing new features like **advanced sorting**, **data analysis**, **persistence features**, and **summary**.

### Starting the Application

Upon execution, the system automatically loads pre-existing fleet data from `vehicles.csv`:

```
Fleet loaded successfully from: vehicles.csv
Available Options:
0. Print Menu Options
1. Add Vehicle
2. Remove Vehicle
...
24. Exit
```

### Advanced Sorting

The system provides four different sorting options demonstrating flexible data organization:

#### Sort by Efficiency
```
> 17
Fleet sorted by efficiency successfully!
```

#### Sort by Max Speed
```
> 18
Fleet sorted by max speed successfully!
```

#### Sort by Model Names (Alphabetically)
```
> 19
Fleet sorted by models successfully!
```

#### Sort by IDs (Alphabetically)
```
> 20
Fleet sorted by IDs successfully!
```

### Data Analysis

The system utilizes `Collections.max()` and `Collections.min()` for efficient data analysis:

#### Get Fastest Vehicle
```
> 21
Vehicle ID: V013
Vehicle Model: Boeing 737
Vehicle Max Speed: 850.0
Vehicle Current Mileage: 120000.0
```

#### Get Slowest Vehicle
```
> 22
Vehicle ID: V020
Vehicle Model: HMM Algeciras
Vehicle Max Speed: 47.0
Vehicle Current Mileage: 460000.0
```

### Persistence Features

Demonstrating robust save and load functionality:

#### Save Fleet to File
```
> 7
Please enter the file name: 
> updated_fleet.csv
Created file: updated_fleet.csv
Successfully added fleet to file: updated_fleet.csv
```

#### Load Fleet from File
```
> 8
Please enter the file name: 
> updated_fleet.csv
Fleet loaded successfully from: updated_fleet.csv
```

### Summary Generation

The new simple summary feature provides quick fleet overview:

```
> 23
Total Vehicles: 20
Distinct model names: Airbus A320
Ashok Leyland Lynx
Ashok Leyland U3718
Boeing 737
...
Volvo 9700
Volvo FH16
Fastest Vehicle: Boeing 737 (ID: V013)
Slowest Vehicle: HMM Algeciras (ID: V020)
```

---

## Key Enhancements from Assignment 1

1. **Dynamic Data Structures:** Replaced any fixed-size arrays with ArrayList
2. **Duplicate Prevention:** HashSet ensures unique vehicle IDs
3. **Automatic Ordering:** TreeSet maintains sorted model names
4. **Flexible Sorting:** Multiple comparators for different sorting criteria
5. **Enhanced Reporting:** Fastest/slowest vehicle identification using Collections utilities
6. **Improved CLI:** Expanded to 25 operations with better input validation
7. **Robust Persistence:** Enhanced error handling and validation during file operations
8. **Auto-load Feature:** Automatically loads fleet data on startup
9. **Save Prompting:** Asks user to save before exit

---

## Constraints

The following constraints and known limitations were identified during the development and testing of the Fleet Management System. Users and testers should be aware of these points to ensure the program is used as intended.

- **CSV Format Dependency:** When loading data from a file, the CSV must strictly adhere to the format specified in Section 5.1.2 of the documentation to ensure successful parsing.

- **Manual Data Saving:** The program does not automatically save the current state of the vehicle fleet upon exit. However, users are prompted to save the fleet upon termination of the program. If the user selects **N**, then the changes are lost permanently. Therefore it is recommended to save the data by entering **Y**, and then entering the **file name**.

- **Default Fuel Level:** All vehicles created interactively through the CLI are initialized with a fuel level of zero. It is recommended to refuel these vehicles using `Menu Option 4 (Refuel All)` before attempting to start a journey.

- **In-Console Reporting:** The system's reporting feature, accessed via `Menu Option 6` OR `Menu Option 23`, displays the generated report directly in the console. The program does not support exporting or saving this report to an external file.

---

## References

The following references were used during the development of the project:

- Lecture Slides
- [ArrayLists](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html)
- [HashSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/HashSet.html)
- [TreeSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html)
- [Comparator](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Comparator.html)
- [Custom Exceptions](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Exception.html)
- [Regular Expressions](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/regex/Pattern.html)
- [Enhanced Switch Expression](https://docs.oracle.com/en/java/javase/17/language/switch-expressions.html)
- [InputMismatchException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/InputMismatchException.html)
- [Scanner](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Scanner.html)
- [String Methods](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html)
- [String Builder](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)
- [Collections](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collections.html)
- [Comparable](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Comparable.html)
