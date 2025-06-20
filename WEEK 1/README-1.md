# WEEK 1 tasks
# 1) 🛠️ Setting Up RISC-V Toolchain on Ubuntu
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
This guide walks through the unpacking, setup, and verification of the `riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14.tar.gz`.

---

## 📦 Step 1: Extract the Toolchain Archive

Download the archive (if not already), then extract it:

```bash
tar -xvzf riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14.tar.gz
```

👉 *You should see a new directory created. It will typically contain a `bin/` folder with the RISC-V binaries.*

---

## 🛣️ Step 2: Add Toolchain to PATH

### 🔍 First, find the `bin/` directory:

Example (adjust based on your extracted folder):

```bash
cd riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14
pwd  # Note this full path
```

Assume the full path is:

```bash
/home/yourusername/riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14/bin
```

### ✅ Add it to your PATH:

#### For current session:

```bash
export PATH=/home/yourusername/riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14/bin:$PATH
```

#### To make it permanent:

```bash
echo 'export PATH=/home/yourusername/riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

✅ *Replace the path with your actual directory if different.*

---

## ✅ Step 3: Verify Installation

Run the following commands to verify everything is working:

```bash
riscv64-unknown-elf-gcc --version
```

```bash
riscv64-unknown-elf-objdump --version
```

```bash
riscv64-unknown-elf-gdb --version
```

Each command should output version info similar to:

```bash
riscv64-unknown-elf-gcc (GCC) 10.2.0
```

</p> </details>

## Outputs (task 1):

![image](https://github.com/user-attachments/assets/4411e65c-4fa0-424d-bc15-751acd7f91c8)


# 2) 🧪 Minimal C Hello World for RISC-V RV32IMC
<details>
  <summary> <b> 🔍 Documentation </b> </summary>

This task demonstrates how to write and cross-compile a minimal C program for the **RISC-V 32-bit RV32IMC** architecture using the RISC-V GNU toolchain.

---

## 📄 Step 1: Create `hello.c`

```c
#include <stdio.h>

int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
```

Save this file as `hello.c`.

---

## ⚙️ Step 2: Compile for RV32IMC

Run the following command using the RISC-V GCC:

```bash
riscv64-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -o hello.elf hello.c
```

* `-march=rv32imc`: Target RISC-V 32-bit with IMC extensions
* `-mabi=ilp32`: Use the 32-bit integer ABI
* `-o hello.elf`: Output file in ELF format

---

## 🧾 Step 3: Confirm Output ELF Format

Use the `file` command to verify:

```bash
file hello.elf
```

✅ Expected output (example):

```
hello.elf: ELF 32-bit LSB executable, UCB RISC-V, version 1 (SYSV), statically linked, not stripped
```

This confirms that the binary is a valid **32-bit RISC-V ELF**.

---
</details>

## Outputs (task 2):
![image](https://github.com/user-attachments/assets/d198d6fb-3507-4757-8e2d-d4cf591e8446)


# 3) 🔧 From C to Assembly (Function Prologue/Epilogue)
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>
---



This task shows how to generate assembly code from a C file using RISC-V GCC and understand the **prologue and epilogue** of the `main` function.

---

## 🎯 Objective

* Generate a `.s` file (assembly output)
* Understand key instructions like `addi sp, sp, -16`, `sw ra, 12(sp)`, etc.

---

## 📝 Step 1: Generate Assembly from C

Make sure you're in the folder with `hello.c`.

Run:

```bash
riscv64-unknown-elf-gcc -S -O0 -march=rv32imc -mabi=ilp32 hello.c
```

* `-S`: Output assembly (`hello.s`)
* `-O0`: No optimization (easier to read)
* `-march=rv32imc`: Target RV32IMC architecture
* `-mabi=ilp32`: Use 32-bit ABI

---

## 📄 Step 2: Inspect `hello.s`

You will now have a file named `hello.s`. Open it:

```bash
cat hello.s
```

Inside, you'll see the `main:` function begin like this (your exact line numbers may vary):

```asm
main:
  addi    sp,sp,-16
  sw      ra,12(sp)
  sw      s0,8(sp)
  addi    s0,sp,16
```

And end like this:

```asm
  lw      ra,12(sp)
  lw      s0,8(sp)
  addi    sp,sp,16
  ret
```

---

## 🧠 Step 3: Explanation — Function Prologue and Epilogue

### ✅ Prologue

```asm
addi    sp,sp,-16     # Allocate 16 bytes on the stack
sw      ra,12(sp)     # Save return address (ra) at offset 12
sw      s0,8(sp)      # Save frame pointer (s0) at offset 8
addi    s0,sp,16      # Set frame pointer (s0 = old sp)
```

💡 This sets up the stack frame:

* Reserves space for local variables and saved registers.
* Preserves return address and previous frame pointer.

---

### ✅ Epilogue

```asm
lw      ra,12(sp)     # Restore return address
lw      s0,8(sp)      # Restore frame pointer
addi    sp,sp,16      # Deallocate stack space
ret                   # Return to caller using ra
```

💡 This restores the stack to its original state before the function call.

---
</p></details>

## Outputs (task 3):
![image](https://github.com/user-attachments/assets/85ee8cdb-1512-401a-b13e-ea2b88d7ab11)



# 4) 🔍 Hex Dump & Disassembly of RISC-V ELF
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

This task explains how to:

* Disassemble a RISC-V ELF file
* Convert it into raw Intel HEX format
* Understand each part of an instruction in the disassembly

---

## 📦 Step 1: Disassemble the ELF

Use `objdump` to generate a readable assembly listing:

```bash
riscv64-unknown-elf-objdump -d hello.elf > hello.dump
```

This creates a `hello.dump` file containing the disassembled code.

To view it in terminal:

```bash
cat hello.dump
```

---

## 📤 Step 2: Convert ELF to Raw Intel HEX Format

Use `objcopy`:

```bash
riscv64-unknown-elf-objcopy -O ihex hello.elf hello.hex
```

This converts `hello.elf` into `hello.hex`, a raw machine-readable hex file useful for flashing to microcontrollers or viewing raw memory.

---

## 🔍 Step 3: Understand the Disassembly Output

A typical instruction in `hello.dump` might look like this:

```asm
80000074:  1141        addi sp,sp,-16
```

Here’s what each part means:

| Field            | Description                                                   |
| ---------------- | ------------------------------------------------------------- |
| `80000074:`      | Address in memory where this instruction resides              |
| `1141`           | Raw hexadecimal encoding of the instruction (opcode+operands) |
| `addi sp,sp,-16` | Assembly mnemonic + operands — what the CPU actually executes |

---

## 🧠 Example Breakdown: `1141` = `addi sp, sp, -16`

* **Instruction Type**: I-type (immediate)
* **Mnemonic**: `addi` → Add immediate value to register
* **Operands**:

  * `sp`: destination register
  * `sp`: source register
  * `-16`: immediate value

This instruction decreases the stack pointer by 16 bytes — typical in function prologues to allocate space on the stack.

</p></details>

## Outputs (task 4):
![image](https://github.com/user-attachments/assets/514c9a34-46ab-4fd7-9c96-4da835ca2e4c)
![image](https://github.com/user-attachments/assets/c42e7a2c-c98d-4b18-8d01-e605a29cbd62)


# 5) 🧾 RISC-V RV32I Register ABI & Calling Convention Cheat-Sheet

This task lists all **32 general-purpose registers** in the **RV32I** architecture, their **ABI names**, and their **function in the calling convention**.

## 🗃️ Register Table (x0–x31)

| Register | ABI Name | Role / Description                            |
| -------- | -------- | --------------------------------------------- |
| x0       | zero     | Constant zero                                 |
| x1       | ra       | Return address (caller-saved)                 |
| x2       | sp       | Stack pointer                                 |
| x3       | gp       | Global pointer                                |
| x4       | tp       | Thread pointer                                |
| x5       | t0       | Temporary (caller-saved)                      |
| x6       | t1       | Temporary (caller-saved)                      |
| x7       | t2       | Temporary (caller-saved)                      |
| x8       | s0/fp    | Saved register / Frame pointer (callee-saved) |
| x9       | s1       | Saved register (callee-saved)                 |
| x10      | a0       | Argument 0 / Return value 0 (caller-saved)    |
| x11      | a1       | Argument 1 / Return value 1 (caller-saved)    |
| x12      | a2       | Argument 2 (caller-saved)                     |
| x13      | a3       | Argument 3 (caller-saved)                     |
| x14      | a4       | Argument 4 (caller-saved)                     |
| x15      | a5       | Argument 5 (caller-saved)                     |
| x16      | a6       | Argument 6 (caller-saved)                     |
| x17      | a7       | Argument 7 (caller-saved)                     |
| x18      | s2       | Saved register (callee-saved)                 |
| x19      | s3       | Saved register (callee-saved)                 |
| x20      | s4       | Saved register (callee-saved)                 |
| x21      | s5       | Saved register (callee-saved)                 |
| x22      | s6       | Saved register (callee-saved)                 |
| x23      | s7       | Saved register (callee-saved)                 |
| x24      | s8       | Saved register (callee-saved)                 |
| x25      | s9       | Saved register (callee-saved)                 |
| x26      | s10      | Saved register (callee-saved)                 |
| x27      | s11      | Saved register (callee-saved)                 |
| x28      | t3       | Temporary (caller-saved)                      |
| x29      | t4       | Temporary (caller-saved)                      |
| x30      | t5       | Temporary (caller-saved)                      |
| x31      | t6       | Temporary (caller-saved)                      |

---

## 📋 RISC-V Calling Convention Summary

| ABI Group | Registers      | Description                        |
| --------- | -------------- | ---------------------------------- |
| `zero`    | x0             | Always 0 — hardwired               |
| `ra`      | x1             | Return address — caller saves      |
| `sp`      | x2             | Stack pointer                      |
| `gp/tp`   | x3, x4         | Global/thread pointer              |
| `a0–a7`   | x10–x17        | Function arguments / return values |
| `t0–t6`   | x5–x7, x28–x31 | Temporaries — **caller-saved**     |
| `s0–s11`  | x8–x9, x18–x27 | Saved registers — **callee-saved** |

---

## 🧠 Quick Tip

* **Caller-saved**: Must be saved **by the caller** before calling another function if they’re needed after the call.
* **Callee-saved**: Must be preserved **by the callee** if it uses them.


# 6) 🐞 Stepping Through RISC-V ELF with GDB
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

This task demonstrates how to debug a cross-compiled RISC-V ELF file using the built-in **GDB simulator**, set breakpoints, step through code, and inspect registers.

---

## 📂 Prerequisites

* Ensure `hello.elf` is compiled with debug info:

```bash
riscv64-unknown-elf-gcc -g -march=rv32imc -mabi=ilp32 -o hello.elf hello.c
```

---

## 🧪 Step-by-Step Debugging with GDB

### 1️⃣ Launch GDB on the ELF file

```bash
riscv64-unknown-elf-gdb hello.elf
```

### 2️⃣ In GDB, connect to the built-in simulator

```
(gdb) target sim
```

### 3️⃣ Set a breakpoint at `main`

```
(gdb) break main
```

Output should say:
`Breakpoint 1 at 0x...: file hello.c, line 3.`

### 4️⃣ Start program execution

```
(gdb) run
```

The program stops at the start of `main`.

### 5️⃣ Step through instructions

```bash
(gdb) step       # Step into function
(gdb) next       # Step over lines
(gdb) disassemble
```

### 6️⃣ Inspect register values

```bash
(gdb) info registers
(gdb) info reg a0
```

This shows the current values of general-purpose and argument registers.

---

## 🧠 Useful GDB Commands Summary

| Command         | Description                        |
| --------------- | ---------------------------------- |
| `target sim`    | Use the GDB simulator backend      |
| `break main`    | Set a breakpoint at `main()`       |
| `run`           | Start the program                  |
| `step` / `next` | Step into / step over instructions |
| `info reg`      | Display all register values        |
| `info reg a0`   | Show just the `a0` register        |
| `disassemble`   | Show disassembled code around PC   |
| `x/i $pc`       | Examine current instruction        |

---

</p></details>

## Outputs (task 6):
![image](https://github.com/user-attachments/assets/2bb47397-d073-42c7-a15c-86ed116f2f0b)
![image](https://github.com/user-attachments/assets/af9289c6-f405-4397-95c7-e50f713ec1c6)
![image](https://github.com/user-attachments/assets/cb75a31b-930d-44e5-b323-80b629314cec)
![image](https://github.com/user-attachments/assets/0e870432-9d2a-46ca-bd1e-cf8606ee9390)


# 7) 🔧 Bare-Metal RISC-V on QEMU
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>  

Minimal example to verify a RISC-V bare-metal environment using QEMU.  

## Code (`hello2.c`)  
```c
/* Minimal bare-metal RISC-V program */
int main() {
    while (1); // Infinite loop = success (no crash)
    return 0;
}
```

## Build & Run  
### Compile  
```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -nostdlib -o hello2.elf hello2.c
```  
- **`-nostdlib`**: No standard libraries (pure bare-metal).  
- **`rv32imac`**: Targets 32-bit RISC-V with Multiply/Atomics/Compressed extensions.  

### Run in QEMU  
```bash
qemu-system-riscv32 -nographic -machine sifive_e -kernel hello2.elf -bios none
```  
- **`-nographic`**: Output to terminal (no GUI).  
- **`sifive_e`**: Emulates SiFive E-series board (RAM at `0x80000000`).  
- **`-bios none`**: Skips firmware (executes `hello.elf` directly).  

### Expected Behavior  
- Silent infinite loop = ✅ Success (no crashes).  
- **Exit QEMU**: Press `Ctrl+A`, then `X`.  

## Why This Works  
1. **No Dependencies**: `-nostdlib` avoids CRT startup code.  
2. **Default RAM**: QEMU’s `sifive_e` machine pre-initializes memory.  
3. **Entry Point**: QEMU jumps directly to `main()`.  

---

### Next Steps  
Add UART initialization for actual outputs
</p></details>

## Outputs (task 7):
![image](https://github.com/user-attachments/assets/bf898e46-beac-4b9a-97c9-0ead38509812)


# 8) 🧪 Exploring GCC Optimisation
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

### ❓ Question

**“Compile the same file with `-O0` vs `-O2`. What differences appear in the assembly and why?”**

---

### 📄 Source Code (`hello4.c`)

```c
#define UART0 0x10000000

void uart_putc(char c) {
    volatile char *tx = (char *)UART0;
    *tx = c;
}

void uart_puts(const char *s) {
    while (*s) {
        uart_putc(*s++);
    }
}

void _start() {
    uart_puts("Hello, UART!\n");
    while (1);
}
```

---

### 🧵 Step 1: Compile with No Optimization (`-O0`)

```bash
riscv64-unknown-elf-gcc -S -O0 -march=rv32imc -mabi=ilp32 hello4.c -o hello4_O0.s
```

> 🔍 This generates verbose, unoptimized assembly. Easy to read, but includes redundant code.

---

### ⚡ Step 2: Compile with Optimization (`-O2`)

```bash
riscv64-unknown-elf-gcc -S -O2 -march=rv32imc -mabi=ilp32 hello4.c -o hello4_O2.s
```

> 🔍 This generates highly optimized assembly: fewer instructions, more registers, tighter loops.

---

### 🧠 Step 3: Compare Both `.s` Files

#### Example: `hello4_O0.s` (Excerpt)

```asm
_start:
    addi    sp,sp,-16
    sw      ra,12(sp)
    ...
    call    uart_puts
    ...
```

#### Example: `hello4_O2.s` (Excerpt)

```asm
_start:
    li      a5,268435456   # UART0
    li      a4,72          # 'H'
    sb      a4,0(a5)
    ...
```

---

### 🧠 Explanation of Differences

| Optimization | Observed Change          | Explanation                                                               |
| ------------ | ------------------------ | ------------------------------------------------------------------------- |
| `-O0`        | Function calls preserved | GCC does not inline or optimize — keeps all calls literal.                |
| `-O2`        | Functions inlined        | `uart_puts()` is inlined directly into `_start()` to avoid call overhead. |
| `-O0`        | Stack frame setup        | Adds `sp` adjustments and saves registers regardless of need.             |
| `-O2`        | No stack frame           | Skips unnecessary instructions if safe to do so.                          |
| `-O2`        | Dead code removed        | GCC removes unused variables or unreachable code.                         |
| `-O2`        | Register use optimized   | Efficient use of `a4`, `a5` instead of memory.                            |

---


> 📎 Include side-by-side screenshots or diffs of `hello4_O0.s` vs `hello4_O2.s`
> Use `diff -u hello4_O0.s hello4_O2.s` for easy visual comparison.

---

### ✅ Conclusion

Using `-O2` optimization results in **faster, smaller binaries** by:

* Inlining functions like `uart_puts()`
* Removing unused instructions
* Avoiding unnecessary memory accesses or stack setup

Use `-O0` when debugging, and `-O2` for production/bare-metal use.

Below is a detailed explanation of **Dead-Code Elimination**, **Register Allocation**, and **Inlining**

---

## 🔍 Compiler Optimizations Explained

When compiling with optimization flags like `-O1`, `-O2`, or `-O3`, GCC performs several powerful transformations on your code. Here we explain three common techniques:

---

### 🗑️ 1. Dead-Code Elimination

> **Definition**: Removing code that **does not affect** program output.

#### ✅ Example (Removed Code)

```c
int x = 5;
int y = 10;
int z = x + y; // z is never used
```

GCC will eliminate `z`, and possibly `x` and `y`, because the result is never used.

#### 💡 Why?

* Saves space and runtime
* No side effects — safe to delete

---

### 🧠 2. Register Allocation

> **Definition**: Replacing memory access with **CPU registers** for faster execution.

#### 🔁 Without Optimization (`-O0`)

```asm
lw a0,0(sp)
sw a0,4(sp)
```

#### ⚡ With Optimization (`-O2`)

```asm
mv a1, a0
```

#### 💡 Why?

* Registers are faster than RAM
* Keeps values in CPU for reuse
* Reduces memory traffic

---

### 📦 3. Inlining

> **Definition**: Replacing a function call with its **actual body**, avoiding call overhead.

#### ❌ Without Inlining

```c
uart_putc('A');  // Function call
```

```asm
call uart_putc
```

#### ✅ With Inlining

```asm
li a0, 'A'
sb a0, 0(a1)     // Direct write to UART memory
```

#### 💡 Why?

* Speeds up small, frequently-used functions
* Saves function-call overhead (like stack/return)
* Enables further optimization inside the function body

---

### 📊 Summary Table

| Optimization          | Benefit                                            | Risk/Tradeoff                    |
| --------------------- | -------------------------------------------------- | -------------------------------- |
| Dead-code Elimination | Smaller binary, faster execution                   | Might remove debugging variables |
| Register Allocation   | Faster runtime, fewer loads/stores                 | Limited by number of registers   |
| Inlining              | Removes call overhead, enables deeper optimization | Can increase code size           |

---

✅ These techniques combined make your **RISC-V ELF faster, tighter, and better** suited for bare-metal execution.

</p></details>

## Outputs (task 8):
![image](https://github.com/user-attachments/assets/80dc3a3c-d304-474e-90bb-f36433f7906e)
![image](https://github.com/user-attachments/assets/74ed9179-6fc0-4e17-87de-12ce64435571)


# 9) 🔧 Inline Assembly Basics
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

### 📄 Source Code

```c
#include <stdint.h>

static inline uint32_t rdcycle(void) {
    uint32_t c;
    asm volatile (
        "csrr %0, cycle"      /* Read the 64‑bit cycle CSR into a 32‑bit register */
        : "=r"(c)             /* Output operand: place the result in C variable 'c' */
        :                     /* No input operands */
        :                     /* No clobbered registers */
    );
    return c;

````

---

### 🧠 Explanation of Each Part

| Syntax               | Meaning |
| -------------------- | ------- |
| `asm volatile (...)` |         |

* **`asm`**: Insert inline assembly.
* **`volatile`**: Prevents the compiler from optimizing away or reordering this asm block. Ensures it executes exactly where placed.
  |
  \| `"csrr %0, cycle"`       | The assembly instruction: read the **cycle** CSR (number 0xC00) into the register `%0`. |
  \| `: "=r"(c)`              | **Output constraint**:
* **`=`** marks it as write‑only.
* **`r`** requests the compiler choose a general‑purpose register.
* `(c)` binds that register to the C variable `c`.
  |
  \| *(empty)*                | **Input operands**: none needed here.                                    |
  \| *(empty)*                | **Clobbers**: none declared (we only modify the output register).       |

---

### 📋 Quick Tips

* **CSR names** (like `cycle`) are aliases for their numeric identifiers (0xC00) in RISC‑V’s assembler.
* Always mark **hardware‑accessing asm** blocks as `volatile` so the compiler doesn’t remove or reorder them.
* The **`r`** constraint is the most common: it lets the compiler pick any register.
* If you needed multiple outputs or inputs, you’d list them as
  `: "=r"(out1), "=r"(out2) : "r"(in1), "r"(in2) : "memory", "cc"`


</p></details>

## Outputs (task 9):
![image](https://github.com/user-attachments/assets/68cd2cd7-eefc-41c0-b1f7-a91d1e1b8024)



# 10) 🔌 Memory-Mapped I/O Demo — GPIO Toggle
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

This is a minimal bare-metal C snippet that toggles a GPIO register located at address `0x10012000`.

## 📜 Description

We write `0x1` to the memory-mapped GPIO register. The `volatile` keyword ensures the compiler does **not optimize away** this memory operation.

## 📂 File Structure

```

.
├── gpio\_toggle.c       # Main C file
├── linker.ld           # Linker script
└── Makefile            # Build file (for riscv32 GCC)

````

## 🧠 Code Explanation

```c
// gpio_toggle.c
#include <stdint.h>

#define GPIO_ADDR 0x10012000

int main() {
    volatile uint32_t *gpio = (uint32_t *)GPIO_ADDR;
    *gpio = 0x1;  // Write 1 to GPIO register
    while (1);    // Infinite loop to keep program alive
    return 0;
}
````

* `volatile` prevents compiler optimization of the memory store.
* `uint32_t*` is used to match the alignment (4 bytes).

## 🧩 Linker Script

```ld
/* linker.ld */
ENTRY(_start)

SECTIONS
{
    . = 0x80000000;
    .text : { *(.text*) }
    .data : { *(.data*) }
    .bss  : { *(.bss*)  }
    /DISCARD/ : { *(.comment*) *(.note*) }
}
```

* Places code at 0x80000000 (a typical bare-metal starting address).

## 🛠️ Makefile

```make
# Makefile

CROSS = riscv32-unknown-elf
CFLAGS = -Wall -O0 -nostdlib -nostartfiles

all: gpio_toggle.elf

gpio_toggle.elf: gpio_toggle.c linker.ld
	$(CROSS)-gcc $(CFLAGS) -T linker.ld -o $@ gpio_toggle.c

clean:
	rm -f *.elf
```

## ▶️ How to Build and Run (QEMU)

```bash
make
qemu-system-riscv32 -nographic -machine sifive_e -kernel gpio_toggle.elf
```

You can use `spike` or `gdb` if targeting a different board.

## 🧪 Output

You won't see any print, but if the address is mapped to UART or LED, you’ll see the effect (e.g., LED toggles, UART fires).

---

## 📘 Notes

* The **`volatile`** keyword tells the compiler that the value at `*gpio` can change outside the program’s control — so don’t optimize it away.
* This is essential for interacting with **memory-mapped I/O** like GPIOs, UARTs, and timers.

---

Let me know if you'd like the same structure with multiple toggles or with delay added!


### 📘 Notes

* The **volatile** keyword is critical when accessing memory-mapped registers to ensure correctness.
* You must ensure the address `0x10012000` is **word-aligned** (which it is) for 32-bit access.


</p></details>

## Outputs (task 10):
![image](https://github.com/user-attachments/assets/5597702d-a57a-4adb-ac1b-06fb9c80715d)


# 11) 🔗 Linker Script 101 — RISC-V Bare-Metal (RV32IMC)
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>
To accompany a **minimal bare-metal project** on RV32IMC. It includes a simple linker script that places `.text` at `0x00000000` (Flash) and `.data` at `0x10000000` (SRAM), with a brief explanation of why those regions differ


This project demonstrates how to use a minimal **linker script** to place different memory sections (`.text`, `.data`) at specific addresses on an RV32IMC target.

---

## 🧱 Memory Layout

| Section  | Address      | Purpose           |
|----------|--------------|-------------------|
| `.text`  | `0x00000000` | Flash (code)      |
| `.data`  | `0x10000000` | SRAM (initialized data) |

---

## 📄 Minimal Linker Script (`linker.ld`)

```ld
ENTRY(_start)

SECTIONS {
  /* Code and read-only data */
  .text 0x00000000 : {
    *(.text*)
    *(.rodata*)
  }

  /* Initialized data */
  .data 0x10000000 : {
    *(.data*)
  }

  /* Uninitialized data (BSS) */
  .bss (NOLOAD) : {
    *(.bss*)
  }
}
````

---

## ⚙️ Build Example

Assuming you have a `task11.c`, build it like this:

```bash
riscv64-unknown-elf-gcc -o prog.elf task11.c -nostartfiles -Wl,-Tlink.ld -march=rv32imc -mabi=ilp32
```

---

## 🔍 Flash vs SRAM: Why Different?

* **Flash (`0x00000000`)** is non-volatile memory used to store program code permanently.
* **SRAM (`0x10000000`)** is fast, volatile memory used for variables during execution.
* Separating code and data ensures:

  * Faster access to variables (SRAM is faster than Flash)
  * Write safety (Flash is often read-only during execution)
  * Compatibility with embedded startup routines (copying `.data` from Flash to SRAM)

---

## 🧪 Running with QEMU (Optional)

If simulating:

```bash
qemu-system-riscv32 -nographic -machine virt -kernel prog.elf
```

> Ensure your `.text` and `.data` regions match what the simulated SoC expects. You may need to adjust addresses or use a matching QEMU device like `virt`.

</p></details>

## Outputs (task 11):
![image](https://github.com/user-attachments/assets/14c4cfde-ba0a-4d47-a964-755aefcd010a)


# 12) 🔧  Bare-Metal RISC-V Startup (`crt0.S`) with UART
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>
## 📁 Project Structure

```
task12_startup_uart/
├── crt0.S
├── link.ld
├── task12.c
├── Makefile
└── README.md

# Task 12 — Bare-Metal RISC-V Startup (`crt0.S`) with UART

This example demonstrates how to use a minimal `crt0.S` startup file in a bare-metal RISC-V program. It:
- Sets up the stack
- Zeroes the `.bss` section
- Calls `main()`
- Loops forever after `main()` returns

UART output is used to verify correct execution.

## 🔧 Files
- `crt0.S` — Startup assembly
- `task12.c` — Simple UART test using `.text`, `.data`, and `.bss`
- `link.ld` — Minimal linker script for RV32IMC
- `Makefile` — Build automation

## ⚙️ Build & Run

```bash
make
qemu-system-riscv32 -nographic -machine virt -kernel task12.elf -bios none
````

Expected Output:

```
Hello from crt0!
Counter: 1
```


---

## 🧠 `crt0.S`

```asm
# crt0.S — Minimal RISC-V startup code

.section .text
.global _start
_start:
    # Set up the stack pointer
    la sp, _stack_top

    # Zero out the .bss section
    la a0, __bss_start
    la a1, __bss_end
1:
    bge a0, a1, 2f
    sw zero, 0(a0)
    addi a0, a0, 4
    j 1b
2:

    # Call main
    call main

    # Infinite loop after main returns
3:  j 3b

# Define stack size and location
.section .bss
.space 1024  # 1 KB stack
.global _stack_top
_stack_top:
````

---

## 💻 `task12.c`

```c
// task12.c — Test UART + crt0 startup

#include <stdint.h>

#define UART_BASE 0x10000000
#define UART_TXDATA (volatile uint32_t *)(UART_BASE + 0x00)

// .data
volatile int counter = 1;

// .bss
volatile int uninitialized_var;

void uart_putc(char c) {
    while (*UART_TXDATA & 0x80000000);
    *UART_TXDATA = c;
}

void uart_print(const char *s) {
    while (*s) uart_putc(*s++);
}

void uart_print_num(int n) {
    if (n == 0) { uart_putc('0'); return; }

    char buf[10];
    int i = 0;
    while (n > 0) {
        buf[i++] = '0' + (n % 10);
        n /= 10;
    }
    while (i--) uart_putc(buf[i]);
}

int main() {
    uart_print("Hello from crt0!\n");
    uart_print("Counter: ");
    uart_print_num(counter);
    uart_putc('\n');
    return 0;
}
```

---

## 📜 `link.ld`

```ld
ENTRY(_start)

SECTIONS {
  . = 0x00000000;

  .text : {
    *(.text*)
    *(.rodata*)
  }

  .data : AT(ADDR(.text) + SIZEOF(.text)) {
    __data_start = .;
    *(.data*)
    __data_end = .;
  }

  .bss : {
    __bss_start = .;
    *(.bss*)
    *(COMMON)
    __bss_end = .;
  }
.stack (NOLOAD) : {
    . = ALIGN(4);
    _stack_top = . + 0x400;  /* 1KB stack */
    PROVIDE(_stack_top = .);
}

}
```

</p></details>

## Outputs (task 12):
![image](https://github.com/user-attachments/assets/e75d8cd1-b099-4ded-8bb3-360806234e5e)

# 13) Machine Timer Interrupt
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

## 📁 Project Structure (Minimal GitHub-like Layout)

```
task13/
├── main.c
├── interrupt.c
├── crt0.S
├── link.ld
└── Makefile
```

---

## 🧠 What This Demo Does

* Initializes **machine timer interrupt**
* Sets `mtimecmp` to fire after delay
* Enables interrupts via `mie` and `mstatus`
* Blinks a flag in the interrupt handler (prints `"TIMER INTERRUPT"` repeatedly using UART)

---

## ✅ `main.c` – Enable Timer Interrupt

```c
#include <stdint.h>
#include "uart.h"

#define MTIME       (*(volatile uint64_t *)(0x0200bff8))
#define MTIMECMP    (*(volatile uint64_t *)(0x02004000))
#define TIMER_DELAY 1000000UL

void trap_handler(void) __attribute__((interrupt));

volatile int timer_flag = 0;

void trap_handler(void) {
    MTIMECMP = MTIME + TIMER_DELAY;
    timer_flag = 1;
}

int main() {
    uart_init();
    uart_puts("Starting timer interrupt demo...\n");

    // Schedule timer
    MTIMECMP = MTIME + TIMER_DELAY;

    // Enable machine-timer interrupt
    asm volatile("csrs mie, %0" :: "r"(1 << 7)); // MTIE
    asm volatile("csrs mstatus, %0" :: "r"(1 << 3)); // MIE

    while (1) {
        if (timer_flag) {
            timer_flag = 0;
            uart_puts("TIMER INTERRUPT\n");
        }
	return 0;
    }
}
```

---

## ✅ `uart.h` (Add to same folder)

```c
#ifndef UART_H
#define UART_H

void uart_init();
void uart_putc(char c);
void uart_puts(const char *s);

#endif
```

## ✅ `uart.c`

```c
#include <stdint.h>
#include "uart.h"
#define UART_TX  (*(volatile uint32_t*)0x10000000)

void uart_init() {
    // No init needed for simple MMIO
}

void uart_putc(char c) {
    UART_TX = c;
}

void uart_puts(const char *s) {
    while (*s) uart_putc(*s++);
}
```

---

## ✅ `crt0.S`

```asm
.section .text
.global _start
_start:
    la sp, _stack_top
    call main
    j .
```

---

## ✅ `link.ld`

```ld
ENTRY(_start)

MEMORY {
  RAM (rwx) : ORIGIN = 0x80000000, LENGTH = 64K
}

SECTIONS {
  .text : {
    *(.text*)
  } > RAM

  .bss : {
    __bss_start = .;
    *(.bss*)
    __bss_end = .;
  } > RAM

  .data : {
    *(.data*)
  } > RAM

  .stack : {
    . = ALIGN(4);
    _stack_top = . + 0x1000;
  } > RAM
}
```

---

## ✅ `Makefile`

```makefile
CC = riscv64-unknown-elf-gcc
CFLAGS = -march=rv32imc -mabi=ilp32 -nostartfiles -Wall

all: task13.elf

task13.elf: crt0.S main.c uart.c
	$(CC) $(CFLAGS) -Tlink.ld $^ -o $@

clean:
	rm -f *.elf *.o
```

---

## ▶️ To Build and Run

```bash
make
qemu-system-riscv32 -nographic -machine sifive_e -kernel task13.elf
```

You should see output like:

```
Starting timer interrupt demo...
TIMER INTERRUPT
TIMER INTERRUPT
TIMER INTERRUPT
...
```


</p></details>

## Outputs (task 13):
![image](https://github.com/user-attachments/assets/dab1e61e-b6f9-4143-9b61-45338b1cbfdc)


# 14) 🧠 `rv32imac` vs `rv32imc` — What’s the “A”?
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

### 📌 Question

**“Explain the ‘A’ (atomic) extension in rv32imac. What instructions are added and why are they useful?”**

---

## ✅ Summary

The **‘A’ extension** in RISC-V stands for **Atomic Instructions** and is crucial for writing **multi-threaded, lock-free programs**, including **operating system kernels**, device drivers, and concurrent embedded systems.

---

### 🆚 rv32imc vs rv32imac

| Feature        | `rv32imc`   | `rv32imac`  |
| -------------- | ----------- | ----------- |
| Integer Core   | ✅ Yes (`I`) | ✅ Yes (`I`) |
| Multiplication | ✅ Yes (`M`) | ✅ Yes (`M`) |
| Compression    | ✅ Yes (`C`) | ✅ Yes (`C`) |
| Atomics        | ❌ No        | ✅ Yes (`A`) |

---

## 📜 Atomic Instructions (`A` Extension)

### ✅ Major Instructions

| Instruction | Description                    |
| ----------- | ------------------------------ |
| `lr.w`      | Load-Reserved (for a word)     |
| `sc.w`      | Store-Conditional (for a word) |
| `amoadd.w`  | Atomic Add                     |
| `amoswap.w` | Atomic Swap                    |
| `amoxor.w`  | Atomic XOR                     |
| `amoor.w`   | Atomic OR                      |
| `amoand.w`  | Atomic AND                     |
| `amomin.w`  | Atomic Min (signed)            |
| `amomax.w`  | Atomic Max (signed)            |
| `amominu.w` | Atomic Min (unsigned)          |
| `amomaxu.w` | Atomic Max (unsigned)          |

---

## 🧩 Why Atomics Matter

### 🔒 Use Case: Lock-Free Synchronization

The `A` extension allows developers to:

* Implement **mutexes**, **spinlocks**, **semaphores**
* Safely access shared memory in **multi-core** systems
* Use **lock-free data structures** like queues, stacks, etc.

---

## ⚙️ Example: Spinlock with `lr.w`/`sc.w`

```c
volatile int lock = 0;

void lock_acquire() {
    int tmp;
    do {
        asm volatile (
            "lr.w %[val], (%[addr]);"
            "bnez %[val], 1f;"
            "li %[val], 1;"
            "sc.w %[tmp], %[val], (%[addr]);"
            "1:"
            : [tmp]"=r"(tmp), [val]"+r"(tmp)
            : [addr]"r"(&lock)
            : "memory"
        );
    } while (tmp != 0);
}

void lock_release() {
    lock = 0;
}
```

---

## 🧪 How to Compile with Atomics

Make sure your toolchain supports the `A` extension:

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 ...
```

> ✅ Use `-march=rv32imac` instead of `rv32imc`.



 A working `.c` program to test atomic add or mutex in QEMU?
Here's a **working C example** using **RISC-V atomic add (`amoadd.w`)** that runs on bare-metal and prints results using UART. It simulates **atomic increment** of a shared counter in a loop.

---

## 📁 Directory Structure

```
task14_atomic/
├── Makefile
├── main.c
├── uart.c
├── uart.h
├── linker.ld
├── crt0.S
```

---

## ✅ `main.c` – Atomic Add Test (with UART)

```c
#include "uart.h"

#define SHARED_COUNTER_ADDR  ((volatile int *)0x80001000)

void atomic_increment(volatile int *addr) {
    int tmp;
    asm volatile (
        "amoadd.w %0, x1, (%1)"
        : "=r"(tmp)
        : "r"(addr)
        : "memory"
    );
}

void main() {
    volatile int *shared = SHARED_COUNTER_ADDR;

    *shared = 0; // Init counter

    uart_puts("Atomic Add Demo\n");

    for (int i = 0; i < 5; i++) {
        atomic_increment(shared);
    }

    uart_puts("Final Counter: ");
    uart_print_int(*shared);
    uart_puts("\n");

    while (1);
}
```

---

## ✅ `uart.c` – Basic UART Output

```c
#include "uart.h"
#define UART_ADDR ((volatile unsigned char *)0x10000000)

void uart_putc(char c) {
    *UART_ADDR = c;
}

void uart_puts(const char *s) {
    while (*s) uart_putc(*s++);
}

void uart_print_int(int num) {
    char buf[10];
    int i = 0;

    if (num == 0) {
        uart_putc('0');
        return;
    }

    while (num > 0 && i < 10) {
        buf[i++] = '0' + (num % 10);
        num /= 10;
    }

    while (i--) {
        uart_putc(buf[i]);
    }
}
```

---

## ✅ `uart.h`

```c
#ifndef UART_H
#define UART_H

void uart_putc(char c);
void uart_puts(const char *s);
void uart_print_int(int num);

#endif
```

---

## ✅ `crt0.S` – Minimal Startup

```asm
.section .text
.global _start

_start:
    la sp, stack_top
    call main
    j .

.section .bss
.space 1024
stack_top:
```

---

## ✅ `linker.ld`

```ld
ENTRY(_start)

MEMORY {
    FLASH (rx)  : ORIGIN = 0x00000000, LENGTH = 512K
    RAM   (rwx) : ORIGIN = 0x80000000, LENGTH = 512K
}

SECTIONS {
    .text : {
        *(.text*)
    } > FLASH

    .data : {
        *(.data*)
        *(.rodata*)
    } > RAM

    .bss : {
        *(.bss*)
        *(COMMON)
    } > RAM
}
```

---

## ✅ `Makefile`

```makefile
CC = riscv64-unknown-elf-gcc
CFLAGS = -march=rv32imac -mabi=ilp32 -Wall -O0 -nostdlib
OBJDUMP = riscv64-unknown-elf-objdump

all: atomic.elf

atomic.elf: main.c uart.c crt0.S linker.ld
	$(CC) $(CFLAGS) -T linker.ld $^ -o $@
	$(OBJDUMP) -d atomic.elf > atomic.dump

run: atomic.elf
	qemu-system-riscv32 -nographic -machine virt -bios none -kernel atomic.elf

clean:
	rm -f *.elf *.dump
```

---

## ▶️ Run with QEMU

```bash
make run
```

Here's a **complete set of terminal commands** to **create all required files** and compile + run your **RISC-V timer interrupt demo** using `qemu-system-riscv32` with the `virt` machine.


---

## ✅ Step-by-Step Terminal Commands

```bash
# Step 1: Create and enter working folder
mkdir task13 && cd task13
```

---

### 📄 Create `main.c`

```bash
cat > main.c << 'EOF'
#include <stdint.h>
#include "uart.h"

#define MTIME       (*(volatile uint64_t *)(0x0200bff8))
#define MTIMECMP    (*(volatile uint64_t *)(0x02004000))
#define TIMER_DELAY 1000000UL

void trap_handler(void) __attribute__((interrupt));

volatile int timer_flag = 0;

void trap_handler(void) {
    uart_puts("TRAP!\n");
    MTIMECMP = MTIME + TIMER_DELAY;
    timer_flag = 1;
}

void main() {
    extern void trap_handler();
    asm volatile("csrw mtvec, %0" :: "r"(trap_handler));

    uart_init();
    uart_puts("Starting timer interrupt demo...\n");

    MTIMECMP = MTIME + TIMER_DELAY;

    asm volatile("csrs mie, %0" :: "r"(1 << 7)); // Enable MTIE
    asm volatile("csrs mstatus, %0" :: "r"(1 << 3)); // Enable global MIE

    while (1) {
        if (timer_flag) {
            timer_flag = 0;
            uart_puts("TIMER INTERRUPT\n");
        }
    }
}
EOF
```

---

### 📄 Create `uart.c`

```bash
cat > uart.c << 'EOF'
#include "uart.h"
#define UART_TX  (*(volatile uint32_t*)0x10000000)

void uart_init() {
    // No init required for simple MMIO UART
}

void uart_putc(char c) {
    UART_TX = c;
}

void uart_puts(const char *s) {
    while (*s) uart_putc(*s++);
}
EOF
```

---

### 📄 Create `uart.h`

```bash
cat > uart.h << 'EOF'
#ifndef UART_H
#define UART_H

void uart_init();
void uart_putc(char c);
void uart_puts(const char *s);

#endif
EOF
```

---

### 📄 Create `crt0.S`

```bash
cat > crt0.S << 'EOF'
.section .text
.global _start
_start:
    la sp, _stack_top
    call main
    j .
EOF
```

---

### 📄 Create `link.ld`

```bash
cat > link.ld << 'EOF'
ENTRY(_start)

MEMORY {
  RAM (rwx) : ORIGIN = 0x80000000, LENGTH = 64K
}

SECTIONS {
  .text : {
    *(.text*)
  } > RAM

  .bss : {
    __bss_start = .;
    *(.bss*)
    __bss_end = .;
  } > RAM

  .data : {
    *(.data*)
  } > RAM

  .stack : {
    . = ALIGN(4);
    _stack_top = . + 0x1000;
  } > RAM
}
EOF
```

---

### 📄 Create `Makefile`

```bash
cat > Makefile << 'EOF'
CC = riscv64-unknown-elf-gcc
CFLAGS = -march=rv32imc -mabi=ilp32 -nostartfiles -Wall

all: task13.elf

task13.elf: crt0.S main.c uart.c
	$(CC) $(CFLAGS) -Tlink.ld $^ -o $@

clean:
	rm -f *.elf *.o
EOF
```

---

## 🛠️ Build the Project

```bash
make
```

✅ You should see `task13.elf` generated.

---

## ▶️ Run with QEMU

```bash
qemu-system-riscv32 -machine virt -nographic -bios none -kernel task13.elf
```

---

### ✅ Expected Output:

```text
Starting timer interrupt demo...
TRAP!
TIMER INTERRUPT
TRAP!
TIMER INTERRUPT
...
```


</p></details>

## Outputs (task 14):
![image](https://github.com/user-attachments/assets/7849d649-5723-4836-a063-b73503a45b09)


# 15) 🧪 Atomic Test Program — Spinlock with LR/SC
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

Here’s a **minimal bare-metal C example** that demonstrates a **spinlock using RV32 `lr.w`/`sc.w`** instructions to implement mutual exclusion between two pseudo-threads. This is great for educational use on platforms like QEMU or Spike.

---

### 📁 File: `atomic_test.c`

```c
#include <stdint.h>
#include <stdbool.h>
#include "uart.h"

volatile int lock = 0;

void lock_acquire(volatile int *lock) {
    int tmp;
    do {
        asm volatile (
            "lr.w %[tmp], %[addr]\n"
            "bnez %[tmp], 1f\n"       // if lock != 0, jump to fail
            "li %[tmp], 1\n"
            "sc.w %[tmp], %[tmp], %[addr]\n" // try to store 1 to lock
            "1:"
            : [tmp] "=&r"(tmp), [addr] "+A" (*lock)
            :
            : "memory"
        );
    } while (tmp != 0);
}

void lock_release(volatile int *lock) {
    *lock = 0;
}

void thread_A() {
    uart_puts("Thread A trying to acquire lock...\n");
    lock_acquire(&lock);
    uart_puts("Thread A acquired lock!\n");

    for (volatile int i = 0; i < 100000; i++); // simulate work

    uart_puts("Thread A releasing lock\n");
    lock_release(&lock);
}

void thread_B() {
    uart_puts("Thread B trying to acquire lock...\n");
    lock_acquire(&lock);
    uart_puts("Thread B acquired lock!\n");

    for (volatile int i = 0; i < 100000; i++); // simulate work

    uart_puts("Thread B releasing lock\n");
    lock_release(&lock);
}

int main() {
    uart_init();

    thread_A();
    thread_B();

    while (1);  // Infinite loop to halt
    return 0;
}
```

---

## 📁 File: `uart.h`

```c
#ifndef UART_H
#define UART_H

void uart_init();
void uart_putc(char c);
void uart_puts(const char *s);

#endif
```

---

## 📁 File: `uart.c`

```c
#include <stdint.h>
#include "uart.h"

#define UART_TX (*(volatile uint32_t*)0x10000000)

void uart_init() {}

void uart_putc(char c) {
    UART_TX = c;
}

void uart_puts(const char *s) {
    while (*s) uart_putc(*s++);
}
```

---

## 🧾 Notes

* This is **bare-metal**, no threads — just sequential pseudo-threads for mutex demo.
* Use of `lr.w` (load-reserved) and `sc.w` (store-conditional) implements atomic lock.
* The loop in `lock_acquire` retries until the lock is acquired.

---

## 🛠️ Build and Run (if Makefile exists)

### 🔧 In Terminal:

```bash
make clean
make
```

Then:

```bash
qemu-system-riscv32 -nographic -machine virt -bios none -kernel atomic_test.elf
```

---

## ✅ Expected Output

```text
Thread A trying to acquire lock...
Thread A acquired lock!
Thread A releasing lock
Thread B trying to acquire lock...
Thread B acquired lock!
Thread B releasing lock
```
Sure! Here's a **single terminal command** that will create **all the required files** for **Task 15: Atomic Test Program** using `lr.w` / `sc.w` instructions.

---

### ✅ Terminal Command to Create All Files

```bash
mkdir task15_atomic && cd task15_atomic && \
echo '#ifndef UART_H
#define UART_H
void uart_init();
void uart_putc(char c);
void uart_puts(const char *s);
#endif' > uart.h && \
echo '#include <stdint.h>
#include "uart.h"
#define UART_TX (*(volatile uint32_t*)0x10000000)
void uart_init() {}
void uart_putc(char c) { UART_TX = c; }
void uart_puts(const char *s) { while (*s) uart_putc(*s++); }' > uart.c && \
echo '#include <stdint.h>
#include "uart.h"

volatile int lock = 0;

void lock_acquire(volatile int *lock) {
    int tmp;
    do {
        asm volatile (
            "lr.w %[tmp], %[addr]\n"
            "bnez %[tmp], 1f\n"
            "li %[tmp], 1\n"
            "sc.w %[tmp], %[tmp], %[addr]\n"
            "1:"
            : [tmp] "=&r"(tmp), [addr] "+A" (*lock)
            :
            : "memory"
        );
    } while (tmp != 0);
}

void lock_release(volatile int *lock) {
    *lock = 0;
}

void thread_A() {
    uart_puts("Thread A trying to acquire lock...\n");
    lock_acquire(&lock);
    uart_puts("Thread A acquired lock!\n");
    for (volatile int i = 0; i < 100000; i++);
    uart_puts("Thread A releasing lock\n");
    lock_release(&lock);
}

void thread_B() {
    uart_puts("Thread B trying to acquire lock...\n");
    lock_acquire(&lock);
    uart_puts("Thread B acquired lock!\n");
    for (volatile int i = 0; i < 100000; i++);
    uart_puts("Thread B releasing lock\n");
    lock_release(&lock);
}

int main() {
    uart_init();
    thread_A();
    thread_B();
    while (1);
    return 0;
}' > atomic_test.c && \
echo 'SECTIONS {
  . = 0x80000000;

  .text : {
    *(.init)
    *(.text*)
  }

  .rodata : { *(.rodata*) }

  .data : { *(.data*) }

  .bss : {
    _bss_start = .;
    *(.bss*)
    *(COMMON)
    _bss_end = .;
  }

  . = 0x80004000;
  _stack_top = .;
}
' > link.ld && \
echo '.section .init
.globl _start
_start:
    la  sp, _stack_top       # Set up stack pointer

    # Zero out .bss section
    la   a0, _bss_start
    la   a1, _bss_end
    li   a2, 0
bss_clear:
    bge  a0, a1, bss_done
    sw   a2, 0(a0)
    addi a0, a0, 4
    j    bss_clear
bss_done:

    call main                # Call main()

    # Loop forever after main returns
hang:
    j hang
' > crt0.S && \
echo 'RISCV_GCC=riscv64-unknown-elf-gcc
CFLAGS=-march=rv32imac -mabi=ilp32 -nostartfiles -Wall

all:
	$(RISCV_GCC) $(CFLAGS) -Tlink.ld crt0.S uart.c atomic_test.c -o atomic_test.elf

clean:
	rm -f *.elf *.o' > Makefile
```

---

### 📦 Files Created:

* `atomic_test.c` – main program with pseudo-thread mutex logic
* `uart.c`, `uart.h` – minimal UART functions
* `crt0.S` – startup file
* `link.ld` – linker script
* `Makefile` – to compile the ELF

---

### 🔧 Build It:

```bash
make
```

### ▶️ Run It (QEMU):

```bash
qemu-system-riscv32 -nographic -machine virt -bios none -kernel atomic_test.elf
```



</p></details>

## Outputs (task 15):
![image](https://github.com/user-attachments/assets/e457bd9a-280e-4300-a0e0-eea5e2fc211a)


# 16) ✅ Using `printf` via UART (No OS)

<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>

Here's a complete and minimal solution for **using `printf` with Newlib** in a **bare-metal RISC-V** setup — no OS required.

---

### 🔧 Step 1: Memory-Mapped UART Address

Let’s assume:

```c
#define UART_TX (*(volatile unsigned char*)0x10000000)
```

---

### 📄 `syscalls.c` – Retarget `_write()`

```c
#include <sys/stat.h>
#include <stdint.h>

#define UART_TX (*(volatile uint8_t*)0x10000000)

int _write(int fd, const char *buf, int len) {
    for (int i = 0; i < len; i++) {
        UART_TX = buf[i];  // Write byte to UART
    }
    return len;
}

// Minimal stubs to satisfy linker
int _close(int fd) { return -1; }
int _fstat(int fd, struct stat *st) { st->st_mode = S_IFCHR; return 0; }
int _lseek(int fd, int ptr, int dir) { return 0; }
int _read(int fd, char *buf, int len) { return 0; }
int _isatty(int fd) { return 1; }
void *_sbrk(int incr) { return (void *)-1; }
```

---

### 📄 `main.c` – Use `printf`

```c
#include <stdio.h>

int main() {
    printf("Hello from RISC-V UART!\n");
}
```

---

### 📄 `crt0.S` – Startup (recap)

```assembly
.section .init
.globl _start
_start:
    la  sp, _stack_top

    la   a0, _bss_start
    la   a1, _bss_end
    li   a2, 0
clear_bss:
    bge  a0, a1, done_bss
    sw   a2, 0(a0)
    addi a0, a0, 4
    j    clear_bss
done_bss:

    call main
1:  j 1b
```

---

### 📄 `link.ld` – Linker Script

```ld
SECTIONS {
  . = 0x80000000;

  .text : {
    *(.init)
    *(.text*)
  }

  .rodata : { *(.rodata*) }

  .data : { *(.data*) }

  .bss : {
    _bss_start = .;
    *(.bss*)
    *(COMMON)
    _bss_end = .;
  }

  . = 0x80004000;
  _stack_top = .;
}
```

---

### 🛠️ `Makefile`

```make
CFLAGS = -march=rv32imac -mabi=ilp32 -Wall -nostartfiles
LDSCRIPT = link.ld

all: printf_uart.elf

printf_uart.elf: main.c syscalls.c crt0.S
	@riscv64-unknown-elf-gcc $(CFLAGS) -T $(LDSCRIPT) $^ -o $@

clean:
	@rm -f *.elf

```

---

### 🧪 Run with QEMU

```bash
qemu-system-riscv32 -nographic -machine virt -bios none -kernel printf_uart.elf
```

You should see:

```
Hello from RISC-V UART!
```

</p></details>

## Outputs (task 16):
![image](https://github.com/user-attachments/assets/608b8bef-d4d4-4ad3-8003-61e1f959a099)


# 17) ENDIANNESS TEST
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>
### Endianness (Very Briefly):

**Endianness** is the order in which bytes are stored in memory for multi-byte data (like `int`, `float`, etc.).

There are two main types:

* **Little-endian**:
  Least significant byte is stored **first** (at the lowest memory address).
  Example: `0x12345678` stored as → `78 56 34 12`

* **Big-endian**:
  Most significant byte is stored **first**.
  Example: `0x12345678` stored as → `12 34 56 78`

It matters in low-level programming, hardware communication, file formats, and networking.


Here’s a single terminal command sequence that will:

1. Create the C file with the union test code,
2. Compile it with `gcc`,
3. Run the resulting executable,

all in one go.

---

Open your terminal and run this command (copy-paste all together):

```bash
cat > endian_test.c << 'EOF'
#include <stdio.h>
#include <stdint.h>

int main() {
    union {
        uint32_t i;
        uint8_t bytes[4];
    } test;

    test.i = 0x01020304;

    printf("Stored 0x01020304 as bytes: ");
    for (int j = 0; j < 4; j++) {
        printf("%02x ", test.bytes[j]);
    }
    printf("\n");

    if (test.bytes[0] == 0x04 && test.bytes[1] == 0x03 &&
        test.bytes[2] == 0x02 && test.bytes[3] == 0x01) {
        printf("System is Little-Endian.\n");
    } else if (test.bytes[0] == 0x01 && test.bytes[1] == 0x02 &&
               test.bytes[2] == 0x03 && test.bytes[3] == 0x04) {
        printf("System is Big-Endian.\n");
    } else {
        printf("Unknown Endianness.\n");
    }

    return 0;
}
EOF
gcc endian_test.c -o endian_test && ./endian_test
```

---

### What this does:

* `cat > endian_test.c << 'EOF' ... EOF` creates the C source file `endian_test.c` with the given code.
* `gcc endian_test.c -o endian_test` compiles it into executable `endian_test`.
* `./endian_test` runs the executable and prints the output.

---

Run this in your terminal and you will see the byte order and the endianness detected!
---

For this particular test — checking endianness with the C union code — you **do not need to produce an ELF file explicitly** yourself. Here's why:

* When you run `gcc endian_test.c -o endian_test`, GCC **automatically compiles and links** your C code into an executable file (which **is** an ELF binary on Linux systems).
* That executable (`endian_test`) is what you run with `./endian_test`.
* The ELF format is just the standard executable format on Linux; you don’t have to manage it manually for simple C programs.

---

### Summary:

* The command compiles your C source code into an ELF executable named `endian_test`.
* You run this ELF executable directly.
* You only need to explicitly deal with `.elf` files if you’re doing low-level embedded or bare-metal development (like for RISC-V without an OS).

---

</p></details>

## Outputs (task 17):
![image](https://github.com/user-attachments/assets/4cc910a8-ad33-43f5-bd8f-6b8bd6a96117)
![image](https://github.com/user-attachments/assets/5c3eaec2-cc4d-4346-ac9d-d179a282dd06)


