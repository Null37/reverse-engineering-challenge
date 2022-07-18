# reverse-engineering-challenge

first of all test this binnary with file and strings commands to know what we have in our hand 
file command to know file type 

![file command](https://i.ibb.co/Y2cJT4Q/command-file-test.png)

we see **ELF 64-bit LSB executable and stripped**
ELF : Executable and Linkable Format


LSB: mean least-significant byte(little endian)

because we have stripped file, if we use strings comamnd  we cannot see symbols

![strings  command](https://i.ibb.co/jyvxh0C/strings-command.png)

