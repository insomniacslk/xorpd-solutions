# 0x02

[[dec](../0x01/)] [[home](../README.md)] [[inc](../0x03/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_02.html)]

```
neg      rax
sbb      rax,rax
neg      rax
```

<details>
<summary>See explanation</summary>

# Double negation

Double negation is a common practice in C to force non-zero numbers to be always 1.
It is typically used when doing boolean operations with integers, where it is
desirable to have 0 to mean false, and 1 to mean true, in case of direct
comparison to possible `define`s of TRUE and FALSE. Whether this is a good
practice or not is out of scope here.

The double negation looks like this:
```
!!x;
```

If `x` is 0, it will stay 0. If `x` is 1, 2, 3 or any other non-zero number, it
will be come 1.

This assembly snippet does exactly that. How?

[`NEG`](https://c9x.me/x86/html/file_module_x86_id_216.html) calculates the
[two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) of its operand,
and sets the carry flag (`CF`) to 0 if the operand is 0, or 1 otherwise.
For example, `NEG` of 0 is 0, and `CF` is set to 0. `NEG` of 1 is 0xffffffff
(assuming 32 bits) and `CF` is 1. `NEG` of 2 is 0xfffffffe and `CF` is 1. And so
on.

Then [`SBB`](https://c9x.me/x86/html/file_module_x86_id_286.html) subtracts the
operands with borrow: the carry flag from the previous operation is added to the
source operand (the rightmost in this example, which is `rax` anyway), then it
is subtracted from the destination operand (the leftmost, `rax` again).
At this point, after `NEG` and `SBB`:
* 0 became 0
* 1 became 0xffffffff (assuming 32 bits)
* 2 became 0xfffffffe (assuming 32 bits)
* etc (just keep decrementing)

Last but not least, `NEG` is applied again as at the first step. At the end of
the process:
* 0 becomes 0
* 1 becomes 1
* 2 becomes 1
* 3 becomes 1

and so on.


</details>

[[dec](../0x01/)] [[home](../README.md)] [[inc](../0x03/)]
