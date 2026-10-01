# MUL

`mul` only defines CF and OF. Both are set if the upper half of the result is non-zero, and cleared if the result fits in the lower half. SF, ZF, AF and PF are **undefined** after `mul`, so any values GDB shows for them are CPU-specific and are not results of the multiplication.

Flags are read right after the `mul` instruction executes (before the final `xor ebx, ebx`).

## mul1.asm

`mov al, [num1]` loads 25, then `mul byte [num2]` computes AL * 10.
Result: AX = 250 = 0x00FA (AH = 0x00, AL = 0xFA).

| Flag | Status | Why |
|------|--------|-----|
| CF | Cleared | The upper half (AH) is 0, so the result fits in AL without needing AH |
| OF | Cleared | Same condition as CF: AH is 0 |
| SF | Undefined | `mul` does not define it. It is not a copy of the result's sign bit |
| ZF | Undefined | `mul` does not define it, even though the result is non-zero |
| PF | Undefined | `mul` does not define it |
| AF | Undefined | `mul` does not define it |

GDB: CF and OF are not listed, so both are cleared, as expected. Any other flag listed is an undefined leftover, not a result of the multiplication.

## mul2.asm

`mov ax, [num1]` loads 3000, then `mul word [num2]` computes AX * 200 into DX:AX.
Result: 600000 = 0x000927C0, so DX = 0x0009 and AX = 0x27C0.

| Flag | Status | Why |
|------|--------|-----|
| CF | Set | The upper half (DX = 9) is non-zero, so the result does not fit in 16 bits |
| OF | Set | Same condition as CF: DX is non-zero |
| SF | Undefined | `mul` does not define it |
| ZF | Undefined | `mul` does not define it |
| PF | Undefined | `mul` does not define it |
| AF | Undefined | `mul` does not define it |

GDB: CF and OF both appear in the flags list, as expected. Any other arithmetic flag listed is an undefined leftover, not a result of the multiplication.