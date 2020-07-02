# 0x00

[dec] [[home](../README.md)] [[inc](../0x01/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_00.html)]

```
xor      eax,eax
lea      rbx,[0]
loop     $
mov      rdx,0
and      esi,0
sub      edi,edi
push     0
pop      rbp
```

<details>
<summary>See explanation</summary>

## Zeroing registers

This snippet shows seven different ways to set various registers to zero, from
`*ax` to `*dx`, and then `esi`, `edi`, `esp`, `rbp`.
Let's see them one by one:

### xor eax,eax

This is as simple as it sounds. Just XOR `eax` with itself. It works on any
register, and width (i.e. it also works with `rax`, `edi`, etc).
XOR is a notoriously slow operation, but it allows for very compact opcodes
(2 bytes) if you are space-constrained:
```
$ rasm2 -a x86 -b 64 'xor eax,eax'
31c0
```

It will take one more byte if we use the 64-bit wide register:
```
$ rasm2 -a x86 -b 64 'xor rax,rax'
4831c0
```

### lea rbx,[0]

LEA stands for `load effective address`. This instruction loads the source value
`[0]` into the 64-bit-wide `rbx` register. See the
[LEA instruction's reference](https://c9x.me/x86/html/file_module_x86_id_153.html)
for details.

The source value uses the square brackets syntax, which means "dereference the
value of the address within the square brackets", i.e. look for the content of
the first 64 bits at the address `0` (i.e. like `*(void *)0` in C).
In protected mode, and on a modern Linux kernel, this means accessing a reserved,
and normally zero'ed area (see
[Linux's memory layout](https://www.kernel.org/doc/html/latest/x86/boot.html#memory-layout)).

This instruction requires memory access, and as such it is very
time-inefficient.
It is also less space-efficient than `xor`, as it takes 7 bytes:
```
$ rasm2 -a x86 -b 64 'lea rbx,[0]'
488d1df9ffffff
```

Even if we swap `rbx` for `ebx` it still takes 7 bytes:
```
$ rasm2 -a x86 -b 64 'lea ebx,[0]'
488d1df9ffffff
```

We have to assemble it in 32-bit mode to save one byte:
```
$ rasm2 -a x86 -b 32 'lea ebx,[0]'
8d1d00000000
```

### loop $

The [`LOOP`](https://c9x.me/x86/html/file_module_x86_id_161.html) instruction
can be confusing: how can it set a register to zero? There is a simple
explanation: `LOOP` jumps to the address indicated by its operand (`$`) until
the value in `ecx` (or `cx`) reaches zero. The operand is a relative address to
the `LOOP` instruction, and in our example `$` means "the current address" (this
value has to be expanded by the assembler program, e.g. `nasm`).

In practice, this instruction will loop on itself until `ecx` is zero before it
continues to the next instruction, in fact setting `ecx` to 0.

This instruction is space-efficient, as it takes only 2 bytes:
```
$ rasm2 -a x86 -b 64 'loop $'
e2fe
```

However it can be time-inefficient if the value of `ecx` is large, because it
will loop as many times as the value of `ecx`. The best case is when `ecx` is
already zero of course.


### mov rdx,0

This is also as simple as it sounds: move 0 to `rdx`, 64-bit wide. It takes 7
bytes:
```
$ rasm2 -a x86 -b 64 'mov rdx,0'
48c7c200000000
```

This is also quite time-efficient because an immediate value is written to a
register. However, keep in mind that on modern CPUs things are less obvious than
it seems, as this article shows: [Does register selection matter to performance
on x86 CPUs?](https://fiigii.com/2020/02/16/Does-register-selection-matter-to-performance-on-x86-CPUs/).

### and esi,0

And this is also pretty obvious, and fast: a register is put to binary AND with
0, which results always in 0.

Space efficiency is pretty good too:
```
$ rasm2 -a x86 -b 64 'and esi,0'
83e600
```

and it gains a byte on 64-bit wide registers:
```
$ rasm2 -a x86 -b 64 'and rsi,0'
4883e600
```

### sub edi,edi

We are again in Obvious Land: subtract the value of `edi` from `edi`, and get a
nice round 0.

Efficient in both time and space:
```
$ rasm2 -a x86 -b 64 'sub edi,edi'
29ff
```

plus one byte for 64-bit wide registers:
```
$ rasm2 -a x86 -b 64 'sub rdi,rdi'
4829ff
```

### push 0; pop rbp

This is more indirect: [`PUSH`](https://c9x.me/x86/html/file_module_x86_id_269.html)
stores the value 0 to the top of the stack, and decrements it by the stack
pointer's size (16, 32, or 64 bit on x86). Then the value is
[`POP`](https://c9x.me/x86/html/file_module_x86_id_248.html)'ed into `rbp`.
These two instructions in fact set `rbp` to 0.

Less time-efficient: 0 is first copied to memory at the value pointed by `rsp,
`rsp` is adjusted, then the value is read from memory again into `rbp`, and
`rsp` is adjusted again to go back to the original value.

Space:
```
$ rasm2 -a x86 -b 64 'push 0; pop rbp'
6a005d
```
Not great, not terrible: `6a00` is `push 0`, `5b` is `pop rbp`.


</details>

[dec] [[home](../README.md)] [[inc](../0x01/)]
