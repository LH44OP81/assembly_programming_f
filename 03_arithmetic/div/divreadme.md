# DIV

`div` does not define any of the arithmetic flags. CF, OF, SF, ZF, AF and PF are all **undefined** after `div`. Whatever GDB shows for them is not a result of the division, so they can't be explained as "set because the result was X". The only way `div` signals a problem is the divide error exception (#DE) when the divisor is 0 or the quotient doesn't fit in the destination register. Neither happens in these programs.

Flags are read right after the `div` instruction executes (before the final `xor ebx, ebx`).

## div1.asm

`mov ax, [dividend]` loads 100, `mov bl, [divisor]` loads 7, then `div bl` divides AX by BL.
Result: AL = 14 (quotient), AH = 2 (remainder), since 14 * 7 + 2 = 100.
The quotient 14 fits in AL (max 255), so no divide error.

| Flag | Status | Why |
|------|--------|-----|
| CF | Undefined | `div` does not define it. It is not a carry or borrow indicator here |
| OF | Undefined | `div` does not define it. Overflow is reported by #DE, not by this flag |
| SF | Undefined | Not a copy of the quotient's sign bit after `div` |
| ZF | Undefined | Not set from the quotient being zero or non-zero |
| PF | Undefined | Not computed from the quotient |
| AF | Undefined | Not computed from the operation |

## div2.asm

`mov ax, [dividend]` loads 50000, `mov dx, [highpart]` loads 0, `mov bx, [divisor]` loads 300, then `div bx` divides DX:AX (50000) by BX.
Result: AX = 166 (quotient), DX = 200 (remainder), since 166 * 300 + 200 = 50000.
The quotient 166 fits in AX (max 65535), so no divide error.

| Flag | Status | Why |
|------|--------|-----|
| CF | Undefined | `div` does not define it |
| OF | Undefined | `div` does not define it. Overflow is reported by #DE |
| SF | Undefined | Not derived from the quotient |
| ZF | Undefined | Not derived from the quotient |
| PF | Undefined | Not derived from the quotient |
| AF | Undefined | Not derived from the operation |

## Observed in GDB

After each `div`, the registers hold the expected quotient and remainder. Any arithmetic flags shown by `info registers eflags` are leftovers from before the instruction or CPU-specific values. They are not results of the division, because the flags are undefined after `div`.