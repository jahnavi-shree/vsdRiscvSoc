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


# 5) 🔧 From C to Assembly (Function Prologue/Epilogue)
<details>
  <summary> <b> 🔍 Documentation </b> </summary>
<p>



</p></details>

## Outputs (task 5):
