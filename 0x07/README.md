# 0x07

[[dec](../0x06/)] [[home](../README.md)] [[inc](../0x08/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_07.html)]

```
inc      rax
neg      rax
inc      rax
neg      rax
```

<details>
<summary>See explanation</summary>

## nop

This snippet, just like the previous, is equivalent to doing nothing.

the value of `rax` in incremented and negated (using [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement)), twice. In other words:


```
-(-(rax+1)+1)
```
which is equivalent to
```
-(-rax-1+1)
```
which is equivalent to
```
rax+1-1
```
which is equivalent to
```
rax
```
</details>

[[dec](../0x06/)] [[home](../README.md)] [[inc](../0x08/)]
