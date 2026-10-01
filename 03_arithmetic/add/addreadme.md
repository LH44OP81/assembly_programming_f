# ADD

Flags are read right after the `add` instruction executes (before the final `xor ebx, ebx`, which overwrites them).

## add1.asm

`mov al, [num1]` loads 120 (0x78), then `add al, [num2]` adds 10 (0x0A).
Result: AL = 0x82 = `10000010b` (130 unsigned, -126 signed).

| Flag | Status | Why |
|------|--------|-----|
| CF | Cleared | 130 fits in 8 bits (max 255), so no carry out of bit 7 |
| ZF | Cleared | Result 0x82 is not zero |
| SF | Set | Bit 7 of the result is 1 |
| OF | Set | Two positive numbers (120 + 10) gave a negative result. 130 exceeds the signed 8-bit max of 127 |
| PF | Set | Low byte `10000010` has two 1 bits (even parity) |
| AF | Set | Low nibbles 0x8 + 0xA = 0x12, so there is a carry from bit 3 into bit 4 |

GDB: `eflags 0xa96 [ PF AF SF IF OF ]`. This matches the table. IF is set by the OS and is not an arithmetic result.

## add2.asm

`mov ax, [num1]` loads 32000 (0x7D00), then `add ax, [num2]` adds 500 (0x01F4).
Result: AX = 0x7EF4 = 32500.

| Flag | Status | Why |
|------|--------|-----|
| CF | Cleared | 32500 fits in 16 bits (max 65535), so no carry out of bit 15 |
| ZF | Cleared | Result 0x7EF4 is not zero |
| SF | Cleared | Bit 15 of the result is 0, so it is positive |
| OF | Cleared | Two positives gave a positive. 32500 is below the signed 16-bit max of 32767 |
| PF | Cleared | Low byte 0xF4 = `11110100` has five 1 bits (odd parity) |
| AF | Cleared | Low nibbles 0x0 + 0x4 = 0x4, so there is no carry from bit 3 into bit 4 |

GDB: `eflags 0x202 [ IF ]`. All arithmetic flags are cleared, matching the table.