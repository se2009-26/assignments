# SE2009: Data Structures – Lab 04: Advanced Struct Operations, Nested Structs & Struct Arrays

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Lab Session:** Lab 04 – Struct Manipulation, Nested Structs & Struct Arrays
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi)**  
  *(All lab reports must be submitted directly via the University DYS platform.)*
* **Submission Deliverable:** Microsoft Word document (`.docx`) with your accessible OneDrive sharing link included at the top.
* **File Naming Convention:** `studentNo_surname_name_SE2009_Lab04.docx`
* **Difficulty Level:** Intermediate (Nested Structures, Const Pointers, Array of Structs)

> [!IMPORTANT]
> **Submission & Delivery Policy:**  
> This laboratory assignment is announced on GitHub for guidelines and starter code. You must compile and test all tasks on your machine, take required terminal output screenshots, and submit your final Microsoft Word report (`.docx`) to the **DYS (Ders Yönetim Sistemi)** portal. Make sure your OneDrive share link is active and readable. **Do not push this file to GitHub directly.**

---

# 👨‍🏫 PART 1: Instructor-Led Core Application

This section will be analyzed on the whiteboard and live-coded on screen by the instructor at the beginning of the lab.

```text
===================================================================================
                   NESTED STRUCT & FUNCTION ENCAPSULATION MODEL
===================================================================================
[ BankAccount (acc1) ]
+---------------------+-------------------+------------------+--------------------+
| account_number      | owner_name        | balance          | open_date (Date)   |
| (int: 4 bytes)      | (char[32]: 32 B)  | (double: 8 bytes)| day | month | year |
+---------------------+-------------------+------------------+-----+-------+------+
Offset: 0             4                   40                 48    52      56

Pointer Access (Pass-by-reference):
acc_ptr -----------------------------------------------------> [ Base Address &acc1 ]
  acc_ptr->balance           => modifies field directly in memory
  acc_ptr->open_date.year    => accesses nested struct member via arrow + dot
===================================================================================
```

---

### 📌 Application 1: Nested Structs, Encapsulation & Safe Pointer Operations

#### 💡 Concept Breakdown & Memory Mechanics
1. **Hierarchical / Nested Structs:** Real-world entities contain sub-components (e.g., an account has an opening date, an employee has an address). Structs can contain instances of other structs as their members.
2. **Access Syntax:**
   * Direct variable: `acc.open_date.year`
   * Pointer to parent struct: `acc_ptr->open_date.year`
3. **Pass-by-Reference for Mutation:** When mutating a struct's state, pass a pointer (`BankAccount *acc`) to avoid copying memory and ensure changes persist outside the function.
4. **`const` Pointer for Safe Read-Only Access:** When displaying or reading struct fields, use `const BankAccount *acc`. This avoids duplicating data on the Stack while ensuring the compiler prevents accidental modifications.

#### 💻 Application 1 Source Code:
```c
#include <stdio.h>
#include <string.h>

// 1. Sub-struct: Date representation
typedef struct {
    int day;
    int month;
    int year;
} Date;

// 2. Main struct: BankAccount containing a nested Date struct
typedef struct {
    int account_number;
    char owner_name[32];
    double balance;
    Date open_date;
} BankAccount;

// Helper function 1: Pass-by-reference to mutate balance directly
void deposit(BankAccount *acc, double amount) {
    if (acc == NULL || amount <= 0) return;
    acc->balance += amount;
    printf("[Deposit] Successfully added $%.2f to Account #%d\n", amount, acc->account_number);
}

// Helper function 2: Read-only access using const pointer (Fast & Memory Safe)
void printAccount(const BankAccount *acc) {
    if (acc == NULL) return;
    printf("========================================\n");
    printf("Account No : #%d\n", acc->account_number);
    printf("Owner      : %s\n", acc->owner_name);
    printf("Balance    : $%.2f\n", acc->balance);
    printf("Open Date  : %02d/%02d/%04d\n", 
           acc->open_date.day, acc->open_date.month, acc->open_date.year);
    printf("========================================\n");
}

int main(void) {
    printf("=== Application 1: Nested Structs & Function Operations ===\n\n");

    // Initialize nested struct using designated initializers
    BankAccount acc1 = {
        .account_number = 10042,
        .owner_name = "Alice Smith",
        .balance = 1250.50,
        .open_date = {15, 4, 2024}
    };

    printf("Initial Account State:\n");
    printAccount(&acc1);

    // Modify state through pointer:
    deposit(&acc1, 350.00);

    printf("\nUpdated Account State:\n");
    printAccount(&acc1);

    return 0;
}
```

---

# 💻 PART 2: Student Hands-on Lab Tasks & Questions

Answer all three tasks in your Microsoft Word report and submit via **DYS (Ders Yönetim Sistemi)**.

---

### Task 1: Nested Structs & Geometric Computation (Point & Rectangle)
Consider a 2D graphics coordinate system modeling rectangles using nested point structures.

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int x;
    int y;
} Point;

typedef struct {
    Point topLeft;
    Point bottomRight;
} Rectangle;

// Function prototypes to implement:
int calculateArea(const Rectangle *rect);
void moveRectangle(Rectangle *rect, int deltaX, int deltaY);
```

1. **Memory Layout & Operator Analysis:**
   * Explain how `rect->topLeft.x` operates: why is the arrow operator (`->`) used first, followed by the dot operator (`.`)?
   * If `sizeof(int) == 4`, what is `sizeof(Rectangle)` on a 64-bit machine? Does any padding occur between `topLeft` and `bottomRight`? Explain why or why not.
2. **Function Implementation:**
   * Implement `calculateArea(const Rectangle *rect)`: calculate and return $\text{width} \times \text{height}$ where $\text{width} = |\text{bottomRight.x} - \text{topLeft.x}|$ and $\text{height} = |\text{bottomRight.y} - \text{topLeft.y}|$.
   * Implement `moveRectangle(Rectangle *rect, int deltaX, int deltaY)`: shift both `topLeft` and `bottomRight` coordinates by `deltaX` and `deltaY`.
3. Provide your complete compilable C code and a terminal screenshot verifying area calculation and coordinate translation.

---

### Task 2: Pass-by-Value vs. `const` Pointer & Memory Stack Frame Analysis
A telemetry software team monitors autonomous vehicle states using the struct below:

```c
#include <stdio.h>

typedef struct {
    int vehicle_id;
    char model[32];
    double fuel_level;
    double max_tank_capacity;
} Vehicle;

// Case A: Faulty refuel function
void refuelFaulty(Vehicle v, double amount) {
    v.fuel_level += amount;
}

// Case B: Correct refuel function
void refuelCorrect(Vehicle *v, double amount) {
    // TODO: Implement using pointer
}

// Case C: Display vehicle
void inspectVehicle(const Vehicle *v) {
    // TODO: Implement read-only display
}

int main(void) {
    Vehicle car = {201, "CyberTruck", 15.0, 80.0};

    refuelFaulty(car, 30.0);
    printf("After refuelFaulty:  Fuel = %.1f L\n", car.fuel_level);

    refuelCorrect(&car, 30.0);
    printf("After refuelCorrect: Fuel = %.1f L\n", car.fuel_level);

    inspectVehicle(&car);
    return 0;
}
```

1. **Stack Memory Analysis:**
   * Draw or describe the stack frame during the execution of `refuelFaulty(car, 30.0)`. Why does `car.fuel_level` remain $15.0\text{ L}$ in `main()` after `refuelFaulty` returns?
2. **`const` Keyword & Performance:**
   * Why is `void inspectVehicle(const Vehicle *v)` preferred over `void inspectVehicle(Vehicle v)` in terms of CPU cycles and memory footprint?
   * What does the `const` qualifier enforce? What would happen if you wrote `v->fuel_level = 0;` inside `inspectVehicle`?
3. Complete the implementations of `refuelCorrect` (ensuring fuel does not exceed `max_tank_capacity`) and `inspectVehicle`. Paste your code and execution screenshot.

---

### Task 3: Array of Structs & Search Algorithm (Inventory Registry)
An electronics lab manages its component storage using an array of structures:

```c
typedef struct {
    int item_id;
    char name[24];
    double unit_price;
    int quantity;
} InventoryItem;
```

Write a complete, modular C program that satisfies the following:
1. Initializes an array of at least 4 `InventoryItem` records with realistic values (e.g., Resistors, Capacitors, Microcontrollers).
2. Implements `double calculateTotalInventoryValue(const InventoryItem items[], int size)`:
   $$\text{Total Value} = \sum_{i=0}^{\text{size}-1} (\text{unit\_price}_i \times \text{quantity}_i)$$
3. Implements `int findMostValuableStockItem(const InventoryItem items[], int size)`: returns the array index of the item with the highest total financial value ($\text{unit\_price} \times \text{quantity}$).
4. Implements `void printInventoryReport(const InventoryItem items[], int size)`: prints a clean, formatted ASCII table listing each item, its subtotal value, the overall inventory total, and highlights the most valuable item.
5. **Pointer Arithmetic Question:** Explain how the compiler resolves `items[i].unit_price` under the hood using pointer arithmetic (show the equivalent expression using `*` and `+`).
6. Provide your complete, clean C code and a terminal screenshot of the program output.

---

## 📄 DYS Submission Template

Copy and paste the template below into your Microsoft Word document (`.docx`), write your answers and paste your code/screenshots, and upload it to **DYS (Ders Yönetim Sistemi)**:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                             FACULTY OF ENGINEERING
                        DEPARTMENT OF SOFTWARE ENGINEERING
                       SE2009 - LAB 04 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Submission Date: [DD/MM/YYYY]
• OneDrive View/Download Link: [Paste Your Accessible OneDrive Sharing Link Here]

--------------------------------------------------------------------------------
TASK 1: NESTED STRUCTS & GEOMETRIC COMPUTATION
Your Answer & Code:
(Explain rect->topLeft.x syntax, sizeof(Rectangle) calculation and padding analysis.
Paste your calculateArea() and moveRectangle() code with output screenshot.)


TASK 2: PASS-BY-VALUE VS. CONST POINTER ANALYSIS
Your Answer & Explanation:
(Explain stack copying in refuelFaulty, describe CPU/RAM benefits of const pointer,
and paste completed refuelCorrect() and inspectVehicle() code with output screenshot.)


TASK 3: ARRAY OF STRUCTS & INVENTORY REGISTRY
Your Answer & Source Code:
(Paste your full C program implementing total valuation, highest stock item search,
and formatted table report. Explain the pointer arithmetic behind items[i].)

================================================================================
```
