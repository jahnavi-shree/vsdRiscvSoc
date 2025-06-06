# WEEK 1 tasks
# 1) 🛠️ Setting Up RISC-V Toolchain (GCC 10.2.0) on Ubuntu

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
## OUTPUT:

