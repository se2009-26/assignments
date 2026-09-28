# SE2009 – Week 01 PreLab: C Memory Management & Pointer Foundations for Data Structures

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Assignment:** PreLab 01 – Conceptual Discussion & Memory Analysis Report
* **Central Assignment Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Student Target Repo:** Your private student repository (`Lab_se-XXXXXXXXX` / `Labse-XXXXXXXXX`)
* **Submission Deadline:** **Tuesday, September 29, 2026 at 23:59**
* **Submission Deliverable:** Microsoft Word document (`.docx`) placed under `week01/` or `prelab01/` in your private lab repository + OneDrive sharing link.
* **File Naming Convention:** `studentNo_surname_name_SE2009_PreLab01.docx`

> [!CAUTION]
> **Mandatory Completion Policy:**  
> Submitting this PreLab report before the deadline (**September 29, 23:59**) is **mandatory**. Students who fail to complete and commit their report to their private repository on time will receive **0 points (no lab grade)** for this lab session.

---

## 🎯 Workflow for Students

1. **Review:** Read the questions below in the central `assignments` repository.
2. **Prepare:** Fill out your answers in Microsoft Word using the report template below.
3. **Commit & Push:** Save your `.docx` file as `studentNo_surname_name_SE2009_PreLab01.docx` inside the `week01/` folder in your private student repository (`Lab_se-XXXXXXXXX`), then commit and push to GitHub:
   ```bash
   cd Lab_se-XXXXXXXXX
   mkdir -p week01
   # Place your studentNo_surname_name_SE2009_PreLab01.docx file into week01/
   git add week01/
   git commit -m "week01: submit prelab 01 report"
   git push origin main
   ```

---

## ❓ PreLab Conceptual Questions

Answer the following three open-ended questions in detail in your Word report:

### Question 1: Pointers, Memory Addressing & Pointer Arithmetic
**Topic:** Low-level Memory Operations and Array-Pointer Equivalence in C

1. Explain how memory addresses are structured in modern architectures and how the address-of operator (`&`) and dereference operator (`*`) interact with variables.
2. Define **pointer arithmetic**. When you increment an integer pointer `int *ptr` with `ptr++`, why does the address change by `4` (or `sizeof(int)`) bytes instead of `1` byte?
3. Discuss the relationship and equivalence between array indexing `arr[i]` and pointer arithmetic `*(arr + i)`. Explain why array boundaries are not automatically checked in C and what risks this introduces.

---

### Question 2: Dynamic Memory Allocation & The Lifecycle of Stack vs. Heap
**Topic:** Memory Segments, `malloc`/`calloc`/`realloc`/`free`, and Common Memory Faults

1. Contrast the **Stack** and **Heap** memory segments regarding allocation mechanism, access speed, memory capacity, and variable lifetime.
2. Explain the operational differences between `malloc()`, `calloc()`, and `realloc()`. Why is it critical to always verify whether the returned pointer is `NULL` before using it?
3. Define and distinguish the following critical memory bugs:
   * **Memory Leak**
   * **Dangling Pointer**
   * **Double Free**
   * **Segmentation Fault (Segfault)**  
   Explain why manual memory management is indispensable when implementing dynamic data structures (such as Linked Lists and Trees).

---

### Question 3: User-Defined Types (`struct`) & Self-Referential Data Structures
**Topic:** Structure Memory Layout, Parameter Passing Mechanisms, and Node Construction

1. Explain the difference between passing a `struct` to a function **by value** (`void process(struct Record r)`) versus **by pointer/reference** (`void process(const struct Record *r)`). Compare both approaches in terms of memory copying overhead and execution performance for large structures.
2. What is the syntactic and semantic difference between the dot operator (`.`) and the arrow operator (`->`) when accessing struct members?
3. Explain the concept of a **Self-Referential Structure** (e.g., `struct Node { int data; struct Node *next; };`). Why must the `next` member be a pointer to `struct Node` rather than a direct `struct Node` instance?

---

## 📄 Word (.docx) Submission Template

Copy and paste the template below into your Microsoft Word document:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                            FACULTY OF ENGINEERING
                       DEPARTMENT OF SOFTWARE ENGINEERING
                     SE2009 - PRELAB 01 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Private Lab Repo: https://github.com/se2009-26/Lab_se-[Your Student ID]
• Submission Date: [DD/MM/YYYY]
• OneDrive View/Download Link: [Insert Link - View & Download Only]

--------------------------------------------------------------------------------
IMPORTANT NOTICE:
Submission Deadline: September 29, 2026, 23:59.
Failure to submit this PreLab report by the deadline results in 0 points 
(no lab grade) for this lab session.
--------------------------------------------------------------------------------

SECTION 1: CONCEPTUAL EVALUATION QUESTIONS

[QUESTION 1: Pointers, Memory Addressing & Pointer Arithmetic]
Your Answer:
(Provide a detailed explanation in your own words regarding memory addresses, 
pointer dereferencing, pointer arithmetic mechanics, and array-pointer duality. 
Add short C code snippets where helpful.)


[QUESTION 2: Dynamic Memory Allocation: Stack vs. Heap & Memory Hazards]
Your Answer:
(Explain Stack vs Heap characteristics, allocation functions malloc/calloc/realloc, 
and provide definitions/mitigations for memory leaks, dangling pointers, and segfaults.)


[QUESTION 3: Structs, Parameter Passing & Self-Referential Types]
Your Answer:
(Analyze pass-by-value vs pass-by-pointer performance, dot vs arrow operators, 
and explain why self-referential structures require pointers in data structures.)


--------------------------------------------------------------------------------

SECTION 2: KEY TAKEAWAYS & SUMMARY
• Summarize the two most important C memory management concepts you learned and 
  how they prepare you for building complex data structures (2–3 sentences):
  [Your Summary...]

================================================================================
```
