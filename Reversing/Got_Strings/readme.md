# Got Strings? (Reversing - 50 Points)
## Challenge & Writeup Courtesy of [Brad Campbell](https://twitter.com/hackersoup)

> [rev-50](rev-50)

Solution
--------

The first step to generally take when working with a binary file is to figure out what it is.
Running the `file` command on it can give us some preliminary information:

```
$ file rev-50
rev-50: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU Linux 2.6.32, BuildID[sha1]=131af12d2785216917c0ba7ec0f1398c9260b73f, not stripped
```

This tells us some important things.
1. The file is ELF format
2. It is 64-bit x86 assembly
3. It isn't stripped

Now that we have that out of the way, lets run the program on a linux VM.

```
root@ce2ee9e83517:/opt# ./rev-50
usage: ./rev-50 <flag>
verifies the flag provided, alerting if the flag is real or not.
root@ce2ee9e83517:/opt# ./rev-50 "GoldenTICKET{test?}"
Invalid flag. To the gulag with you!
```

Lets take a quick peek under the hood with radare to see what is going on.

```
root@ce2ee9e83517:/opt# r2 ./rev-50
[0x004004c0]> aaa
[0x004004c0]> s main
[0x004005b6]> pdf
/ (fcn) sym.main 135
|           0x004005b6   push rbp
|           0x004005b7   mov rbp, rsp
|           0x004005ba   sub rsp, 0x20
|           0x004005be   mov [rbp-0x14], edi
|           0x004005c1   mov [rbp-0x20], rsi
|           0x004005c5   mov qword [rbp-0x8], str.GoldenTICKETy0u_c4ll_7h15_53cur3
|           0x004005cd   cmp dword [rbp-0x14], 0x2
|       ,=< 0x004005d1   jz 0x4005fd
|       |   0x004005d3   mov rax, [rbp-0x20]
|       |   0x004005d7   mov rax, [rax]
|       |   0x004005da   mov rsi, rax
|       |   0x004005dd   mov edi, str.usagesflag
|       |   0x004005e2   mov eax, 0x0
|       |   0x004005e7   call sym.imp.printf
|       |      sym.imp.printf(unk)
|       |   0x004005ec   mov edi, str.verifiestheflagprovidedalertingiftheflagisrealornot.
|       |   0x004005f1   call sym.imp.puts
|       |      sym.imp.puts()
|       |   0x004005f6   mov eax, 0x1
|      ,==< 0x004005fb   jmp loc.0040063b
|      |`-> 0x004005fd   mov rax, [rbp-0x20]
|      |    0x00400601   add rax, 0x8
|      |    0x00400605   mov rdx, [rax]
|      |    0x00400608   mov rax, [rbp-0x8]
|      |    0x0040060c   mov rsi, rdx
|      |    0x0040060f   mov rdi, rax
|      |    0x00400612   call sym.imp.strcmp
|      |       sym.imp.strcmp()
|      |    0x00400617   test eax, eax
|     ,===< 0x00400619   jnz 0x40062c
|     ||    0x0040061b   mov edi, str.Validflag.Yougetthegoldenticket
|     ||    0x00400620   call sym.imp.puts
|     ||       sym.imp.puts()
|     ||    0x00400625   mov eax, 0x0
|           ; CODE (CALL) XREF from 0x0040063b (fcn.0040058c)
|    ,====< 0x0040062a   jmp loc.0040063b
|    |`---> 0x0040062c   mov edi, str.Invalidflag.Tothegulagwithyou
|    | |    ; CODE (CALL) XREF from 0x00400470 (fcn.0040046c)
|    | |    0x00400631   call sym.imp.puts
|    | |       sym.imp.puts()
|    | |    0x00400636   mov eax, 0x1
|    | |    ; CODE (CALL) XREF from 0x0040062a (fcn.0040058c)
|    | |    ; CODE (CALL) XREF from 0x004005fb (fcn.0040058c)
|- loc.0040063b 2
|    `-`--> 0x0040063b   leave
\           0x0040063c   ret
[0x004005b6]>
```

Off the bat we can see some interesting parts. A call to `strcmp` at `0x00400612`
points us towards a solution. Looking at the args to `strcmp` we see `[rbp-0x8]` being passed
as argument 1, and `[rbp-0x20]` as argument 2. Local variable `0x20` is argv[1] to the program,
as can be seen at the end of the function prolog when `mov [rbp-0x20], rsi` is executed (this 
program is using the x64 fastcall calling convention).

Following variable `0x8` we can see it assigned earlier in the program with
`mov qword [rbp-0x8], str.GoldenTICKETy0u_c4ll_7h15_53cur3`. R2 automatically builds
the variable name based on the contents of that string, and it looks suspiciously like a flag...
Lets check out that part of memory.

```
[0x004005b6]> psz @ str.GoldenTICKETy0u_c4ll_7h15_53cur3
GoldenTICKET{y0u_c4ll_7h15_53cur3?}
[0x004005b6]>
```

Well that sure looks like a flag. Lets try it out.

```
root@ce2ee9e83517:/opt# ./rev-50 "GoldenTICKET{y0u_c4ll_7h15_53cur3?}"
Valid flag. You get the golden ticket!
```

Alright! Solved the challenge.
Flag: 'GoldenTICKET{y0u_c4ll_7h15_53cur3?}'

