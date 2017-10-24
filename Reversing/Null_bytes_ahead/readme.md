# Null bytes ahead. (Reversing - 100 Points)
## Challenge & Writeup Courtesy of [Brad Campbell](https://twitter.com/hackersoup)

> [rev-100](rev-100)

Solution
--------
Solution
--------

First step, as always, is to figure out what we are working with.

```
root@bce926f33e20:/opt/Rev-100# file rev-100
rev-100: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=862d74b1876204b80f1ae3635d20df73a7333474, not stripped
```

Same as level 50, we have:
1. The file is ELF format
2. It is 64-bit x86 assembly
3. It isn't stripped

Running the program we get the exact same output as rev-50... Odd...

```
root@bce926f33e20:/opt/Rev-100# ./rev-100
usage: ./rev-100 <flag>
verifies the flag provided, alerting if the flag is real or not.
root@bce926f33e20:/opt/Rev-100# ./rev-100 "GoldenTICKET{test?}"
Invalid flag. To the gulag with you!
```

Strange... Lets see what is going on underneath.

```
root@bce926f33e20:/opt/Rev-100# r2 rev-100
[0x00400570]> aaa
[0x00400570]> s main
[0x00400666]> pdf
/ (fcn) sym.main 331
|           0x00400666   push rbp
|           0x00400667   mov rbp, rsp
|           0x0040066a   sub rsp, 0x1040
|           0x00400671   mov [rbp-0x1034], edi
|           0x00400677   mov [rbp-0x1040], rsi
|           0x0040067e   mov rax, [fs:0x28]
|           0x00400687   mov [rbp-0x8], rax
|           0x0040068b   xor eax, eax
|           0x0040068d   mov qword [rbp-0x1018], 0x400848
|           0x00400698   cmp dword [rbp-0x1034], 0x2
|       ,=< 0x0040069f   jz 0x4006d1
|       |   0x004006a1   mov rax, [rbp-0x1040]
|       |   0x004006a8   mov rax, [rax]
|       |   0x004006ab   mov rsi, rax
|       |   0x004006ae   mov edi, str.usagesflag
|       |   0x004006b3   mov eax, 0x0
|       |   0x004006b8   call sym.imp.printf
|       |      sym.imp.printf(unk)
|       |   0x004006bd   mov edi, str.verifiestheflagprovidedalertingiftheflagisrealornot.
|       |   0x004006c2   call sym.imp.puts
|       |      sym.imp.puts()
|       |   0x004006c7   mov eax, 0x1
|      ,==< 0x004006cc   jmp loc.0040079b
|      |`-> 0x004006d1   mov dword [rbp-0x1020], 0x0
|     ,===< 0x004006db   jmp 0x40073b ; (fcn.0040063c)
|   .-----> 0x004006dd   cmp dword [rbp-0x1020], 0x400
|- fcn.0040073b 202
|   |,====< 0x004006e7   jg 0x40075d
|   ||||    0x004006e9   mov rax, [rbp-0x1040]
|   ||||    0x004006f0   add rax, 0x8
|   ||||    0x004006f4   mov rdx, [rax]
|   ||||    0x004006f7   mov eax, [rbp-0x1020]
|   ||||    0x004006fd   cdqe
|   ||||    0x004006ff   add rax, rdx
|   ||||    0x00400702   movzx eax, byte [rax]
|   ||||    0x00400705   mov [rbp-0x1021], al
|   ||||    0x0040070b   movsx eax, byte [rbp-0x1021]
|   ||||    0x00400712   mov edi, eax
|   ||||    0x00400714   call sym.imp.btowc
|   ||||       sym.imp.btowc()
|   ||||    0x00400719   mov [rbp-0x101c], eax
|   ||||    0x0040071f   mov eax, [rbp-0x1020]
|   ||||    0x00400725   cdqe
|   ||||    0x00400727   mov edx, [rbp-0x101c]
|   ||||    0x0040072d   mov [rbp+rax*4-0x1010], edx
|   ||||    0x00400734   add dword [rbp-0x1020], 0x1
|   |||     ; CODE (CALL) XREF from 0x004006db (fcn.0040063c)
|   ||`---> 0x0040073b   mov rax, [rbp-0x1040]
|   || |    0x00400742   add rax, 0x8
|   || |    0x00400746   mov rdx, [rax]
|   || |    0x00400749   mov eax, [rbp-0x1020]
|   || |    0x0040074f   cdqe
|   || |    0x00400751   add rax, rdx
|   || |    0x00400754   movzx eax, byte [rax]
|   || |    0x00400757   test al, al
|   `=====< 0x00400759   jnz 0x4006dd
|  ,======< 0x0040075b   jmp loc.0040075e
|  | `----> 0x0040075d   nop
|  |        ; CODE (CALL) XREF from 0x0040075b (fcn.0040063c)
|- loc.0040075e 83
|  `------> 0x0040075e   lea rdx, [rbp-0x1010]
|      |    0x00400765   mov rax, [rbp-0x1018]
|      |    0x0040076c   mov rsi, rdx
|      |    0x0040076f   mov rdi, rax
|      |    ; CODE (CALL) XREF from 0x00400500 (fcn.004004fc)
|      |    0x00400772   call sym.imp.wcscmp
|      |       sym.imp.wcscmp()
|      |    0x00400777   test eax, eax
| ,=======< 0x00400779   jnz 0x40078c
| |    |    0x0040077b   mov edi, str.Validflag.Yougetthegoldenticket
| |    |    0x00400780   call sym.imp.puts
| |    |       sym.imp.puts()
| |    |    0x00400785   mov eax, 0x0
|           ; CODE (CALL) XREF from 0x0040079b (fcn.0040063c)
| ========< 0x0040078a   jmp loc.0040079b
| `-------> 0x0040078c   mov edi, str.Invalidflag.Tothegulagwithyou
|      |    ; CODE (CALL) XREF from 0x00400510 (fcn.00400506)
|      |    0x00400791   call sym.imp.puts
|      |       sym.imp.puts()
|      |    0x00400796   mov eax, 0x1
|      |    ; CODE (CALL) XREF from 0x0040078a (fcn.0040063c)
|      |    ; CODE (CALL) XREF from 0x004006cc (fcn.0040063c)
|- loc.0040079b 22
| -----`--> 0x0040079b   mov rcx, [rbp-0x8]
|           0x0040079f   xor rcx, [fs:0x28]
|           0x004007a8   jz 0x4007af
|           ; CODE (CALL) XREF from 0x00400520 (fcn.00400516)
|           0x004007aa   call sym.imp.__stack_chk_fail
|              sym.imp.__stack_chk_fail()
|           0x004007af   leave
\           0x004007b0   ret
[0x00400666]>
```

(Unrelated, but the address of `main()` is offset 0x666 :D )

The format looks very similar. We want to reach offset `0x0040077b`.
Backtracing this, we see a call to `wcscmp` instead of `strcmp`. Hmm. Looking at the
documentation for `wcscmp` we see that it compares "wide strings". Well, lets
see if it can be solved in the same way. Looking at the arguments, we have `[rbp-0x1010]`
and `[rbp-0x1018]` being passed in. Tracking those back to their absolute address we can see 
that argv[1] is getting put into variable `0x1010` at `0x0040072d` with `mov [rbp+rax*4-0x1010], edx`
after some kind of computation. Examining `0x1018` shows that it gets assigned at the 
start of main, with `0x0040068d   mov qword [rbp-0x1018], 0x400848`. Strange, R2 doesn't generate
a name for the string. Lets take a look.

```
[0x00400666]> psz @ 0x400848
G
```

Progress! That looks like the start of a flag. What could be the problem here?
The solution lies in the functions being used. They operate on what is called a "wide string".
This is a string that has more than one byte associated with it. In this case, it is using
4 bytes to represent each character. When we try to print out the strings for something, usually
those searches will terminate after finding a null byte. In this case, each character has 3 null
bytes after each character! This interferes with most string printing directives. Lets try
printing a number of raw bytes at that offset.

```
[0x00400766]> px 144 @ 0x400848
- offset -   0 1  2 3  4 5  6 7  8 9  A B  C D  E F  0123456789ABCDEF
0x00400848  4700 0000 6f00 0000 6c00 0000 6400 0000  G...o...l...d...
0x00400858  6500 0000 6e00 0000 5400 0000 4900 0000  e...n...T...I...
0x00400868  4300 0000 4b00 0000 4500 0000 5400 0000  C...K...E...T...
0x00400878  7b00 0000 6300 0000 3400 0000 7500 0000  {...c...4...u...
0x00400888  3700 0000 3100 0000 3000 0000 6e00 0000  7...1...0...n...
0x00400898  5f00 0000 7700 0000 3100 0000 6400 0000  _...w...1...d...
0x004008a8  3300 0000 5f00 0000 6300 0000 6800 0000  3..._...c...h...
0x004008b8  3400 0000 7200 0000 5f00 0000 6c00 0000  4...r..._...l...
0x004008c8  3000 0000 3400 0000 6400 0000 7d00 0000  0...4...d...}...
```

That sure looks like a flag. Stripping out the null bytes we can get the flag
of `GoldenTICKET{c4u710n_w1d3_ch4r_l04d}`

Flag: 'GoldenTICKET{c4u710n_w1d3_ch4r_l04d}'

