# SE2009: Data Structures – Lab 02: Pointers, Memory Architecture & Memory Leak Fundamentals

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Assignment:** Lab 02 – Pointer Mechanics, Low-Level Memory Architecture & CPU/GPU Memory Leaks
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi)**  
  *(All lab reports must be submitted directly via the University DYS platform.)*
* **Submission Deliverable:** Microsoft Word document (`.docx`) with your accessible OneDrive sharing link included inside the document.
* **File Naming Convention:** `studentNo_surname_name_SE2009_Lab02.docx`
* **Difficulty Level:** Beginner / Lower-Intermediate (Core Concepts, Code Analysis & Tracing)

> [!IMPORTANT]
> **Submission Policy:**  
> This laboratory assignment is announced on GitHub for review and instructions. You must submit your completed Microsoft Word report (`.docx`) directly to the **DYS (Ders Yönetim Sistemi)** assignment submission portal. Ensure your OneDrive view/download link is pasted at the top of your `.docx` report.

---

# 👨‍🏫 PART 1: Instructor-Led Core Applications

---

### 📌 Application 1: Memory Addressing, Pointer Dereferencing & Pointer Strides

#### 💡 Concept Breakdown & Memory Mechanics
Computer memory (RAM) is structured as a continuous array of 1-byte storage cells.
* The **Address-of Operator (`&`)** fetches the physical memory address of a variable.
* The **Dereference Operator (`*`)** accesses or modifies the value stored at that address.
* While all pointers occupy **8 bytes** in a 64-bit architecture, their data types (`char*`, `int*`, `double*`) define how many bytes are read upon dereferencing and the **stride (step size)** when performing pointer arithmetic (`ptr + 1`).

#### 💻 Application 1 Source Code:
```c
#include <stdio.h>

int main() {
    int score = 75;
    int *ptr = &score; // ptr holds the memory address of score

    printf("=== Basic Pointer Mechanics ===\n");
    printf("Value of score:                      %d\n", score);            // Output: 75
    printf("Memory address of score (&score):    %p\n", (void*)&score);    // e.g., 0x7ffd58
    printf("Address stored inside ptr (ptr):     %p\n", (void*)ptr);       // 0x7ffd58
    printf("Value pointed to by ptr (*ptr):      %d\n", *ptr);             // 75

    // Mutating memory directly via pointer:
    *ptr = 95;
    printf("New value of score after *ptr = 95:  %d\n", score);            // Output: 95

    printf("\n=== Pointer Types & Arithmetic Strides ===\n");
    char c = 'A';
    int i = 500;
    double d = 3.14159;

    char *c_ptr = &c;
    int *i_ptr = &i;
    double *d_ptr = &d;

    printf("char*   c_ptr: %p -> c_ptr + 1: %p (+%zu byte)\n", 
           (void*)c_ptr, (void*)(c_ptr + 1), sizeof(char));
    printf("int*    i_ptr: %p -> i_ptr + 1: %p (+%zu bytes)\n", 
           (void*)i_ptr, (void*)(i_ptr + 1), sizeof(int));
    printf("double* d_ptr: %p -> d_ptr + 1: %p (+%zu bytes)\n", 
           (void*)d_ptr, (void*)(d_ptr + 1), sizeof(double));

    return 0;
}
```

```text
MEMORY LAYOUT:
+-------------------+-----------------------------+
| Memory Address    | Stored Content              |
+-------------------+-----------------------------+
| 0x7ffd58          | score = 95                  | <----+ ptr points here
| 0x7ffd60          | ptr   = 0x7ffd58 (8 bytes)  | -----+
+-------------------+-----------------------------+
```

---

### 📌 Application 2: Dynamic Heap Memory & Dual-Memory Architecture (CPU Host vs. GPU VRAM)

#### 💡 Concept Breakdown & Memory Mechanics
1. **CPU Dynamic Memory:** Allocated on the **Heap** via `malloc()` / `calloc()`. When memory is allocated, it must be explicitly freed with `free()`. Immediately after calling `free(ptr);`, the pointer must be set to `NULL` to eliminate **Dangling Pointers**. Failing to free memory leads to a **Memory Leak**.
2. **GPU Dual-Memory Architecture:** High-performance AI and graphics frameworks utilize GPU Device Memory (VRAM) alongside CPU Host RAM. GPU memory is allocated using `cudaMalloc()` and freed using `cudaFree()`. Because GPU VRAM is strictly limited (e.g., 8 GB – 24 GB), unreleased tensors inside Deep Learning training loops rapidly trigger fatal **"CUDA Out of Memory (OOM)"** crashes.

```text
==================================================================================
                              SYSTEM DUAL MEMORY MODEL
==================================================================================
[ CPU HOST RAM (System Memory) ]        <==== PCIe Bus ====>  [ GPU DEVICE VRAM (Video RAM) ]
• Allocation: malloc()                                         • Allocation: cudaMalloc()
• Deallocation: free()                                         • Deallocation: cudaFree()
• Size: Large (16 GB – 128 GB)                                 • Size: Limited (6 GB – 24 GB)
• Failure: System RAM exhaustion                               • Failure: Fatal CUDA OOM crash
==================================================================================
```

#### 💻 Application 2 Source Code:
```c
#include <stdio.h>
#include <stdlib.h>

void cpuSafeMemoryManagement() {
    // 1. Allocate dynamic buffer of 5 integers (20 bytes) on Heap:
    int *dyn_arr = (int*)malloc(5 * sizeof(int));

    // Verify allocation success:
    if (dyn_arr == NULL) {
        perror("Heap allocation failed!");
        exit(EXIT_FAILURE);
    }

    // 2. Populate and display data:
    for (int i = 0; i < 5; i++) {
        *(dyn_arr + i) = (i + 1) * 100;
        printf("dyn_arr[%d] = %d (Address: %p)\n", i, *(dyn_arr + i), (void*)(dyn_arr + i));
    }

    // 3. Release memory to prevent Memory Leak:
    free(dyn_arr);

    // 4. Neutralize pointer to prevent Dangling Pointer bugs:
    dyn_arr = NULL;
    printf("Memory freed successfully and pointer neutralized to NULL.\n");
}

int main() {
    printf("=== CPU Dynamic Memory Management ===\n");
    cpuSafeMemoryManagement();
    return 0;
}
```

---

# 💻 PART 2: Student Hands-on Lab Tasks & Questions

Complete the following 4 practical tasks and questions in your Microsoft Word report and submit via **DYS (Ders Yönetim Sistemi)**.

---

### Task 1: Pointer Mutation & Execution Tracing
Analyze the following C program. Trace the changes in memory step-by-step and write down the exact console output produced by the `printf` statements:

```c
#include <stdio.h>

int main() {
    int x = 10;
    int y = 20;
    int *p1 = &x;
    int *p2 = &y;

    *p1 = *p1 + 5;
    *p2 = *p1 * 2;
    p1 = p2;
    *p1 = 50;

    printf("x = %d\n", x);
    printf("y = %d\n", y);
    printf("*p1 = %d\n", *p1);
    printf("*p2 = %d\n", *p2);

    return 0;
}
```
* **Required Deliverables:**
  1. Provide the exact 4 lines of console output.
  2. After executing `p1 = p2;`, does `*p1 = 50;` modify variable `x` or variable `y`? Explain the underlying pointer mechanism.

---

### Task 2: Pass-by-Reference: Implementing `correctSwap`
A student attempts to swap two integer variables using the following faulty function:

```c
#include <stdio.h>

// Faulty: Operates only on local stack copies
void faultySwap(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int x = 10, y = 20;
    faultySwap(x, y);
    printf("After faultySwap: x = %d, y = %d\n", x, y); // Outputs: 10, 20 (Unchanged!)
    return 0;
}
```
* **Required Deliverables:**
  1. Explain why `faultySwap` fails to alter `x` and `y` in `main()` (explain stack frame value copying).
  2. Write the corrected function `void correctSwap(int *a, int *b)` using pointers and provide the complete runnable C code showing `x` and `y` successfully swapped.

---

### Task 3: Array Traversal & Pointer Arithmetic Strides
Analyze the following C program:

```c
#include <stdio.h>

int main() {
    int arr[4] = {100, 200, 300, 400};
    int *ptr = arr; // Holds base address &arr[0]

    printf("Element 1: %d\n", *ptr);
    ptr++;
    printf("Element 2: %d\n", *ptr);
    printf("Element 4: %d\n", *(ptr + 2));

    return 0;
}
```
* **Required Deliverables:**
  1. When executing `ptr++`, why does the address advance by 4 bytes (or `sizeof(int)`) instead of 1 byte?
  2. Explain why `*(ptr + 2)` outputs `400`. Detail the address offset calculation step.

---

### Task 4: Dynamic Heap Allocation & Memory Leak Analysis (CPU vs. GPU)
1. Write a complete, safe C code snippet that:
   * Dynamically allocates an array of 5 integers on the Heap using `malloc()`.
   * Fills the array with values `10, 20, 30, 40, 50`.
   * Safely frees the allocated memory block and sets the pointer to `NULL`.
2. Compare a memory leak occurring on the CPU (System RAM) with one occurring on a GPU (Device VRAM). Specifically explain why memory leaks inside Deep Learning training loops cause fatal **"CUDA Out of Memory (OOM)"** crashes.

---

## 📄 DYS Submission Template

Copy and paste the template below into your Microsoft Word document (`.docx`), complete your answers, and upload it to **DYS (Ders Yönetim Sistemi)**:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                            FACULTY OF ENGINEERING
                       DEPARTMENT OF SOFTWARE ENGINEERING
                      SE2009 - LAB 02 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Submission Date: [DD/MM/YYYY]
• OneDrive View/Download Link: [Paste Your Accessible OneDrive Sharing Link Here]

--------------------------------------------------------------------------------
TASK 1: POINTER MUTATION & EXECUTION TRACING
Your Answer & Output:
(Provide the 4 console output lines and explain the pointer assignment mechanism.)


TASK 2: PASS-BY-REFERENCE (CORRECT SWAP IMPLEMENTATION)
Your Answer & Code:
(Explain why faultySwap fails and paste your correctSwap C source code.)


TASK 3: ARRAY TRAVERSAL & POINTER ARITHMETIC STRIDES
Your Answer:
(Explain pointer stride calculation based on data types and *(ptr+2) mechanics.)


TASK 4: DYNAMIC HEAP ALLOCATION & CPU/GPU MEMORY LEAK ANALYSIS
Your Answer & Code:
(Paste your safe malloc/free C snippet and explain CPU RAM vs GPU VRAM OOM impacts.)

================================================================================
```
