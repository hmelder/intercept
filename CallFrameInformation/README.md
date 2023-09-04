## Call Frame Information (CFI) Overview

CFI (Call Frame Information) provides metadata about the function call frames on the stack, allowing dissection of the stack.
This provides the necessary information to perform stack unwinding, and exception handling.

### DWARF Register Number Mapping
Note that instead of the register names, you might find numbers that are used for identifying registers in .cfi directives.

Here is the corresponding mapping for x86\_64 (See: System V ABI, AMD64 Architecture Processor Supplement, $3.6.2):

| Register Name | Number |
| --- | --- |
| RAX | 0 |
| RDX | 1 |
| RCX | 2 |
| RBX | 3 |
| RSI | 4 |
| RDI | 5 |
| RBP | 6 |
| RSP | 7 |
| r8-r15 | 8-15 |
| Return Address (RA) | 16 |
| SSE Registers xmm0-xmm7 | 17-24 |
| Extended SSE Registers xmm8-xmm15 | 25-32 |
| Floating Point Registers st0-st7 | 33-40 |
| MMX Registers mm0-mm7 | 41-48 |

### Some Directives

For a complete list, binutils docs provides a page with an explaination of (all?) [CFI Directives](https://sourceware.org/binutils/docs/as/CFI-directives.html#).

#### `.cfi_startproc`, `.cfi_endproc`
Description: Mark the start/end of a function. The function will have an entry in the `.eh_frame` section.

Example:
```asm
foo:
    .cfi_startproc
    // Procedure body
    .cfi_endproc
```

#### 

#### `.cfi_def_cfa REGISTER, OFFSET`
Description: Defines the current CFA (Canonical Frame Address) rule. The CFA is a reference point (the value of the stack pointer at the entry
of the function) used to compute the addresses of other registers and local variables.

Arguments:
    - REGISTER: The register value as the base address
    - OFFSET: (Absolute) Offset added to the base address

#### `.cfi_def_cfa_register REGISTER`
Description: Modifies the current CFA rule: From now on, REGISTER is used for the reference point computation.

Arguments:
    - REGISTER: The register value as new the base address.

#### `cfi_def_cfa_offset OFFSET`
Description: Modifies the current CFA rule: From now on, OFFSET is used for the reference point computation.

Arguments:
    - OFFSET: (Absolute) Offset added to the base address

#### `.cfi_adjust_cfa_offset OFFSET`
Description: Adds OFFSET to the current CFA offset.

Arguments:
    - OFFSET: (Relative) Offset added to the current offset.

#### `.cfi_offset REGISTER, OFFSET`
Description: Records the location of a register's previous value onto the stack.

Arguments:
    - REGISTER: The register that was pushed onto the stack
    - OFFSET: The offset for address computation

#### `.cfi_val_offset REGISTER, OFFSET`
Description: Different from `.cfa_offset`, `.cfa_val_offset` specifies the *value* of the previous register as CFA + OFFSET,
**not** the location on the stack.

Arguments:
    - REGISTER: The register in question
    - OFFSET: The offset added to CFA

#### `.cfi_rel_offset REGISTER, OFFSET`
Description: Different from `.cfa_offset`, the value of the current CFA register is used for address computation:
(Current CFA Register) + OFFSET).
This is later translated to the `.cfi_offset` directive, as the displacement of the CFA register value
from the CFA is known.

Arguments:
    - REGISTER: The register that was pushed onto the stack
    - OFFSET: The offset for address computation  

#### `.cfi_register REGISTER1, REGISTER2`
Description: Previous value of REGISTER1 is stored in REGISTER2.

#### `.cfi_restore REGISTER`
Description: Indicate that REGISTER has been restored to the value it held at the beginning of the function (right after `.cfi_startproc`)

Arguments:
    - REGISTER: The register in question
