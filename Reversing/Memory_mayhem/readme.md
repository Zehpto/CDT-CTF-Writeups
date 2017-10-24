# Memory mayhem (Reversing - 200 Points)
## Challenge Courtesy of [Brad Campbell](https://twitter.com/hackersoup)

> [rev-200](rev-200)

Solution
--------

First step, as always, is to figure out what we are working with.

```
root@bce926f33e20:/opt/Rev-200# file rev-200
rev-200: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=c9d1af850dbce4a6819a1d585fd3a80c43c05a88, not stripped
```

Same as level 100, we have:
1. The file is ELF format
2. It is 64-bit x86 assembly
3. It isn't stripped

Now lets run the program:

```
root@d0154f30490a:/opt/Rev-200# ./rev-200
Segmentation fault
root@d0154f30490a:/opt/Rev-200# ./rev-200 "GoldenTICKET{test?}"
47
Invalid flag. To the gulag with you!
```

So when provided an argument it outputs some number then displays a message. Lets
disassemble the program and see what happens.

```
[0x0040080e]> pdf
|           ; CODE (CALL) XREF from 0x0040080e (unk)
/ (fcn) sym.main 225
|           0x0040080e   push rbp
|           0x0040080f   mov rbp, rsp
|           0x00400812   sub rsp, 0x20
|           0x00400816   mov [rbp-0x14], edi
|           0x00400819   mov [rbp-0x20], rsi
|           0x0040081d   mov qword [rbp-0x10], 0x400988
|           0x00400825   mov rax, [rbp-0x20]
|           0x00400829   add rax, 0x8
|           0x0040082d   mov rax, [rax]
|           0x00400830   movzx eax, byte [rax]
|           0x00400833   movsx eax, al
|           0x00400836   mov esi, eax
|           0x00400838   mov edi, str.x
|           0x0040083d   mov eax, 0x0
|           ; CODE (CALL) XREF from 0x00400600 (fcn.004005f6)
|           0x00400842   call sym.imp.printf
|              sym.imp.printf(unk)
|           0x00400847   cmp dword [rbp-0x14], 0x2
|       ,=< 0x0040084b   jz 0x400877
|       |   0x0040084d   mov rax, [rbp-0x20]
|       |   0x00400851   mov rax, [rax]
|       |   0x00400854   mov rsi, rax
|       |   0x00400857   mov edi, str.usagesflag
|       |   0x0040085c   mov eax, 0x0
|       |   0x00400861   call sym.imp.printf
|       |      sym.imp.printf()
|       |   0x00400866   mov edi, str.verifiestheflagprovidedalertingiftheflagisrealornot.
|       |   0x0040086b   call sym.imp.puts
|       |      sym.imp.puts()
|       |   0x00400870   mov eax, 0x1
|      ,==< 0x00400875   jmp loc.004008ed
|      |`-> 0x00400877   mov rax, [rbp-0x10]
|      |    0x0040087b   mov rdi, rax
|      |    0x0040087e   call sym.imp.strlen
|      |       sym.imp.strlen()
|      |    0x00400883   mov edx, eax
|      |    0x00400885   mov rax, [rbp-0x10]
|      |    0x00400889   mov esi, edx
|      |    0x0040088b   mov rdi, rax
|      |    0x0040088e   call sym.decrypt
|      |       sym.decrypt()
|      |    0x00400893   mov [rbp-0x8], rax
|      |    0x00400897   mov rax, [rbp-0x20]
|      |    0x0040089b   add rax, 0x8
|      |    0x0040089f   mov rdx, [rax]
|      |    0x004008a2   mov rax, [rbp-0x8]
|      |    0x004008a6   mov rsi, rdx
|      |    0x004008a9   mov rdi, rax
|      |    0x004008ac   call sym.imp.strcmp
|      |       sym.imp.strcmp()
|      |    0x004008b1   test eax, eax
|     ,===< 0x004008b3   jnz 0x4008d2
|     ||    0x004008b5   mov edi, str.Validflag.Yougetthegoldenticket
|     ||    0x004008ba   call sym.imp.puts
|     ||       sym.imp.puts()
|     ||    0x004008bf   mov rax, [rbp-0x8]
|     ||    0x004008c3   mov rdi, rax
|     ||    0x004008c6   call sym.imp.free
|     ||       sym.imp.free()
|     ||    0x004008cb   mov eax, 0x0
|           ; CODE (CALL) XREF from 0x004008ed (unk)
|    ,====< 0x004008d0   jmp loc.004008ed
|    |`---> 0x004008d2   mov edi, str.Invalidflag.Tothegulagwithyou
|    | |    ; CODE (CALL) XREF from 0x004005d0 (fcn.004005c6)
|    | |    0x004008d7   call sym.imp.puts
|    | |       sym.imp.puts()
|    | |    0x004008dc   mov rax, [rbp-0x8]
|    | |    0x004008e0   mov rdi, rax
|    | |    ; CODE (CALL) XREF from 0x004005c0 (fcn.004005bc)
|    | |    0x004008e3   call sym.imp.free
|    | |       sym.imp.free()
|    | |    0x004008e8   mov eax, 0x1
|    | |    ; CODE (CALL) XREF from 0x004008d0 (unk)
|    | |    ; CODE (CALL) XREF from 0x00400875 (unk)
|- loc.004008ed 2
|    `-`--> 0x004008ed   leave
\           0x004008ee   ret
[0x0040080e]>
```

This looks very similar to the first challenge. Lets try printing out the string stored
in memory at the start of the program.

```
[0x0040080e]> psz @ 0x400988
.= *",=Z^^Z6^]6\6]XX^ZV
[0x0040080e]> px @ 0x400988
- offset -   0 1  2 3  4 5  6 7  8 9  A B  C D  E F  0123456789ABCDEF
0x00400988  2e06 050d 0c07 3d20 2a22 2c3d 120b 5a5e  ......= *",=..Z^
0x00400998  5e5a 1b36 5e01 5d07 3604 0d5c 365d 0458  ^Z.6^.].6..\6].X
0x004009a8  1b58 5e5a 5614 0025 780a 0075 7361 6765  .X^ZV..%x..usage
```

A jumble of characters and even some unprintable ones... Doesn't look like a flag
to me. The length looks about right. Maybe its been obfuscated?

Looking at the assembly of the main function, we can see a very interesting line:
`0x0040088e   call sym.decrypt`
which takes the encoded string in as an argument, and an integer value. Since the
integer value provided is generated by `strlen` we can assume that argument 2 is the length of
the provided string. Lets take apart the decrypt function:

```
[0x0040090e]> s sym.decrypt
[0x00400766]> pdf
|           ; CODE (CALL) XREF from 0x0040088e (unk)
/ (fcn) sym.decrypt 168
|           0x00400766   push rbp
|           0x00400767   mov rbp, rsp
|           0x0040076a   sub rsp, 0x30
|           0x0040076e   mov [rbp-0x28], rdi
|           0x00400772   mov [rbp-0x2c], esi
|           0x00400775   mov eax, [rbp-0x2c]
|           0x00400778   mov eax, eax
|           0x0040077a   mov rdi, rax
|           0x0040077d   call sym.imp.malloc
|              sym.imp.malloc(unk)
|           0x00400782   mov [rbp-0x10], rax
|           0x00400786   cmp qword [rbp-0x10], 0x0
|       ,=< 0x0040078b   jnz 0x400794
|       |   0x0040078d   mov eax, 0x0
|      ,==< 0x00400792   jmp loc.0040080c
|      |`-> 0x00400794   mov dword [rbp-0x14], 0x0
|     ,===< 0x0040079b   jmp 0x4007c3 ; (fcn.0040073c)
|    .----> 0x0040079d   mov eax, [rbp-0x14]
|- fcn.004007c3 110
|    |||    0x004007a0   movsxd rdx, eax
|    |||    0x004007a3   mov rax, [rbp-0x10]
|    |||    0x004007a7   add rax, rdx
|    |||    0x004007aa   mov edx, [rbp-0x14]
|    |||    0x004007ad   movsxd rcx, edx
|    |||    0x004007b0   mov rdx, [rbp-0x28]
|    |||    0x004007b4   add rdx, rcx
|    |||    0x004007b7   movzx edx, byte [rdx]
|    |||    0x004007ba   xor edx, 0x69
|    |||    0x004007bd   mov [rax], dl
|    |||    0x004007bf   add dword [rbp-0x14], 0x1
|    ||     ; CODE (CALL) XREF from 0x0040079b (fcn.0040073c)
|    |`---> 0x004007c3   mov eax, [rbp-0x14]
|    | |    0x004007c6   cmp eax, [rbp-0x2c]
|    `====< 0x004007c9   jl 0x40079d
|      |    0x004007cb   mov esi, str.wb
|      |    0x004007d0   mov edi, str.out.lol
|      |    0x004007d5   call sym.imp.fopen
|      |       sym.imp.fopen()
|      |    0x004007da   mov [rbp-0x8], rax
|      |    0x004007de   mov eax, [rbp-0x2c]
|      |    0x004007e1   movsxd rsi, eax
|      |    0x004007e4   mov rdx, [rbp-0x8]
|      |    0x004007e8   mov rax, [rbp-0x10]
|      |    0x004007ec   mov rcx, rdx
|      |    0x004007ef   mov edx, 0x1
|      |    0x004007f4   mov rdi, rax
|      |    0x004007f7   call sym.imp.fwrite
|      |       sym.imp.fwrite()
|      |    0x004007fc   mov rax, [rbp-0x8]
|      |    0x00400800   mov rdi, rax
|      |    0x00400803   call sym.imp.fclose
|      |       sym.imp.fclose()
|      |    0x00400808   mov rax, [rbp-0x10]
|      |    ; CODE (CALL) XREF from 0x00400792 (fcn.0040073c)
|- loc.0040080c 2
|      `--> 0x0040080c   leave
\           0x0040080d   ret
[0x00400766]>
```

The assembly output shows a simple loop that iterates over the string, XORs the
character with the letter `i`, then puts that into a new heap array. So this seems to
be nothing more than a simple XOR cipher.

Now that we know what we are dealing with, we can attack this in one of two ways. The first
is to put the bytes of the encrypted string into an XOR cipher and use the key `0x69`.
Or we could spin up a debugger and rip the string from the program's memory. Lets do the
latter, it sounds more fun.

We know that the decoded string gets passed into `strcmp`, so lets set a breakpoint
for that function. Once we trigger that breakpoint, we can see from the disassembly that
argument 1 for the function is the encrypted value (note that since this is the x86_64
fastcall convention, `rdi` and `rsi` are arguments 1 and 2, respectively). We can then
print out the memory at that pointer location, and that should get us the decrypted flag:

```
(gdb) break main
Breakpoint 6 at 0x40081d: file rev-200.c, line 20.
(gdb) run "GoldenTICKET{test?}"
Starting program: /home/brad/rev-200 "GoldenTICKET{test?}"

Breakpoint 6, main (argc=2, argv=0x7fffffffe198) at rev-200.c:20
20	in rev-200.c
(gdb) break strcmp
Breakpoint 7 at gnu-indirect-function resolver at 0x7ffff7a9b3e0: file ../sysdeps/x86_64/multiarch/strcmp.S, line 87.
(gdb) c
Continuing.
47

Breakpoint 7, __strcmp_sse2_unaligned () at ../sysdeps/x86_64/multiarch/strcmp-sse2-unaligned.S:24
24	../sysdeps/x86_64/multiarch/strcmp-sse2-unaligned.S: No such file or directory.
(gdb) x/s $rdi
0x602420:	"GoldenTICKET{b3773r_7h4n_md5_4m1r173?}"
(gdb) 
```

Now that sure looks like a flag! I hope you all enjoyed these reversing challenges!

Flag: 'GoldenTICKET{b3773r_7h4n_md5_4m1r173?}'

