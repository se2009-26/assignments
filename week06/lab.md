# SE2009: Data Structures – Lab 06: Circular Singly Linked Lists

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Lab Session:** Lab 06 – Circular Singly Linked Lists
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi)**  
  *(All lab reports must be submitted directly via the University DYS platform.)*
* **Submission Deliverable:** Microsoft Word document (`.docx`) with your accessible OneDrive sharing link included at the top.
* **File Naming Convention:** `studentNo_surname_name_SE2009_Lab06.docx`
* **Difficulty Level:** Intermediate (Circular Pointers, Traversal Mechanics, Node Insertion & Deletion)

> [!IMPORTANT]
> **Submission & Delivery Policy:**  
> Compile and test all tasks on your machine, take required terminal output screenshots, and submit your final Microsoft Word report (`.docx`) to the **DYS (Ders Yönetim Sistemi)** portal. Make sure your OneDrive share link is active and readable. **Do not push your report to GitHub directly.**

---

# 👨‍🏫 PART 1: Instructor-Led Core Application

This section will be analyzed on the board and live-coded on screen by the instructor at the beginning of the lab.

```text
===================================================================================
                   CIRCULAR SINGLY LINKED LIST MEMORY MODEL
===================================================================================

[ Standard Singly Linked List: Last node points to NULL ]
Head -> [ 10 | next ] ---> [ 20 | next ] ---> [ 30 | NULL ]

[ Circular Singly Linked List: Last node points back to Head ]
Head -> [ 10 | next ] ---> [ 20 | next ] ---> [ 30 | next ] ---+
  ^                                                             |
  +-------------------------------------------------------------+

Key Rule: There is NO NULL in a circular linked list!
Traversal Condition: do { ... curr = curr->next; } while (curr != head);
===================================================================================
```

---

### 📌 Core Concept: Creation, Append & Traversal

In a circular singly linked list, the `next` pointer of the last node points back to the first node (`head`). 

```c
#include <stdio.h>
#include <stdlib.h>

// Definition of a Circular Singly Linked List Node
typedef struct Node {
    int data;
    struct Node *next;
} Node;

// Insert a node at the end of the circular list
void insertEnd(Node **head, int val) {
    Node *newNode = (Node *)malloc(sizeof(Node));
    newNode->data = val;

    // Case 1: Empty list (Node points to itself)
    if (*head == NULL) {
        newNode->next = newNode;
        *head = newNode;
        return;
    }

    // Case 2: Traverse to the last node (whose next is head)
    Node *temp = *head;
    while (temp->next != *head) {
        temp = temp->next;
    }

    // Link last node to new node, and new node back to head
    temp->next = newNode;
    newNode->next = *head;
}

// Display the circular list using do-while
void printList(const Node *head) {
    if (head == NULL) {
        printf("List is empty.\n");
        return;
    }

    const Node *temp = head;
    printf("Circular List: ");
    do {
        printf("[%d] -> ", temp->data);
        temp = temp->next;
    } while (temp != head);
    printf("(loops back to head: %d)\n", head->data);
}

int main(void) {
    Node *head = NULL;

    printf("=== Instructor Demo: Circular List Basics ===\n\n");
    insertEnd(&head, 10);
    insertEnd(&head, 20);
    insertEnd(&head, 30);

    printList(head);

    return 0;
}
```

---

# 💻 PART 2: Student Hands-on Lab Tasks

Complete all three tasks in your Microsoft Word report and submit via **DYS (Ders Yönetim Sistemi)**.

---

### Task 1: Conceptual & Traversal Mechanics

Answer the following questions based on the circular list topology:

1. **Infinite Loop Danger:**
   * In a standard linked list, we traverse using `while (curr != NULL)`. Why does this condition cause an **infinite loop** in a circular linked list?
2. **The `do-while` Loop:**
   * Why do we use `do { ... } while (curr != head);` instead of `while (curr != head)` when traversing a circular list? What happens if you start with `curr = head` in a normal `while` loop?
3. **Pointer Tracing:**
   * Draw or describe the pointer connections for a 3-node circular list containing `10, 20, 30`.
   * What exact pointer assignments must happen when a new node `5` is inserted at the beginning of this list?

---

### Task 2: Hands-on Practice: Prepend, Search & Count

Extend the circular linked list implementation by writing the following functions:

```c
// Prototypes to implement:
void insertBegin(Node **head, int val);
int countNodes(const Node *head);
int searchKey(const Node *head, int target);
```

#### Requirements:
1. **`insertBegin(Node **head, int val)`:**
   * Inserts a new node at the front of the list.
   * Remember: The last node's `next` pointer must be updated to point to the new `*head`!
2. **`countNodes(const Node *head)`:**
   * Returns the total number of nodes in the circular list (return 0 if empty).
3. **`searchKey(const Node *head, int target)`:**
   * Searches for `target` in the circular list. Returns `1` if found, `0` otherwise.
4. **Test in `main()`:**
   * Build a circular list: insert `10, 20, 30` at the end, then insert `5` at the beginning (`5 -> 10 -> 20 -> 30`).
   * Print the list and print total node count.
   * Search for an existing element (`20`) and a non-existing element (`99`).
   * Paste your clean C code and execution terminal screenshot.

---

### Task 3: Node Deletion in a Circular List

Implement a function to delete a node by value from a circular singly linked list:

```c
int deleteNode(Node **head, int val);
```

#### Requirements:
Handle the three main cases carefully:
1. **Case 1: Single-Node List:** If the list has only 1 node and it matches `val`, free it and set `*head = NULL`.
2. **Case 2: Deleting Head Node:** Find the last node so you can update its `next` pointer to `(*head)->next`, move `*head` forward, and free the old head.
3. **Case 3: Deleting a Middle or Last Node:** Maintain a `prev` pointer, bypass `curr` (`prev->next = curr->next`), and free `curr`.
4. If `val` is not found, return `0` without corrupting the list.

#### Test Execution in `main()`:
* Create a circular list: `10, 20, 30, 40`.
* Delete head node (`10`) $\rightarrow$ verify list becomes `20 -> 30 -> 40`.
* Delete middle node (`30`) $\rightarrow$ verify list becomes `20 -> 40`.
* Delete tail node (`40`) $\rightarrow$ verify list becomes `20`.
* Delete the remaining node (`20`) $\rightarrow$ verify list becomes empty.
* Paste your complete C program and terminal execution screenshot.

---

## 📄 DYS Submission Template

Copy and paste the template below into your Microsoft Word document (`.docx`), write your answers and paste your code/screenshots, and upload it to **DYS (Ders Yönetim Sistemi)**:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                             FACULTY OF ENGINEERING
                        DEPARTMENT OF SOFTWARE ENGINEERING
                        SE2009 - LAB 06 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Submission Date: [DD/MM/YYYY]
• OneDrive View/Download Link: [Paste Your Accessible OneDrive Sharing Link Here]

--------------------------------------------------------------------------------
TASK 1: CONCEPTUAL & TRAVERSAL MECHANICS
Your Answer:
(Explain why while(curr != NULL) loops forever and why do-while is necessary.
Describe pointer tracing for inserting a node at the beginning.)


TASK 2: PREPEND, SEARCH & COUNT IMPLEMENTATION
Your Answer & Source Code:
(Paste your C code implementing insertBegin, countNodes, and searchKey.
Include terminal screenshot demonstrating list printing, counting, and searching.)


TASK 3: CIRCULAR LIST DELETION
Your Answer & Source Code:
(Paste your C code implementing deleteNode handling single-node, head, and
middle/tail deletions. Include terminal screenshot showing step-by-step deletion.)

================================================================================
```
