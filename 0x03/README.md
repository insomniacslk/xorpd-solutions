# 0x03

[[dec](../0x02/)] [[home](../README.md)] [[inc](../0x04/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_03.html)]

```
sub      rdx,rax
sbb      rcx,rcx
and      rcx,rdx
add      rax,rcx
```

<details>
<summary>See explanation</summary>

## min(a, b)

This snippet finds the minimum between the two values stored in
`rax` and `rdx` and stores the result in `rcx`.
This is a branchless implementation, and as such it offers performance
benefits on architectures with prefetching and branch prediction
(like modern x86 CPUs). SSE introduces dedicated instructions, see
the section "Unsigned integer compares" in the
[SSE: mind the gap](https://fgiesen.wordpress.com/2016/04/03/sse-mind-the-gap/)
post.

### How does this work?

`sub rdx,rax` subtracts the value contained in `rax` from the value
contained in `rdx`, and stores the result in `rdx`. It also sets `CF`
(carry flag) to 1 if there was carry, or in other words if `rax` is
greater than `rdx`.

`sbb rcx,rcx` sets all the bits of `rcx` to the value of `CF` after
the preceding instruction. In other words it is set to either all
zeros, or all ones. We have seen how `sbb` does it in the previous
riddle.

`and rcx,rdx` sets `rcx` to 0 if `CF` was zero, or to the current
value of `rdx` if `CF` was 1, using `rcx` as a mask, with the value
that was set at the previous step.
Remember that at this stage `rdx` contains the difference between
the original values of `rdx` and `rax`, this will be relevant in the
next instruction.

`add rax,rcx` sums `rax` and `rcx` and sets the result into `rax`.
At this point `rcx` contains either the initial value of `rax`, or
the value of `rdx` as seen above, so adding it up yields either the
initial value of `rax`, or the initial value of `rdx`

### Examples

This was the theory, but let's also see the practice with two examples.

Assume that the initial value of `rax` is 3, and the initial value of
`rdx` is 5. As said above this snippet computes the minimum of the two
values, and puts it in `eax`.

* step 1: `sub rdx,rax`. This sets `rdx` to `rdx - rax` = 5 - 3 = 2,
  and the carry flag `CF` is set to 0. Now we have `rax = 3` and `rdx = 2`
* step 2: `sbb rcx,rcx`. This sets `rcx` to 0, because `CF` is 0
* step 3: `and rcx,rdx`. This uses `rcx` as a mask for `rdx`, in fact
  leaving `rcx` set to 0
* step 4: `add rax,rcx`. This sets `rax` to the sum of the two operands,
  i.e. 3 + 0 = 3, that is the original value of `rax`.


Let's see the second example. We assume that `rax` is set to 5 and `rdx` is
set to 3. This will trigger the opposite behaviour of the previous example,
so we expect that the final value in `rax` will be the initial value of
`rdx`, 3.

* step 1: `sub rdx,rax`. This sets `rdx` to `rdx - rax` = 3 - 5 = -2,
  and the carry flag `CF` is set to 1. Now we have `rdx = -2` (or
  0xfffffffffffffffe), and `rax = 5`
* step 2: `sbb rcx,rcx`. This sets `rcx` to 0xffffffffffffffff,
  because `CF` is 1
* step 3: `and rcx,rdx`. This uses `rcx` as a mask for `rdx`, in fact
  setting `rcx` to the value of `rdx`, that is 3
* step 4: `add rax,rcx`. This sets `rax` to the sum of the two operands,
  i.e. 5 + (-2) = 3, that is the original value of `rdx`.


</details>

[[dec](../0x02/)] [[home](../README.md)] [[inc](../0x04/)]
