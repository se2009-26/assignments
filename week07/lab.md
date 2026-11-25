# SE2009: Data Structures – Lab 07: Stacks (LIFO) – Array & Linked List Implementations

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Lab Session:** Lab 07 – Stack Data Structure (Array-Based & Linked-List-Based LIFO)
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi)**  
  *(All lab reports must be submitted directly via the University DYS platform.)*
* **Submission Deliverable:** Microsoft Word document (`.docx`) with your accessible OneDrive sharing link included at the top.
* **File Naming Convention:** `studentNo_surname_name_SE2009_Lab07.docx`
* **Difficulty Level:** Intermediate (LIFO Operations, Overflow/Underflow Handling, Dynamic Linked Stack)

> [!IMPORTANT]
> **Submission & Delivery Policy:**  
> Compile and test all tasks on your machine, verify that your program matches the expected outputs, take required terminal screenshots, and submit your final Microsoft Word report (`.docx`) to the **DYS (Ders Yönetim Sistemi)** portal. Make sure your OneDrive share link is active and readable. **Do not push your report to GitHub directly.**

---

# 👨‍🏫 PART 1: Instructor-Led Core Application

This section will be explained on the board and live-coded on screen by the instructor at the beginning of the lab.

```text
===================================================================================
                         STACK DATA STRUCTURE (LIFO MODEL)
===================================================================================

  push(30) --->   +----+
                  | 30 |  <--- TOP (Last In, First Out)
                  +----+
                  | 20 |
                  +----+
                  | 10 |
                  +----+
              Array Index 0

Key Rules:
• Empty Stack: top == -1
• Full Stack:  top == MAX - 1
• All insertions (push) and removals (pop) happen ONLY at the TOP!
===================================================================================
```

---

### 📌 Core Concept: Stack Setup, Overflow Check & `push`

In an array-based stack, we store elements in a fixed-size array and keep track of the top element using an integer index `top`.

```c
#include <stdio.h>

#define MAX 5

typedef struct {
    int items[MAX];
    int top;
} Stack;

// Initialize stack as empty
void initStack(Stack *s) {
    s->top = -1;
}

// Check if stack is empty
int isEmpty(const Stack *s) {
    return s->top == -1;
}

// Check if stack is full
int isFull(const Stack *s) {
    return s->top == MAX - 1;
}

// Push an element onto the top of the stack
void push(Stack *s, int val) {
    if (isFull(s)) {
        printf("[Error] Stack Overflow! Cannot push %d.\n", val);
        return;
    }
    s->top++;
    s->items[s->top] = val;
    printf("Pushed: %d (top = %d)\n", val, s->top);
}

// Print stack contents from top to bottom
void printStack(const Stack *s) {
    if (isEmpty(s)) {
        printf("Stack is EMPTY.\n");
        return;
    }
    printf("Stack (top -> bottom): ");
    for (int i = s->top; i >= 0; i--) {
        printf("[%d] ", s->items[i]);
    }
    printf("\n");
}

int main(void) {
    Stack s;
    initStack(&s);

    printf("=== Instructor Demo: Stack Basics ===\n\n");
    push(&s, 10);
    push(&s, 20);
    push(&s, 30);
    printStack(&s);

    push(&s, 40);
    push(&s, 50);
    push(&s, 60); // Triggers Stack Overflow (MAX = 5)
    printStack(&s);

    return 0;
}
```

#### 🖥️ Expected Output (Part 1):
```text
=== Instructor Demo: Stack Basics ===

Pushed: 10 (top = 0)
Pushed: 20 (top = 1)
Pushed: 30 (top = 2)
Stack (top -> bottom): [30] [20] [10] 
Pushed: 40 (top = 3)
Pushed: 50 (top = 4)
[Error] Stack Overflow! Cannot push 60.
Stack (top -> bottom): [50] [40] [30] [20] [10] 
```

---

# 💻 PART 2: Student Hands-on Lab Tasks

Complete all three tasks in your Microsoft Word report and submit via **DYS (Ders Yönetim Sistemi)**.

---

### Task 1: Conceptual & LIFO Tracing Questions

Answer the following questions clearly:

1. **Initialization & Boundary Conditions:**
   * Why is `top` initialized to `-1` instead of `0` in an empty array-based stack?
   * Briefly explain what **Stack Overflow** and **Stack Underflow** mean.
2. **LIFO Operation Tracing:**
   * Starting with an empty stack (`MAX = 5`), trace the contents of the stack (from bottom to top) and the value of `top` after every step:
     ```text
     push(5) -> push(12) -> pop() -> push(8) -> push(20) -> pop() -> peek()
     ```
3. **Array vs. Linked-List Stack:**
   * What is the main limitation of an array-based stack compared to a linked-list-based stack?

---

### Task 2: Complete the Array-Based Stack (`pop`, `peek`, `getSize`)

Using the instructor's `Stack` structure from Part 1, implement the remaining core operations:

```c
// Function prototypes to implement:
int pop(Stack *s, int *removedVal);
int peek(const Stack *s, int *topVal);
int getSize(const Stack *s);
```

#### Requirements:
1. **`pop(Stack *s, int *removedVal)`:**
   * If the stack is empty, print `[Error] Stack Underflow! Stack is empty.` and return `0`.
   * Otherwise, store the top item into `*removedVal`, decrement `s->top`, and return `1`.
2. **`peek(const Stack *s, int *topVal)`:**
   * If the stack is empty, print `[Error] Stack is empty! Nothing to peek.` and return `0`.
   * Otherwise, store the top item into `*topVal` **without** removing it, and return `1`.
3. **`getSize(const Stack *s)`:**
   * Return the current number of elements inside the stack (`0` when empty).
4. **Driver Test in `main()`:**
   * Push `10, 20, 30`, print stack and size.
   * Call `peek` and `pop`, print stack and size again.
   * Pop the remaining elements in a loop (`while (!isEmpty(&s))`), then call `pop` once more to demonstrate **Stack Underflow**.
   * Verify your program produces the exact output below and paste your code + screenshot.

#### 🖥️ Expected Output (Task 2):
```text
=== Task 2: Array Stack Operations ===

Pushed: 10
Pushed: 20
Pushed: 30
Stack (top -> bottom): [30] [20] [10] 
Current size: 3

Peek top element: 30
Popped element: 30
Stack (top -> bottom): [20] [10] 
Current size: 2

Popped element: 20
Popped element: 10
[Error] Stack Underflow! Stack is empty.
Stack is EMPTY.
```

---

### Task 3: Linked-List Stack Application (String Reversal)

In a linked-list-based stack, there is **no fixed `MAX` limit**. Both `push` and `pop` happen at the head (`top`) in $O(1)$ time.

```c
typedef struct Node {
    char data;
    struct Node *next;
} Node;

// Function prototypes to implement:
void pushChar(Node **top, char c);
int popChar(Node **top, char *out);
void reverseString(char str[]);
```

#### Requirements:
1. **`pushChar(Node **top, char c)`:**
   * Dynamically allocate a new `Node`, store `c`, point `newNode->next` to `*top`, and update `*top = newNode`.
2. **`popChar(Node **top, char *out)`:**
   * If `*top == NULL`, return `0`.
   * Otherwise, store `(*top)->data` into `*out`, move `*top = (*top)->next`, `free` the removed node, and return `1`.
3. **`reverseString(char str[])`:**
   * Push every character of `str` onto a linked stack, then pop all characters back into `str` so the string becomes reversed in-place.
4. **Test in `main()`:**
   * Reverse `"SE2009"` and `"STACKS"`, verify your terminal matches the expected output below, and include your code + screenshot.

#### 🖥️ Expected Output (Task 3):
```text
=== Task 3: Linked-List Stack (String Reversal) ===

Original 1: SE2009
Reversed 1: 9002ES

Original 2: STACKS
Reversed 2: SKCATS
```

---

## 📄 DYS Submission Template

Copy and paste the template below into your Microsoft Word document (`.docx`), write your answers and paste your code/screenshots, and upload it to **DYS (Ders Yönetim Sistemi)**:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                             FACULTY OF ENGINEERING
                        DEPARTMENT OF SOFTWARE ENGINEERING
                        SE2009 - LAB 07 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Submission Date: [DD/MM/YYYY]
• OneDrive View/Download Link: [Paste Your Accessible OneDrive Sharing Link Here]

--------------------------------------------------------------------------------
TASK 1: CONCEPTUAL & LIFO TRACING QUESTIONS
Your Answer:
(Explain top = -1 initialization, Overflow/Underflow, trace the 7 operations,
and compare array vs. linked-list stack capacity.)


TASK 2: ARRAY-BASED STACK OPERATIONS (POP, PEEK, SIZE)
Your Answer & Source Code:
(Paste your complete C code implementing pop, peek, and getSize.
Include terminal screenshot matching the Expected Output.)


TASK 3: LINKED-LIST STACK (STRING REVERSAL)
Your Answer & Source Code:
(Paste your complete C code implementing pushChar, popChar, and reverseString.
Include terminal screenshot matching the Expected Output.)

================================================================================
```
