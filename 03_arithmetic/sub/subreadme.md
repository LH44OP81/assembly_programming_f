# SUB

Flags are read right after the `sub` instruction executes (before the final `xor ebx, ebx`, which overwrites them). For `sub`, CF means a borrow: the unsigned first operand is smaller than the second.

## sub1.asm

`mov al, [num1]` loads 50 (0x32), then `sub al, [num2]` subtracts 80 (0x50).
Result: AL = 0xE2 = `11100010b` (226 unsigned, -30 signed).

| Flag | Status | Why |
|------|--------|-----|
| CF | Set | Unsigned 50 < 80, so a borrow is needed from beyond bit 7 |
| ZF | Cleared | Result 0xE2 is not zero |
| SF | Set | Bit 7 of the result is 1 (negative as signed) |
| OF | Cleared | Signed 50 - 80 = -30, which fits in the signed 8-bit range (-128 to 127) |
| PF | Set | Low byte `11100010` has four 1 bits (even parity) |
| AF | Cleared | Low nibbles 0x2 - 0x0 need no borrow from bit 4 |

GDB: `eflags 0x287 [ CF PF SF IF ]`. This matches the table.

## sub2.asm

`mov ax, [num1]` loads 1000 (0x03E8), then `sub ax, [num2]` subtracts 2000 (0x07D0).
Result: AX = 0xFC18 (64536 unsigned, -1000 signed).

| Flag | Status | Why |
|------|--------|-----|
| CF | Set | Unsigned 1000 < 2000, so a borrow is needed from beyond bit 15 |
| ZF | Cleared | Result 0xFC18 is not zero |
| SF | Set | Bit 15 of the result is 1 (negative as signed) |
| OF | Cleared | Signed 1000 - 2000 = -1000, which fits in the signed 16-bit range (-32768 to 32767) |
| PF | Set | Low byte 0x18 = `00011000` has two 1 bits (even parity) |
| AF | Cleared | Low nibbles 0x8 - 0x0 need no borrow from bit 4 |

GDB: `eflags 0x287 [ CF PF SF IF ]`. This matches the table.

Both programs borrow (CF set) without signed overflow (OF cleared). CF tracks unsigned wraparound, OF tracks signed overflow.