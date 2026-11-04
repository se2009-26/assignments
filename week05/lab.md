# SE2009: Data Structures – Lab 05: Array-Based Lists vs. Singly Linked Lists (Memory Architecture, Pointer Mechanics & Performance Trade-offs)

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Lab Session:** Lab 05 – Array-Based Sequential Lists vs. Singly Linked Lists
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi)**  
  *(All lab reports must be submitted directly via the University DYS platform.)*
* **Submission Deliverable:** Microsoft Word document (`.docx`) with your accessible OneDrive sharing link included at the top.
* **File Naming Convention:** `studentNo_surname_name_SE2009_Lab05.docx`
* **Difficulty Level:** Intermediate (Heap Allocation, Pointer-to-Pointer `Node**`, Node Traversal, Deletion Edge Cases, Big-O & Cache Locality)

> [!IMPORTANT]
> **Submission & Delivery Policy:**  
> This laboratory assignment is announced on GitHub for guidelines and starter code. You must compile and test all tasks on your machine, take required terminal output screenshots, and submit your final Microsoft Word report (`.docx`) to the **DYS (Ders Yönetim Sistemi)** portal. Make sure your OneDrive share link is active and readable. **Do not push this file to GitHub directly.**

---

# 👨‍🏫 PART 1: Instructor-Led Core Application

This section will be analyzed on the whiteboard and live-coded on screen by the instructor at the beginning of the lab.

```text
===================================================================================
             CONTIGUOUS ARRAY vs. SCATTERED LINKED LIST MEMORY MODEL
===================================================================================

[ 1. CONTIGUOUS ARRAY IN MEMORY (Physical Adjacency = Logical Order) ]
Base Address: 0x1000
+-------------------+-------------------+-------------------+-------------------+
|  arr[0] = 10      |  arr[1] = 20      |  arr[2] = 30      |  arr[3] = 40      |
+-------------------+-------------------+-------------------+-------------------+
0x1000              0x1004              0x1008              0x100C  (stride: 4 B)
* CPU Cache Line (64 Bytes): Loads arr[0..15] in a SINGLE hardware memory fetch!
* Index Calculation: Address(arr[i]) = Base + (i * sizeof(int))  ==> O(1) Access!
* Prepend / Insert at Index 0: Requires shifting ALL elements right ==> O(N) Shift!


[ 2. SINGLY LINKED LIST ON THE HEAP (Scattered Nodes Linked by Pointers) ]
Head: 0x5020
      |
      v
+---------------+     +---------------+     +---------------+
| data: 10      |     | data: 20      |     | data: 30      |
| next: 0x8140  | --> | next: 0x2400  | --> | next: NULL    |
+---------------+     +---------------+     +---------------+
Heap @ 0x5020         Heap @ 0x8140         Heap @ 0x2400
* Spatial Locality: POOR (Each node allocated separately on heap; CPU cache misses).
* Index Calculation: Impossible! Must traverse pointers one by one ==> O(N) Access.
* Prepend (Insert at Head): newNode->next = head; head = newNode ==> O(1) Instant!
===================================================================================
```

---

### 📌 Application 1: Dynamic Node Allocation, Pointer-to-Pointer (`Node**`), and Prepend Operations

#### 💡 Concept Breakdown & Memory Mechanics
1. **Node Topology:** A singly linked list node is a self-referential structure containing data and a pointer to the next node:
   ```c
   typedef struct Node {
       int data;
       struct Node *next;
   } Node;
   ```
2. **Why Pointer-to-Pointer (`Node **head_ref`)?**
   * If a function takes `Node *head`, it receives a **copy** of the head pointer address (pass-by-value). Modifying `head` inside the function does **not** update `head` in `main()`.
   * Passing `Node **head_ref` (the address of the pointer variable in `main`) allows the function to dereference it (`*head_ref = newNode`) and directly update the caller's head variable.
3. **Prepend Cost ($O(1)$ vs $O(N)$):**
   * In an array, prepending an element forces shifting existing items one position to the right, taking $O(N)$ time.
   * In a linked list, prepending simply requires allocating a new node, setting its `next` pointer to the current head, and updating the head pointer. Exactly 3 operations regardless of list size: **$O(1)$ constant time**.
4. **Memory Deallocation (`freeList`):**
   * Unlike arrays that can be freed with a single `free(arr)` call, every linked list node was created with an individual `malloc()`. Therefore, each node must be visited and freed sequentially to prevent memory leaks.

#### 💻 Application 1 Source Code:
```c
#include <stdio.h>
#include <stdlib.h>

// Definition of Singly Linked List Node
typedef struct Node {
    int data;
    struct Node *next;
} Node;

// Insert a new node at the very beginning of the list - O(1) Constant Time
void insertAtHead(Node **head_ref, int new_data) {
    if (head_ref == NULL) return;

    // 1. Allocate heap memory for new node
    Node *newNode = (Node *)malloc(sizeof(Node));
    if (newNode == NULL) {
        perror("Fatal: Memory allocation failed for new node");
        exit(EXIT_FAILURE);
    }

    // 2. Assign payload data
    newNode->data = new_data;

    // 3. Point new node's next to the current head
    newNode->next = *head_ref;

    // 4. Update the caller's head pointer to point to the new node
    *head_ref = newNode;

    printf("[InsertHead] Successfully added value %d at address %p\n", new_data, (void *)newNode);
}

// Traverse and print list nodes along with their memory addresses - O(N)
void printList(const Node *head) {
    printf("List State: Head -> ");
    const Node *curr = head;
    while (curr != NULL) {
        printf("[%d | next:%p] -> ", curr->data, (void *)curr->next);
        curr = curr->next;
    }
    printf("NULL\n");
}

// Safely deallocate all nodes from the heap to prevent memory leaks - O(N)
void freeList(Node **head_ref) {
    if (head_ref == NULL || *head_ref == NULL) return;

    Node *curr = *head_ref;
    Node *tempNext = NULL;
    int freed_count = 0;

    while (curr != NULL) {
        tempNext = curr->next; // Save pointer to next node before freeing current
        free(curr);            // Release current node heap block
        curr = tempNext;       // Advance to next node
        freed_count++;
    }

    *head_ref = NULL; // Crucial: avoid dangling pointer in caller
    printf("[FreeList] Deallocated %d nodes from heap. Head set to NULL.\n", freed_count);
}

// Array Prepend Demonstration: Shows the O(N) shifting burden
void arrayPrepend(int arr[], int *size, int capacity, int value) {
    if (*size >= capacity) {
        printf("[ArrayPrepend] Error: Array capacity full!\n");
        return;
    }
    // Shift every existing element one index to the right
    for (int i = *size; i > 0; i--) {
        arr[i] = arr[i - 1];
    }
    arr[0] = value;
    (*size)++;
    printf("[ArrayPrepend] Shifted %d elements right. Inserted %d at arr[0].\n", *size - 1, value);
}

int main(void) {
    printf("=================================================================\n");
    printf("    SE2009 Lab 05: Array vs. Singly Linked List Mechanics       \n");
    printf("=================================================================\n\n");

    // --- Part A: Singly Linked List O(1) Prepend Demo ---
    printf("--- PART A: Linked List Prepend Operations (O(1)) ---\n");
    Node *head = NULL; // Initially empty list

    insertAtHead(&head, 30);
    insertAtHead(&head, 20);
    insertAtHead(&head, 10);
    printList(head);

    // --- Part B: Array O(N) Shift Demo ---
    printf("\n--- PART B: Array Prepend Operations (O(N) Shift) ---\n");
    int arr[10] = {20, 30};
    int current_size = 2;
    printf("Initial Array: [%d, %d] (size = %d)\n", arr[0], arr[1], current_size);

    arrayPrepend(arr, &current_size, 10, 10);
    printf("Updated Array: [%d, %d, %d] (size = %d)\n", arr[0], arr[1], arr[2], current_size);

    // Clean up heap
    printf("\n--- Clean Up ---\n");
    freeList(&head);
    printList(head);

    return 0;
}
```

---

# 💻 PART 2: Student Hands-on Lab Tasks & Questions

Answer all three tasks in your Microsoft Word report and submit via **DYS (Ders Yönetim Sistemi)**.

---

### Task 1: Memory Footprint, Cache Locality & Big-O Trade-off Analysis

A high-frequency trading firm and an embedded telemetry system debate whether to use dynamic arrays (`int *arr`) or singly linked lists (`Node *head`).

1. **CPU Cache Lines & Hardware Prefetching:**
   * Explain the concept of **Spatial Locality**. Why does traversing an array of 50,000 integers utilize L1/L2 CPU cache lines (typically 64 bytes wide) with near-zero cache misses, while traversing a 50,000-node linked list causes frequent CPU pipeline stalls and cache misses?
2. **Quantitative Memory Footprint Calculation:**
   Suppose an application stores $N = 1,000,000$ integers (`int` = 4 bytes).
   * **Array Footprint:** Calculate the total raw heap memory needed for a single contiguous buffer:
     $$\text{RAM}_{\text{Array}} = N \times \text{sizeof(int)}$$
   * **Singly Linked List Footprint:** On a 64-bit operating system (`sizeof(void*) == 8` bytes):
     ```c
     typedef struct Node {
         int data;           // 4 bytes
         struct Node *next;  // 8 bytes
     } Node;
     ```
     Calculate `sizeof(Node)` including 4-byte struct padding. In addition, every standard `malloc()` chunk carries an allocator bookkeeping header overhead of approximately 16 bytes per allocation.
     Calculate the total memory consumed by the 1,000,000 separate nodes on the heap:
     $$\text{RAM}_{\text{List}} = N \times (\text{sizeof(Node)} + \text{header\_overhead})$$
   * What percentage of the linked list memory is pure data payload vs. structural overhead?
3. **Big-O Algorithmic Comparison Table:**
   Reproduce and complete the following comparison table in your report, providing brief justifications for each:

   | Operation | Array (Fixed/Dynamic) | Singly Linked List (Head only) | Singly Linked List (with Tail pointer) |
   | :--- | :--- | :--- | :--- |
   | Random Access by Index (`k`) | | | |
   | Insert at Head (Prepend) | | | |
   | Insert at Tail (Append) | | | |
   | Insert after given Node/Index | | | |
   | Delete First Element (Head) | | | |
   | Delete Given Value (Search + Del) | | | |
   | Memory Overhead per Element | | | |

---

### Task 2: Singly Linked List Implementation: Tail Insertion, Search & Safe Deletion

Write a complete, modular C program that extends the core linked list implementation with critical operations:

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

// Prototypes to implement:
void insertAtHead(Node **head, int val);
void insertAtTail(Node **head, int val);
int searchNode(const Node *head, int target, int *steps);
int deleteByValue(Node **head, int target);
void printList(const Node *head);
void freeList(Node **head);
```

#### Detailed Implementation Requirements:
1. **`insertAtTail(Node **head, int val)`:**
   * Allocate new node. If list is empty (`*head == NULL`), set `*head = newNode`.
   * Otherwise, traverse to the last node (where `curr->next == NULL`) and link `curr->next = newNode`.
2. **`searchNode(const Node *head, int target, int *steps)`:**
   * Linearly traverse the list. Track the number of nodes visited via `*steps`.
   * Return `1` if target is found, `0` otherwise.
3. **`deleteByValue(Node **head, int target)`:**
   Handle all four boundary cases correctly:
   * **Case 1:** List is empty (`*head == NULL`) $\rightarrow$ return 0.
   * **Case 2:** The target node is at the head (`(*head)->data == target`) $\rightarrow$ move `*head = (*head)->next`, `free()` old head, return 1.
   * **Case 3:** The target node is in the middle or at the tail $\rightarrow$ maintain a `prev` pointer while searching, rewire `prev->next = curr->next`, `free(curr)`, return 1.
   * **Case 4:** Target value does not exist in the list $\rightarrow$ return 0 without modifying the list.
4. **Driver `main()` Execution Test:**
   * Build a list containing: `10, 25, 40, 55, 70`.
   * Search for `40` (record steps) and search for `99` (not found).
   * Delete the head node (`10`). Print list.
   * Delete a middle node (`40`). Print list.
   * Delete the tail node (`70`). Print list.
   * Attempt to delete a non-existent element (`99`). Print error message.
   * Free the entire list and verify `head == NULL`.
5. Provide your complete, clean C code and a terminal screenshot verifying all operations.

---

### Task 3: Systems Simulation: Sensor Telemetry Packet Buffer (Array vs. Heap Verification)

An IoT environmental monitoring gateway records sensor packets using the following data structure:

```c
#include <stdio.h>
#include <stdlib.h>
#include <stddef.h> // For ptrdiff_t and pointer difference arithmetic

typedef struct {
    int packet_id;
    char sensor_type[16]; // e.g., "TEMPERATURE", "PRESSURE"
    double reading;
} SensorPacket;

typedef struct PacketNode {
    SensorPacket data;
    struct PacketNode *next;
} PacketNode;
```

#### Requirements:
1. **Contiguous Memory Address Proof (Array):**
   * Allocate an array of 4 `SensorPacket` elements on the stack or dynamic memory.
   * Print the memory address (`%p`) of each element and compute the byte difference between adjacent elements: `(char*)&arr[i] - (char*)&arr[i-1]`.
   * Prove that the distance between consecutive elements strictly equals `sizeof(SensorPacket)`.
2. **Scattered Heap Address Proof (Linked List):**
   * Dynamically allocate 4 individual `PacketNode` structures on the heap using `malloc()` and link them.
   * Print the memory address of each allocated node and its `next` pointer target.
   * Analyze the differences between node addresses: why are they not strictly sequential, and what does this demonstrate about the C runtime heap allocator?
3. **FIFO Queue Operation Comparison:**
   * Implement a function `void enqueuePacketList(PacketNode **head, PacketNode **tail, SensorPacket pkt)` that inserts a packet at the tail in $O(1)$ time using both head and tail pointers.
   * Implement a function `int dequeuePacketList(PacketNode **head, PacketNode **tail, SensorPacket *out_pkt)` that removes and returns the packet from the head in $O(1)$ time.
   * Contrast this with an array queue: explain why dequeuing from index 0 in a naive array requires $O(N)$ operations or requires a circular array index model (`head = (head + 1) % CAPACITY`).
4. Provide your complete, working C program and a terminal screenshot showing the memory address inspection and queue execution.

---

## 📄 DYS Submission Template

Copy and paste the template below into your Microsoft Word document (`.docx`), write your answers and paste your code/screenshots, and upload it to **DYS (Ders Yönetim Sistemi)**:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                             FACULTY OF ENGINEERING
                        DEPARTMENT OF SOFTWARE ENGINEERING
                        SE2009 - LAB 05 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Submission Date: [DD/MM/YYYY]
• OneDrive View/Download Link: [Paste Your Accessible OneDrive Sharing Link Here]

--------------------------------------------------------------------------------
TASK 1: MEMORY FOOTPRINT, CACHE LOCALITY & BIG-O TRADE-OFF ANALYSIS
Your Answer & Calculations:
(Explain Spatial Locality, CPU cache line prefetching vs. cache misses.
Show step-by-step memory overhead calculations for 1,000,000 integers in Array vs.
Singly Linked List on 64-bit OS with malloc headers. Paste completed Big-O table.)


TASK 2: SINGLY LINKED LIST CORE IMPLEMENTATION & SAFE DELETION
Your Answer & Source Code:
(Paste your full C program implementing insertAtHead, insertAtTail, searchNode,
and deleteByValue handling all 4 boundary cases. Include terminal screenshots
demonstrating deletions of head, middle, tail, and absent elements.)


TASK 3: SYSTEMS SIMULATION: SENSOR TELEMETRY PACKET BUFFER
Your Answer & Memory Proof:
(Paste your C program inspecting physical array addresses vs. scattered heap
node pointers. Explain the pointer address output and contrast linked list
O(1) enqueue/dequeue with array element shifting.)

================================================================================
```
