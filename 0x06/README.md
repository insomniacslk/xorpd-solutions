# 0x06

[[dec](../0x05/)] [[home](../README.md)] [[inc](../0x07/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_06.html)]

```
not      rax
inc      rax
neg      rax
```

<details>
<summary>See explanation</summary>

## nop

This snippet is equivalent to.. doing nothing.

`not` flips all the bits, which is equivalent to doing the
[one's complement](https://en.wikipedia.org/wiki/Ones%27_complement)
of a number.

`inc` increments by one.

`not` does the
[two's complement](https://en.wikipedia.org/wiki/Two%27s_complement).

In practice a two's complement is equivalent to a one's complement + 1, so the
last operation neutralizes the first two.

</details>

[[dec](../0x05/)] [[home](../README.md)] [[inc](../0x07/)]
