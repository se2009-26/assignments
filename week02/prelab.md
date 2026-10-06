# SE2009 – Week 02 PreLab: Pointer Mechanics, Memory Architecture, Hexadecimal & Binary Foundations

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Assignment:** PreLab 02 – Low-Level Memory Representation, Hexadecimal/Binary Systems & Byte-Level Pointer Analysis
* **Central Assignment Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Student Target Repo:** Your private student repository (`Lab_se-XXXXXXXXX` / `Labse-XXXXXXXXX`)
* **Submission Deadline:** **Tuesday, October 6, 2026 at 23:59**
* **Submission Deliverable:** Microsoft Word document (`.docx`) placed under `week02/` or `prelab02/` in your private lab repository + OneDrive sharing link.
* **File Naming Convention:** `studentNo_surname_name_SE2009_PreLab02.docx`

> [!CAUTION]
> **Mandatory Completion Policy:**  
> Submitting this PreLab report before the deadline (**October 6, 2026, 23:59**) is **mandatory**. Students who fail to complete and commit their report to their private repository on time will receive **0 points (no lab grade)** for this lab session.

---

## 🎯 Workflow for Students

1. **Review:** Read the questions below in the central `assignments` repository.
2. **Prepare:** Fill out your answers in Microsoft Word using the report template provided at the bottom of this document.
3. **Commit & Push:** Save your `.docx` file as `studentNo_surname_name_SE2009_PreLab02.docx` inside the `week02/` folder in your private student repository (`Lab_se-XXXXXXXXX`), then commit and push to GitHub:
   ```bash
   cd Lab_se-XXXXXXXXX
   mkdir -p week02
   # Place your studentNo_surname_name_SE2009_PreLab02.docx file into week02/
   git add week02/
   git commit -m "week02: submit prelab 02 report"
   git push origin main
   ```

---

## ❓ PreLab Conceptual Questions

Answer the following three open-ended questions in detail in your Word report:

### Question 1: Number Systems in Memory: Binary, Hexadecimal & Byte Addressing
**Topic:** Bit-to-Hex Mapping, Memory Addressing, and Two's Complement Representation

1. Explain why computer memory is physically structured as **byte-addressable** ($8\text{-bit}$ chunks) and why memory addresses and raw bytes are conventionally represented in **Hexadecimal (Base-16, `0x...`)** rather than Decimal or Binary.
2. Explain the mathematical and structural relationship between Binary bits, Hexadecimal digits (nibbles), and Bytes:
   * How many bits does a single hexadecimal digit represent?
   * Convert the 32-bit integer value `0x4A3F2C10` into its 32-bit Binary representation.
   * Convert the binary byte sequence `11011010 10111110 11101111 00000000` into Hexadecimal.
3. Explain how negative numbers are stored in memory using **Two's Complement (2'ye Tümleyen)**. For a signed 8-bit integer `char`, what are the binary and hexadecimal representations of `-1`, `-128`, and `+127`?

---

### Question 2: Memory Endianness, Byte Inspection & Pointer Type Casting
**Topic:** Little-Endian vs. Big-Endian, `char*` Pointer Traversal, and Memory Dumping

Suppose you allocate a 32-bit unsigned integer variable in C:
```c
uint32_t val = 0x1A2B3C4D;
```
Assume this variable is stored at memory address `0x7FFF0000`.

1. Contrast **Little-Endian** and **Big-Endian** memory architectures. Draw or illustrate the exact byte values (in Hex and Binary) stored at each memory address from `0x7FFF0000` to `0x7FFF0003` for both architectures:
   * `0x7FFF0000`: `?`
   * `0x7FFF0001`: `?`
   * `0x7FFF0002`: `?`
   * `0x7FFF0003`: `?`
2. Explain how a programmer can inspect the individual raw bytes of `val` in C by casting its pointer to a byte pointer (`unsigned char *` or `uint8_t *`).
   * Compare `int *ptr = &val; ptr++;` vs `unsigned char *cptr = (unsigned char*)&val; cptr++;` in terms of memory stride (address increment in bytes).
3. What is **Memory Alignment** and **Structure Padding**? Why does a 64-bit CPU require a 4-byte or 8-byte boundary alignment for memory accesses, and what happens at the hardware/performance level during unaligned memory accesses?

---

### Question 3: Bitwise Manipulation & Flag Register Extraction via Pointers
**Topic:** Bitwise Operators (`&`, `|`, `^`, `~`, `<<`, `>>`), Bitmasking, and Low-Level Data Packing

1. Explain the operational mechanics of the bitwise operators in C:
   * **AND (`&`)** for masking and clearing specific bits.
   * **OR (`|`)** for setting specific bits.
   * **XOR (`^`)** for toggling bits.
   * **NOT (`~`)** for inverting bit patterns.
   * **Shifts (`<<`, `>>`)** for bit alignment and arithmetic scaling.
2. Suppose an embedded sensor packs three readings into a single 16-bit word (`uint16_t packet = 0xB57C`):
   * **Bit 15..12 (Upper 4 bits):** Device ID
   * **Bit 11..4 (Middle 8 bits):** Temperature Reading
   * **Bit 3..0 (Lower 4 bits):** Status Flags  
   Write the exact C expressions (using bitwise masking `&` and bit-shifting `>>`) to extract each of the three fields from `packet`.
3. Explain why bitwise operations combined with pointer dereferencing are fundamental in building high-performance systems, custom memory allocators, and hardware driver interfaces.

---

## 📄 Word (.docx) Submission Template

Copy and paste the template below into your Microsoft Word document:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN UNIVERSITY
                            FACULTY OF ENGINEERING
                       DEPARTMENT OF SOFTWARE ENGINEERING
                     SE2009 - PRELAB 02 EVALUATION REPORT
================================================================================

STUDENT INFORMATION:
• Full Name: [Your Name and Surname]
• Student ID: [Your Student ID]
• GitHub Username: se-[Your Student ID]
• Private Lab Repo: https://github.com/se2009-26/Lab_se-[Your Student ID]
• Submission Date: [06/10/2026]
• OneDrive View/Download Link: [Insert Link - View & Download Only]

--------------------------------------------------------------------------------
IMPORTANT NOTICE:
Submission Deadline: October 6, 2026, 23:59.
Failure to submit this PreLab report by the deadline results in 0 points 
(no lab grade) for this lab session.
--------------------------------------------------------------------------------

SECTION 1: CONCEPTUAL EVALUATION QUESTIONS

[QUESTION 1: Binary, Hexadecimal & Byte Addressing in Memory]
Your Answer:
(Explain byte addressability, why Hex is used for memory, show the conversions 
between Binary and Hex for 0x4A3F2C10 and byte streams, and explain Two's 
Complement representations for -1, -128, +127.)


[QUESTION 2: Memory Endianness, Byte Inspection & Pointer Type Casting]
Your Answer:
(Provide the Little-Endian vs Big-Endian byte layout diagrams for 0x1A2B3C4D 
at address 0x7FFF0000, explain uint8_t* pointer casting for memory dumps, 
and explain memory alignment/padding.)


[QUESTION 3: Bitwise Operations & Flag Extraction via Pointers]
Your Answer:
(Explain bitwise operators &, |, ^, ~, <<, >>, write the bit-masking and shift 
expressions to unpack the 16-bit packet 0xB57C, and explain low-level system 
applications.)


--------------------------------------------------------------------------------

SECTION 2: KEY TAKEAWAYS & SUMMARY
• Summarize the two most critical insights you gained regarding binary/hex memory 
  representation and byte-level pointer manipulation (2–3 sentences):
  [Your Summary...]

================================================================================
```
