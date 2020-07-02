# 0x04

[[dec](../0x03/)] [[home](../README.md)] [[inc](../0x05/)]

[[See this riddle on the book](https://www.xorpd.net/pages/xchg_rax/snip_04.html)]

```
xor      al,0x20
```

<details>
<summary>See explanation</summary>

# toupper() and tolower()

This instruction swaps the case of ASCII letters.

This is one of the oldest and well-known ASCII tricks: the ASCII symbols are
arranged such as the only difference between a lower-case and an upper-case
latin letter is the 6th bit: set to 1 for lower-case, set to 0 for
upper-case.

A quick look at the 8-bit binary representation of a few ASCII letters:

<pre><code>
a = 01<b>1</b>00001
A = 01<b>0</b>00001

b = 01<b>1</b>00010
B = 01<b>0</b>00010
</code></pre>

Notice the difference? Just the 6th bit between lower and upper case.

So, the trick is to do a [`XOR`](https://en.wikipedia.org/wiki/Exclusive_or)
between `al` (the lowest order byte of `rax`) and `0x20`, and here we go,
'a' becomes 'A' and 'A' becomes 'a'.

This instruction lets you go back and forth between upper and lower case, but
there's more. As you have probably already imagined you can implement `tolower`
by setting the 6th bit with a binary OR, and implement `toupper` by clearing
the 6th bit with an AND NOT. In C:
```
int
tolower(int c) {
	return c | 0x20;
}

int
toupper(int c) {
	return c & ~0x20;
}
```

Or, in assembly:
```
tolower:
    or al,0x20

...

toupper:
    and al,0xdf
```

</details>

[[dec](../0x03/)] [[home](../README.md)] [[inc](../0x05/)]
