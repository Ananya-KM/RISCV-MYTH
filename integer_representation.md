
# Integer Number Representation (64-bit Systems)

#### Introduction to 64-bit Binary Numbers
- Computers operate using binary, whereas humans understand decimal.
- Conversion is needed between these two systems for computation and interpretation.
- A 64-bit binary number is commonly used in modern computer systems for data representation.

#### Word Size and Bit Structure
- **Word**: A fixed-size group of bits, typically 32 or 64 bits in modern systems.
- **Least Significant Bit (LSB)**: The rightmost bit, representing the smallest value.
- **Most Significant Bit (MSB)**: The leftmost bit, representing the largest or sign value.
- **Byte**: A group of 8 bits; four bytes form a 32-bit word, eight bytes form a 64-bit word.

#### Unsigned Binary Representation
- With `n` bits, the total number of unique patterns is `2^n`.
- In a 64-bit system, the maximum value is `2^64 - 1`.
- Unsigned numbers represent only non-negative integers.

#### Understanding Large Numbers
- Large binary values are interpreted by summing the powers of 2 corresponding to set bits.
- Overflow occurs when results exceed the maximum value representable in the system.

#### Signed vs. Unsigned Numbers
- **Unsigned**: Represent only non-negative numbers (0 to `2^64 - 1`).
- **Signed**: Represent both positive and negative numbers, using the two's complement system.

#### Two’s Complement Representation
- A standard method to encode negative numbers.
- To get the two's complement of a number:
  1. Invert all bits (1’s complement).
  2. Add 1.
- The MSB indicates the sign:
  - `MSB = 0` → positive number.
  - `MSB = 1` → negative number.

#### Range in Two’s Complement (64-bit)
- Positive numbers: `0` to `2^63 - 1`
- Negative numbers: `-2^63` to `-1`

#### Binary to Decimal Conversion
- **Positive**: Convert directly from binary.
- **Negative**:
  - Check if MSB is 1.
  - Apply two’s complement to find the magnitude.
  - Result is negative of that magnitude.

#### Importance in RISC-V Architecture
- Integer instructions follow the two's complement logic.
- The MSB plays a key role in distinguishing between signed and unsigned operations.
- Integer arithmetic in RISC-V must account for overflow and correct sign interpretation.

  ## LABS
Sure! Here are the commands listed separately:

**1. Open the file in Vim:**
```bash
gedit unsignedHighest.c
```
![WhatsApp Image 2025-04-11 at 12 03 40 AM (8)](https://github.com/user-attachments/assets/a1d69192-2924-42b4-9a5f-e40d87fe1cc4)
![WhatsApp Image 2025-04-11 at 12 03 40 AM (9)](https://github.com/user-attachments/assets/5011a57d-0de4-40c7-80ff-c7c444eea686)

**2. Compile with RISC-V GCC:**
```bash
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o unsignedHighest.o unsignedHighest.c
```
**3. Run the Spike simulation:**
```bash
spike pk unsignedHighest.o
```
![Screenshot 2025-04-11 082257](https://github.com/user-attachments/assets/01527559-fce3-4d3a-aacd-956d7ae8e7f2)

