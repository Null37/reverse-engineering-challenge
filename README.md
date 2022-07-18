# reverse-engineering-challenge

first of all test this binnary with file and strings commands to know what we have in our hand 
file command to know file type 

![file command](https://i.ibb.co/Y2cJT4Q/command-file-test.png)

we see **ELF 64-bit LSB executable and stripped**

ELF : Executable and Linkable Format


LSB: mean least-significant byte(little endian)

because we have stripped file, if we use strings comamnd  we cannot see symbols

![strings  command](https://i.ibb.co/jyvxh0C/strings-command.png)

i check the size we get 2.1M

![ls  command](https://i.ibb.co/hm0jBmG/ls-command.png)

this file is an elf file but an elf headers  can't reach the size of 2M

 i used strace command line to check syscall in run processor in this binnary to check can use buffroverflow or something can help to know what the code do
 
 
![strace command](https://i.ibb.co/jWRhZ4v/strace.png)


what we can see this binnary file use read and write syscalls

not fully undrestand what happen inside the code 




