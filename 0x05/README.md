# 0x05

[[dec](../0x04/)] [[home](../README.md)] [[inc](../0x06/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_05.html)]

```
sub      rax,5
cmp      rax,4
```

<details>
<summary>See explanation</summary>

## 5 <= x <= 9

I had some trouble understanding this, so I cheated, and looked at
[Raptor's solution](https://github.com/0xdea/xorpd-solutions/blob/master/0x05.asm) .
I can't remember if I solved this back then, but probably not.

So, since I cheated, I'll try at least to provide some more information to beg
your forgiveness.

This snippet allows checking if a number is in the range [5-9] using only
one jump instruction. The jump instruction is not shown, but it can be either
`jbe` or `jna` - they are equivalent and translate to the same opcode (`76cb`).

### Geometry to the rescue

It is easier to understand this snippet if you figure out a geometrical axis
`x`, and mark the two spots 5 and 9:

```
-----0----5---9------->
```

In order to check if x is between 5 and 9 you can either:
1. check if it is smaller or equal than 5, and jump out if so
1. check if it is greater or equal than 9, and jump out if so
1. at this point you know that it is in range, and continue

Or "shift left" our reference frame so that what was 5 is now
the center (the 0) of the reference frame
Graphically, we have shifted the `x` axis to the left by 5:
```
-----0----4----->
^    ^    ^
|    |    |
|    |    this was 9 before shifting
|    this was 5 before shifting
this was 0 before shifting
```

The shifting is done with `sub`. this instruction also sets
[`RFLAGS`](https://en.wikipedia.org/wiki/FLAGS_register) accordingly.

Remember that `sub` and `cmp` are essentially the same instruction,
except that `cmp` does not modify the destination operand. This also
means that the rules to set `RFLAGS` are the same in both instructions.
It is essential to understand how `RFLAGS` is affected in order to
understand this riddle. To make it easier, let's write down what
happens with two dummy operands `a` and `b`:

* if `a < b`, `CF=1`, `SF=1`, `ZF=0` (other flags may change too, but they
  are not relevant to this discussion)
* if `a == b`, `CF=0`, `SF=0`, `ZF=1`
* if `a > b`, `CF=0`, `SF=0`, `ZF=0`

After executing the two instructions of this riddle, `RFLAGS` will be
manipulated twice, and will look like the the following:
* if `x < 5`, `CF=1`, `ZF=0`, `SF=1` 
* if `5 <= x < 8`, `CF=1`, `ZF=0`, `SF=1`
* if `x == 9`, `CF=0`, `ZF=1`, `SF=0`
* if `x > 9`, `CF=0`, `ZF=0`, `SF=0`

As said initially, only `jbe` and `jna` can be used with this snippet. With
`jbe` we check if 
The [`Jcc` instructions](https://c9x.me/x86/html/file_module_x86_id_146.html)
can jump accordingly to these values. The documentation should be sufficient
to know what to choose in each case. To jump when x is within the [5, 9] range
you can use either `jbe` or `jna`, which as already seen, are in fact the same
instruction.

</details>

[[dec](../0x04/)] [[home](../README.md)] [[inc](../0x06/)]
