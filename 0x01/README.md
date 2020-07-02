# 0x01

[[dec](../0x00/)] [[home](../README.md)] [[inc](../0x02/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_01.html)]

```
.loop:
    xadd     rax,rdx
    loop     .loop
```

<details>
<summary>See explanation</summary>

## Fibonacci

This is probably the smallest implementation of a
[Fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number) generator,
as long as the two registers are set to 0 and 1 (doesn't matter which one), and you
have set `ecx` to the index of the desired Fibonacci number.

In other words, if you want the 7th Fibonacci number, `mov ecx,7` before the loop.

The result will be in `rax`.

### Why?

[`XADD`](https://c9x.me/x86/html/file_module_x86_id_327.html) swaps and adds
the two operands into the destination operand. In other words, the sum of `rax`
and `rdx` is written to `rax`.

Then [`LOOP`](https://c9x.me/x86/html/file_module_x86_id_161.html) makes sure
that this is done until `ecx` reaches 0 (it is decremented at every iteration).

When the loop ends (i,e. when `ecx` is 0), the i-th Fibonacci number will be in
the destination operand `rax`.

</details>

[[dec](../0x00/)] [[home](../README.md)] [[inc](../0x02/)]
