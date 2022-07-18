First of all test, this binary with file and strings commands to know what we have in our hand file command to know the file type

[https://camo.githubusercontent.com/c6e4657e1bfbca2cac7a2240b3f0eecd4e4f34d2f6a40a44aaf1f61d760fb40c/68747470733a2f2f692e6962622e636f2f5932634a5434512f636f6d6d616e642d66696c652d746573742e706e67](https://camo.githubusercontent.com/c6e4657e1bfbca2cac7a2240b3f0eecd4e4f34d2f6a40a44aaf1f61d760fb40c/68747470733a2f2f692e6962622e636f2f5932634a5434512f636f6d6d616e642d66696c652d746573742e706e67)

we see **ELF 64-bit LSB executable and stripped**

ELF: Executable and Linkable Format

LSB: mean least-significant byte(little endian)

because we have stripped file, if we use strings command we cannot see symbols

[https://camo.githubusercontent.com/0c6935c6a4134132a9413b496208c56e6e87630bdf3bd5d229bf32e8fb555941/68747470733a2f2f692e6962622e636f2f6a7976786830432f737472696e67732d636f6d6d616e642e706e67](https://camo.githubusercontent.com/0c6935c6a4134132a9413b496208c56e6e87630bdf3bd5d229bf32e8fb555941/68747470733a2f2f692e6962622e636f2f6a7976786830432f737472696e67732d636f6d6d616e642e706e67)

i check the size we get 2.1M

[https://camo.githubusercontent.com/acad2e60f85241b734f5ae2784d4f8a99d93daa84a5dabcd2f7263521e8949d7/68747470733a2f2f692e6962622e636f2f686d306a426d472f6c732d636f6d6d616e642e706e67](https://camo.githubusercontent.com/acad2e60f85241b734f5ae2784d4f8a99d93daa84a5dabcd2f7263521e8949d7/68747470733a2f2f692e6962622e636f2f686d306a426d472f6c732d636f6d6d616e642e706e67)

this file is an elf file but elf headers can't reach the size of 2M

I used strace command line to check syscall in run processor in this binary to check if can use buffer overflow or something can help to know what the code does

[https://camo.githubusercontent.com/01c1a819d4397e67cf4d51631d6d48cd63afdccad27233d6606bc153e91979df/68747470733a2f2f692e6962622e636f2f6a5752685a34762f7374726163652e706e67](https://camo.githubusercontent.com/01c1a819d4397e67cf4d51631d6d48cd63afdccad27233d6606bc153e91979df/68747470733a2f2f692e6962622e636f2f6a5752685a34762f7374726163652e706e67)

what we can see in this binary file use read and write syscalls.

after that, i run checksec to ****Identify security properties to know more about  memory security**** 

![Screen Shot 2022-07-18 at 5.47.20 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a1b15dbd-2c7f-49a8-85c7-a2be982c1792/Screen_Shot_2022-07-18_at_5.47.20_PM.png)

we can see in the screenshot the NX(do not execute) is enabled

-**NX**: The NX (do not execute) bit is a technology used in CPUs that guarantees that certain memory areas (such as the stack and heap) are not executable, and others, such as the code section, cannot be written.

for more information what happened inside the code,  I use ghidra to check if I can almost get code

![https://i.ibb.co/hRGfKHY/ghidra.png](https://i.ibb.co/hRGfKHY/ghidra.png)

I read assembly and turn it to code c but after testing the code doesn't make sense and go to binary ninja to see if can read the assembly code because in ghidra nothing was clear.

//code c

binary ninja result 

//image here

assembly code 

```jsx
   0x400144:    mov    rax,0x1
   0x40014b:    mov    rdi,0x1
   0x400152:    movabs rsi,0x6000000
   0x40015c:    mov    rdx,0x6
   0x400163:    syscall 
   0x400165:    xor    rax,rax
   0x400168:    xor    rdi,rdi
   0x40016b:    movabs rbx,0x6000400
   0x400175:    mov    rsi,rbx
   0x400178:    mov    rdx,0x8
   0x40017f:    syscall 
   0x400181:    rdtsc  
   0x400183:    xor    rax,rdx
   0x400186:    mov    rdi,rbx
   0x400189:    mov    rcx,0x0
   0x400190:    mov    rdi,rbx
   0x400193:    add    rdi,rcx
   0x400196:    xor    BYTE PTR [rdi],al
   0x400198:    inc    rcx
   0x40019b:    cmp    rcx,0x8
   0x40019f:    jl     0x400190
   0x4001a1:    mov    rax,QWORD PTR [rbx]
   0x4001a4:    push   rax
   0x4001a5:    sub    rsp,0x20
   0x4001a9:    mov    rax,0x1
   0x4001b0:    mov    rdi,0x1
   0x4001b7:    movabs rsi,0x6000007
   0x4001c1:    mov    rdx,0xa
   0x4001c8:    syscall 
   0x4001ca:    xor    rax,rax
   0x4001cd:    xor    rdi,rdi
   0x4001d0:    mov    rsi,rsp
   0x4001d3:    mov    rdx,0x100
   0x4001da:    syscall 
   0x4001dc:    add    rsp,0x20
   0x4001e0:    mov    rdi,0x1
   0x4001e7:    mov    rsi,0x3
   0x4001ee:    mov    rdx,0x3
   0x4001f5:    mov    rcx,0x7
   0x4001fc:    cmp    rax,0x20
   0x400200:    jle    0x400214
   0x400202:    pop    r11
   0x400204:    xor    r11,QWORD PTR [rbx]
   0x400207:    jne    0x400214
   0x400209:    mov    QWORD PTR [rsp+0x8],0x23
   0x400212:    retfq  
   0x400214:    mov    rax,0x3c
   0x40021b:    mov    rbx,0x0
   0x400222:    syscall
```

first **instructions** 

```jsx
	 0x400144:    mov    rax,0x1
   0x40014b:    mov    rdi,0x1
   0x400152:    movabs rsi,0x6000000
   0x40015c:    mov    rdx,0x6
   0x400163:    syscall
```

is write syscall to write in 1(output) word “input\n” with 6 characters  

And the second syscall

```jsx
	 0x400165:    xor    rax,rax
   0x400168:    xor    rdi,rdi
   0x40016b:    movabs rbx,0x6000400
   0x400175:    mov    rsi,rbx
   0x400178:    mov    rdx,0x8
   0x40017f:    syscall 
```

is read syscall to  read from input 8 characters and move  value  in address to register

```jsx
read(0, &buff, 8)
```

```jsx
	 0x400181:    rdtsc  
   0x400183:    xor    rax,rdx
```

rdtsc(Read Time-Stamp Counter) Reads the current value of the processor’s time-stamp counter (a 64-bit MSR) into the RDX or RAX registers

in line 

```jsx
0x400186:    mov    rdi,rbx
```

rbx has input value and in this line move the value to rdi

```jsx
	 0x400189:    mov    rcx,0x0
   0x400190:    mov    rdi,rbx
   0x400193:    add    rdi,rcx
   0x400196:    xor    BYTE PTR [rdi],al
   0x400198:    inc    rcx
```

set rcx to 0 and increment like( i= 0 and i++)

```jsx
   0x4001a9:    mov    rax,0x1
   0x4001b0:    mov    rdi,0x1
   0x4001b7:    movabs rsi,0x6000007
   0x4001c1:    mov    rdx,0xa
   0x4001c8:    syscall 
```

write syscall write 10 characters “checkser:\n” 

```jsx
	 0x4001ca:    xor    rax,rax
   0x4001cd:    xor    rdi,rdi
   0x4001d0:    mov    rsi,rsp
   0x4001d3:    mov    rdx,0x100
   0x4001da:    syscall 
```

read syscall to read 256 

rsp + 32 to move stack register to point rsp + 32

```jsx
  0x4001dc:    add    rsp,0x20
   0x4001e0:    mov    rdi,0x1
   0x4001e7:    mov    rsi,0x3
   0x4001ee:    mov    rdx,0x3
```

compare the number of character in checker input with 32 character 

```jsx
 0x4001fc:    cmp    rax,0x20
```

if not true jump to 0x400214 

```jsx
0x400200:    jle    0x400214
```

```jsx
   0x400214:    mov    rax,0x3c
   0x40021b:    mov    rbx,0x0
   0x400222:    syscall
```

0x400214 is exit syscall exit  from the programme 

if the true number of characters equals 32 go to && (condition two)

```jsx
   0x400202:    pop    r11
   0x400204:    xor    r11,QWORD PTR [rbx]
```

Get top of the stack that RAX push it above and stores in r11.

0x400204 xor every character with old RBX(old input in input: after change)

if not equal go 

```jsx
jle    0x400214
```

exit the process. if 2 condition is true 

```jsx
	 0x400209:    mov    QWORD PTR [rsp+0x8],0x23
   0x400212:    retfq
```

  after debugging  i realised the rsp(stack pointer) + 8  for change the cs(code segment)  value 

and retfq update the CS and the programme go from 64 bit to 32bit 

retfq update the CS and execute what is in the top stack.

//image debug

For debugging I used the GDB-PEDA to speed up the exploit
