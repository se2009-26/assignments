# SE2009: Data Structures – Lab 03: Custom Data Types with `struct`, Memory Alignment & Dynamic Struct Allocation

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Lab Session:** Lab 03 – User-Defined Heterogeneous Data Types, Struct Memory Architecture & Dynamic Allocations
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi)**  
  *(All lab reports must be submitted directly via the University DYS platform.)*
* **Submission Deliverable:** Microsoft Word document (`.docx`) with your accessible OneDrive sharing link included at the top.
* **File Naming Convention:** `studentNo_surname_name_SE2009_Lab03.docx`
* **Difficulty Level:** Intermediate (Memory Alignment, Struct Pointers, Dynamic Heap Records)

> [!IMPORTANT]
> **Submission & Delivery Policy:**  
> This laboratory assignment is announced on GitHub for guidelines and starter code. You must compile and test all tasks on your machine, take required terminal output screenshots, and submit your final Microsoft Word report (`.docx`) to the **DYS (Ders Yönetim Sistemi)** portal. Make sure your OneDrive share link is active and readable. **Do not push this file to GitHub directly.**

---

# 👨‍🏫 PART 1: Instructor-Led Core Applications

This section will be analyzed on the whiteboard and live-coded on screen by the instructor at the beginning of the lab.

```text
===================================================================================
                       STRUCT MEMORY MODEL & ALIGNMENT
===================================================================================
Case A: Unaligned Struct (12 Bytes due to Padding Holes)
Address:    0x00      0x01  0x02  0x03      0x04      0x05  0x06  0x07      0x08      0x09  0x0A  0x0B
Content:  [char c]  [--- PADDING ---]   [--------- int id ---------]    [char tag][--- PADDING ---]
Size:      1 byte        3 bytes                   4 bytes               1 byte        3 bytes

Case B: Optimized Order Struct (8 Bytes - Minimized Padding)
Address:    0x00      0x01  0x02  0x03      0x04      0x05      0x06  0x07
Content:  [--------- int id ---------]   [char c]  [char tag]  [-- PADDING --]
Size:                  4 bytes            1 byte     1 byte        2 bytes
===================================================================================
```

---

### 📌 Application 1: Struct Declaration, `typedef`, and Memory Padding Analysis

#### 💡 Concept Breakdown & Memory Mechanics
1. **Heterogeneous Data Aggregation:** Unlike standard C arrays (which can only store homogeneous data of the identical type), a `struct` packages multiple variables of different data types into a single contiguous memory block.
2. **The `typedef` Advantage:** Eliminates the verbose `struct SensorNode node1;` syntax, allowing clean type definitions such as `SensorNode node1;`.
3. **Memory Padding & Alignment:** Modern 32-bit and 64-bit CPUs fetch words from memory addresses that are multiples of 4 or 8 bytes. To prevent misaligned memory access, compilers inject invisible **padding bytes** between members. Struct member ordering directly affects the RAM footprint!

#### 💻 Application 1 Source Code:
```c
#include <stdio.h>
#include <string.h>

// Struct with suboptimal field ordering (Causes wasted padding memory)
struct BadSensor {
    char status;     // 1 byte + 3 bytes padding
    int sensor_id;   // 4 bytes
    char flag;       // 1 byte + 3 bytes padding
}; // Total: 12 bytes

// Optimized struct field ordering (Largest data types first)
typedef struct {
    int sensor_id;   // 4 bytes
    char status;     // 1 byte
    char flag;       // 1 byte
                     // + 2 bytes trailing padding
} OptimizedSensor;   // Total: 8 bytes

int main(void) {
    printf("=== Application 1: Struct Memory Padding & Alignment ===\n");
    printf("Size of struct BadSensor:       %zu bytes\n", sizeof(struct BadSensor));
    printf("Size of struct OptimizedSensor: %zu bytes\n", sizeof(OptimizedSensor));

    OptimizedSensor s1;
    s1.sensor_id = 101;
    s1.status = 'A';
    s1.flag = 'X';

    printf("\nSensor Initialized:\n");
    printf("ID: %d | Status: %c | Flag: %c\n", s1.sensor_id, s1.status, s1.flag);
    printf("Base Address (&s1):            %p\n", (void*)&s1);
    printf("Member Address (&s1.sensor_id): %p (offset 0)\n", (void*)&s1.sensor_id);
    printf("Member Address (&s1.status):    %p (offset 4)\n", (void*)&s1.status);
    printf("Member Address (&s1.flag):      %p (offset 5)\n", (void*)&s1.flag);

    return 0;
}
```

---

### 📌 Application 2: Struct Pointers, Arrow Operator (`->`), and Dynamic Heap Allocation

#### 💡 Concept Breakdown & Memory Mechanics
1. **Pass-by-Value vs. Pass-by-Reference:** Passing a raw struct to a function copies every byte across the stack frame. Passing a pointer (`SensorNode*`) copies only **8 bytes** (the address) and allows in-place modifications.
2. **Dot (`.`) vs. Arrow (`->`) Operator:**
   * When accessing through a direct variable: `sensor.id`
   * When accessing through a pointer: `(*sensor_ptr).id` $\iff$ `sensor_ptr->id`
3. **Dynamic Structs on the Heap:** Allocated via `malloc(sizeof(StructName))` and released with `free()`.

```text
===================================================================================
                   HEAP STRUCT & POINTER ACCESS (ARROW OPERATOR)
===================================================================================
[ STACK FRAME (main) ]                                  [ HEAP MEMORY BLOCK ]
+----------------------------+                          +-------------------------------+
| SensorNode *s_ptr = 0x8500 | -----------------------> | 0x8500: sensor_id = 505       |
+----------------------------+                          | 0x8504: reading   = 24.85     |
                                                        | 0x850C: active    = 1         |
                                                        +-------------------------------+
                                                              s_ptr->reading
===================================================================================
```

#### 💻 Application 2 Source Code:
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    int id;
    char model[32];
    double temperature;
} IoTDevice;

// Updates the struct directly via pointer (Pass-by-reference)
void calibrateDevice(IoTDevice *dev, double offset) {
    if (dev == NULL) return;
    dev->temperature += offset; // Identical to: (*dev).temperature += offset;
}

int main(void) {
    printf("=== Application 2: Dynamic Struct & Arrow Operator ===\n");

    // 1. Dynamic allocation of a struct on the Heap:
    IoTDevice *device = (IoTDevice*)malloc(sizeof(IoTDevice));
    if (device == NULL) {
        perror("Failed to allocate memory for IoTDevice!");
        return EXIT_FAILURE;
    }

    // 2. Initialize values via arrow operator:
    device->id = 7001;
    strncpy(device->model, "DHT22-Thermal", sizeof(device->model) - 1);
    device->temperature = 23.40;

    printf("Before Calibration: Device #%d [%s] Temp: %.2f C\n",
           device->id, device->model, device->temperature);

    // 3. Pass pointer to calibration function:
    calibrateDevice(device, 1.85);

    printf("After Calibration:  Device #%d [%s] Temp: %.2f C\n",
           device->id, device->model, device->temperature);

    // 4. Free Heap memory to avoid leaks:
    free(device);
    device = NULL;
    printf("Device memory freed and pointer set to NULL.\n");

    return 0;
}
```

---

# 💻 PART 2: Student Hands-on Lab Tasks & Questions

Answer all four tasks in your Microsoft Word report and submit via **DYS (Ders Yönetim Sistemi)**.

---

### Task 1: Struct Padding & Memory Alignment Investigation
Analyze the two struct declarations below:

```c
struct SchemaA {
    char a;
    double b;
    char c;
};

struct SchemaB {
    double b;
    char a;
    char c;
};
```

1. Predict the total byte size (`sizeof`) for both `struct SchemaA` and `struct SchemaB` on a 64-bit architecture.
2. Explain step-by-step why the sizes differ even though both structs store identical member types. Draw or describe the byte-by-byte memory layout and identify where the padding bytes are placed.
3. Write a small C verification program, compile it, and include your terminal execution screenshot in your report.

---

### Task 2: Dot (`.`) vs. Arrow (`->`) Operator & Pass-by-Reference
A junior developer writes the following program to increase a player's level and health:

```c
#include <stdio.h>

typedef struct {
    char nickname[20];
    int level;
    int health;
} Player;

// Faulty level-up:
void levelUpFaulty(Player p) {
    p.level++;
    p.health += 50;
}

// Function to fix:
void levelUpCorrect(Player *p) {
    // TODO: Implement using pointer and arrow operator
}

int main(void) {
    Player warrior = {"ShadowKnight", 1, 100};

    levelUpFaulty(warrior);
    printf("After levelUpFaulty:  Lvl: %d, HP: %d\n", warrior.level, warrior.health);

    levelUpCorrect(&warrior);
    printf("After levelUpCorrect: Lvl: %d, HP: %d\n", warrior.level, warrior.health);

    return 0;
}
```

1. Explain in detail why `levelUpFaulty` fails to modify the `warrior` variable in `main()` (discuss stack memory copying).
2. Complete the implementation of `levelUpCorrect(Player *p)` using the arrow operator `->`.
3. Provide the full compilable code, execution output, and explain the equivalence of `p->level` and `(*p).level`.

---

### Task 3: Dynamic Struct Array (Student / Record Registry) on the Heap
Write a complete, robust C program that:

1. Defines a `typedef struct` named `StudentGrade`:
   * `int student_id`
   * `char name[30]`
   * `float midterm_score`
   * `float final_score`
2. Prompts the user to enter the number of students $N$ (e.g., $N = 3$).
3. Dynamically allocates contiguous Heap memory for an array of $N$ students:
   ```c
   StudentGrade *classroom = (StudentGrade*)malloc(N * sizeof(StudentGrade));
   ```
4. Collects input data for each student, calculates their weighted average score ($\text{Average} = 0.4 \times \text{Midterm} + 0.6 \times \text{Final}$), and prints a formatted summary table.
5. Frees all dynamically allocated memory and neutralizes the pointer to `NULL`.
6. Include terminal screenshots proving successful memory allocation and safe cleanup.

---

### Task 4: Self-Referential Structs – Gateway to Linked Lists
In computer science, a struct that contains a pointer to another structure of its own type is called a **Self-Referential Struct**. This is the fundamental building block of Linked Lists, Trees, and Graphs.

Examine the following code:

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *next; // Self-referential pointer!
} Node;

int main(void) {
    // Creating two independent nodes on Heap:
    Node *first = (Node*)malloc(sizeof(Node));
    Node *second = (Node*)malloc(sizeof(Node));

    first->data = 10;
    first->next = second; // Linking first node to second node!

    second->data = 20;
    second->next = NULL;   // End of list!

    // Traversal using a temporary pointer:
    Node *current = first;
    while (current != NULL) {
        printf("Node Data: %d (Address: %p, Next: %p)\n", 
               current->data, (void*)current, (void*)current->next);
        current = current->next;
    }

    // Clean up:
    free(first);
    free(second);
    return 0;
}
```

1. Explain why the self-referential member must be a pointer (`struct Node *next`) rather than a direct struct instance (`struct Node next`). What would happen to the size of the struct if it contained an instance of itself?
2. Draw or sketch an ASCII memory diagram representing `first`, `second`, their `data`, and their `next` pointer relationships.
3. Trace how the `while (current != NULL)` traversal loop visits nodes sequentially until it terminates.

---

## 📄 DYS Submission Template

Copy and paste the template below into your Microsoft Word document (`.docx`), write your answers and paste your code/screenshots, and upload it to **DYS (Ders Yönetim Sistemi)**:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                            FACULTY OF ENGINEERING
                        DEPARTMENT OF SOFTWARE ENGINEERING
                       SE2009 - LAB 03 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Submission Date: [DD/MM/YYYY]
• OneDrive View/Download Link: [Paste Your Accessible OneDrive Sharing Link Here]

--------------------------------------------------------------------------------
TASK 1: STRUCT PADDING & MEMORY ALIGNMENT INVESTIGATION
Your Answer & Analysis:
(Provide the sizeof calculations, explain why SchemaA and SchemaB differ in size, 
detail byte-by-byte padding locations, and paste your verification terminal screenshot.)


TASK 2: DOT (.) VS. ARROW (->) OPERATOR & PASS-BY-REFERENCE
Your Answer & Code:
(Explain why levelUpFaulty fails, provide your completed levelUpCorrect code, 
and show console output proof.)


TASK 3: DYNAMIC STRUCT ARRAY (STUDENT REGISTRY) ON HEAP
Your Answer & Source Code:
(Paste your full compilable C code implementing dynamic allocation, user input, 
weighted average calculation, formatted output, and safe free() cleanup.)


TASK 4: SELF-REFERENTIAL STRUCTS – GATEWAY TO LINKED LISTS
Your Answer & Diagram:
(Explain why next must be a pointer, provide your ASCII node diagram, and explain 
the while loop traversal mechanism.)

================================================================================
```
