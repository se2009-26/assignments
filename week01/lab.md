# SE2009: Data Structures – Lab 01: Low-Level C Foundations, Memory Architecture & Pointer Mastery

**Course:** SE2009 – Data Structures  
**Lab Session:** Lab 01  
**Central Assignment Repo:** `https://github.com/se2009-26/assignments`  
**Student Target Repo:** Your private student repository (`Lab_se-XXXXXXXXX` / `Labse-XXXXXXXXX`)  
**Deliverable:** Microsoft Word report (`.docx`) including your 3 C source codes, terminal execution output evidence, and OneDrive sharing link, committed to `week01/lab/` in your private student repository.

---

# 👨‍🏫 PART 1: Instructor-Led Deep Dive

This section will be explained and built incrementally on the board and screen by the instructor at the beginning of the lab.

```text
===================================================================================
                                C MEMORY MODEL
===================================================================================
0xFFFFFFFF  +--------------------------------------------------------------------+
            | STACK (Automatic Lifetime, Function Frames, Local Variables)       |
            | - main(): my_data (0x1000)                                         |
            | - allocateBuffer(&my_data): buffer_ptr (pointer holding 0x1000)    |
            +--------------------------------------------------------------------+
            |                                 ↓ (Grows Downward)                 |
            |                                 ↑ (Grows Upward)                   |
            +--------------------------------------------------------------------+
            | HEAP (Dynamic Memory: malloc / realloc / free - Programmer Managed)|
            | - [0x5000] -> [ 10 | 20 | 30 | 40 | ... ]                          |
0x00000000  +--------------------------------------------------------------------+
```

---

## 📌 Step 1: Why a Single Pointer Is Not Enough

### 💡 Concept Breakdown & Memory Mechanics
In C, all function parameters are strictly **passed by value (copied)**.
* If a function is defined as `void allocate(int *ptr)` and calls `ptr = malloc(...)`, the address is assigned only to the local copy `ptr`. The original pointer inside `main()` remains **`NULL` (causing a guaranteed Memory Leak)**.
* To permanently modify the memory address stored in the caller's pointer (`int*`), you must pass the **address of the pointer itself (`&my_data`)** and receive it as **`int** buffer_ptr` (a pointer to a pointer)**.

### 💻 Step 1 Implementation:
```c
#include <stdio.h>
#include <stdlib.h>

/**
 * Step 1: Allocate initial memory on the Heap using a double pointer.
 * *buffer_ptr represents the exact pointer variable located in main().
 */
void allocateBuffer(int **buffer_ptr, size_t initial_capacity) {
    if (initial_capacity == 0) initial_capacity = 2;

    // Allocate memory from Heap and store its address directly into caller's pointer:
    *buffer_ptr = (int*)malloc(initial_capacity * sizeof(int));
    
    if (*buffer_ptr == NULL) {
        perror("Initial memory allocation failed!");
        exit(EXIT_FAILURE);
    }
    
    printf("[STEP 1] Allocated memory for %zu ints. Base Address: %p\n", 
           initial_capacity, (void*)*buffer_ptr);
}
```

---

## 📌 Step 2: Dynamic Capacity Doubling with `realloc` and the Safety Rule

### 💡 Concept Breakdown & Memory Mechanics
When the buffer is full (`size == capacity`), the capacity must be doubled to accommodate new elements.
* ⚠️ **Critical Bug:** Writing `*buffer_ptr = realloc(*buffer_ptr, new_cap);` is dangerous! If the operating system fails to allocate memory, `realloc` returns `NULL`. Overwriting the original pointer directly causes the old memory block address to be permanently lost, resulting in an **unrecoverable Memory Leak**.
* **Safe Pattern:** Always store the return value of `realloc` in a temporary pointer `int *temp`. Only overwrite `*buffer_ptr` when `temp != NULL`.

### 💻 Step 2 Implementation:
```c
/**
 * Step 2: Dynamically resize buffer using realloc and append element.
 */
void resizeAndAppend(int **buffer_ptr, size_t *size, size_t *capacity, int value) {
    // 1. Capacity check: Is buffer completely full?
    if (*size == *capacity) {
        size_t new_cap = (*capacity == 0) ? 2 : (*capacity * 2);
        
        // 2. Safe realloc call:
        int *temp = (int*)realloc(*buffer_ptr, new_cap * sizeof(int));
        if (temp == NULL) {
            perror("realloc failed to expand memory block!");
            return;
        }
        
        // 3. Update pointer (Heap base address may have moved to a new region):
        *buffer_ptr = temp;
        *capacity = new_cap;
        printf("[STEP 2] Capacity doubled to %zu. Current Address: %p\n", *capacity, (void*)*buffer_ptr);
    }
    
    // 4. Append the new value using pure pointer dereferencing and increment size:
    *(*buffer_ptr + *size) = value;
    (*size)++;
}
```

---

## 📌 Step 3: Pure Pointer Arithmetic without Square Brackets

### 💡 Concept Breakdown & Memory Mechanics
In C, the array subscript operator `arr[i]` is purely syntactic sugar. Under the hood, the compiler directly translates this into:
$$\text{Address} = \text{Base Address} + (i \times \text{sizeof(int)})$$
Therefore, `arr[i]` $\equiv$ `*(arr + i)`.

### 💻 Step 3 Implementation:
```c
/**
 * Step 3: Print all buffer elements strictly using pointer arithmetic.
 */
void printBuffer(const int *buffer, size_t size, size_t capacity) {
    printf("\n--- Buffer Elements (Size: %zu / Capacity: %zu) ---\n", size, capacity);
    for (size_t i = 0; i < size; i++) {
        // (buffer + i) -> Memory address of element at index i
        // *(buffer + i) -> Integer value stored at that address
        printf("Index %zu -> Address: %p | Value: %d\n", i, (void*)(buffer + i), *(buffer + i));
    }
    printf("---------------------------------------------------\n");
}
```

---

## 📌 Step 4: Safe Deallocation & Preventing Dangling Pointers

### 💡 Concept Breakdown & Memory Mechanics
Calling `free(ptr);` releases the allocated Heap memory back to the OS. However, the pointer variable still contains the old address (**Dangling Pointer**). Any subsequent read or write to `*ptr` triggers **Undefined Behavior** or a **Segmentation Fault**.
* **Solution:** Pass `int **buffer_ptr` to the cleanup function so that immediately after calling `free(*buffer_ptr)`, `*buffer_ptr` is explicitly set to `NULL`.

### 💻 Step 4 Implementation:
```c
/**
 * Step 4: Deallocate Heap memory and neutralize pointer to NULL.
 */
void freeBufferSafely(int **buffer_ptr) {
    if (buffer_ptr != NULL && *buffer_ptr != NULL) {
        printf("\n[STEP 4] Deallocating Heap memory at %p\n", (void*)*buffer_ptr);
        free(*buffer_ptr);
        *buffer_ptr = NULL; // Dangling pointer eliminated!
    }
}
```

---

## 📌 Step 5: Integrating All Modules

```c
int main() {
    int *my_data = NULL;
    size_t size = 0;
    size_t capacity = 0;

    // 1. Initialize dynamic buffer with initial capacity of 2
    allocateBuffer(&my_data, 2);
    capacity = 2;

    // 2. Append 7 elements (Triggers realloc capacity doubling: 2 -> 4 -> 8)
    for (int i = 1; i <= 7; i++) {
        resizeAndAppend(&my_data, &size, &capacity, i * 10);
    }

    // 3. Inspect buffer elements and raw memory addresses
    printBuffer(my_data, size, capacity);

    // 4. Safely clean up memory
    freeBufferSafely(&my_data);
    printf("Pointer address after safe free: %p (Safe NULL)\n", (void*)my_data);

    return 0;
}
```

---

# 💻 PART 2: Student Hands-on Lab Tasks

Students must independently implement, test, and document the following 3 programs in their Word report and repository.

---

### 🛠️ Task 1: Array Filtering Exclusively with Pointer Arithmetic
**Difficulty:** Medium  
**File:** `task1_filter.c`

**Task Description:**  
Write a function that accepts a dynamically allocated integer array and extracts all **even numbers** into a newly allocated dynamic array.

* **STRICT RULE:** You are **strictly forbidden from using square brackets (`[]`)** in your filtering function. All element traversal and indexing must be done strictly with pointer arithmetic (`*ptr`, `*(ptr + i)`, `ptr++`).
* **Function Prototype:**
  ```c
  int* filterEvenNumbers(const int *source, size_t src_size, size_t *out_size);
  ```
* **Requirements:**
  1. Traverse the `source` array using pointer arithmetic to count the number of even elements.
  2. Dynamically allocate exactly the required memory block with `malloc()`.
  3. Copy the even elements into the new block using pointer arithmetic.
  4. In `main()`, create a sample array, call `filterEvenNumbers()`, display the result, and ensure all allocated blocks are cleanly freed.

---

### 🛠️ Task 2: Dynamic 2D Matrix with Double Pointers & Transpose
**Difficulty:** Medium  
**File:** `task2_matrix.c`

**Task Description:**  
Implement a complete dynamic 2D matrix management system using double pointers (`int**`) that allocates an $R \times C$ matrix, fills it with values, computes its transpose ($C \times R$), and deallocates all memory without memory leaks.

* **Required Function Prototypes:**
  ```c
  int** createMatrix(size_t rows, size_t cols);
  int** transposeMatrix(int **matrix, size_t rows, size_t cols);
  void printMatrix(int **matrix, size_t rows, size_t cols);
  void freeMatrix(int ***matrix_ptr, size_t rows);
  ```
* **Requirements:**
  1. `createMatrix`: Allocate an array of $R$ row pointers (`int*`), then allocate each individual row of size $C$.
  2. `transposeMatrix`: Allocate a new matrix of dimension $C \times R$ and populate it such that $T[j][i] = M[i][j]$.
  3. `freeMatrix`: Accept a **triple pointer (`int***`)** so that after freeing all row buffers and the top-level pointer array, `*matrix_ptr` is safely reset to `NULL`.

---

### 🛠️ Task 3: Struct Records & In-Place Pointer Sorting
**Difficulty:** Medium  
**File:** `task3_students.c`

**Task Description:**  
Define a `Student` record and manage an array of dynamically allocated student records. Implement a pointer-based sorting routine to sort students by **GPA in descending order**.

* **Required Structure & Prototypes:**
  ```c
  typedef struct {
      int id;
      char name[32];
      float gpa;
  } Student;

  Student* createStudentArray(size_t count);
  void swapStudents(Student *a, Student *b); // Swap entire structs via pointers
  void sortStudentsByGPA(Student *arr, size_t count);
  void printStudents(const Student *arr, size_t count);
  ```
* **Requirements:**
  1. `swapStudents`: Swap two student structs using a temporary variable and pointer dereferencing.
  2. `sortStudentsByGPA`: Implement Bubble Sort or Selection Sort using pointer arithmetic (`*(arr + i)`) to sort by GPA in descending order.
  3. Deallocate the student array before program exit (verify zero leaks with `valgrind`).

---

## 📋 Student Submission Workflow

1. Complete and test all 3 C source codes (`task1_filter.c`, `task2_matrix.c`, `task3_students.c`).
2. Paste the **full source codes of all 3 C tasks**, your explanations, and terminal execution output screenshots into your Microsoft Word document (`studentNo_surname_name_SE2009_Lab01.docx`).
3. Include your accessible **OneDrive view/download link** at the top of your Word document.
4. Save and push your Word document (`.docx`) and the 3 C source files into the `week01/lab/` folder of your private student repository:

```bash
cd Lab_se-XXXXXXXXX
mkdir -p week01/lab
# Place your studentNo_surname_name_SE2009_Lab01.docx and 3 .c files into week01/lab/
git add week01/lab/
git commit -m "week01: submit in-lab C tasks and word report"
git push origin main
```
