## About
executable (i.e. executable file) is a file that contains instructions that a computer's OS can load into memory and execute as a program. Note 
it is not .exe file! Thats for windows but in Linux, executables dont use extention but use the **file's permissions, specifically the "execute"
bit).

executable consists primary of **machine code**, which are instructions encoded in a format that CPU can process. The format is os-specific so for Linux it uses
ELF (executable and linkable format) while windows use PE (portable executable) format.

for example, the executables are found in directories like `/bin`, `/usr/bin`,`/sbin`,`/usr/sbin`. Program like ls is a compiled executable file, 
typically located at /bin/ls. 

## diff between scripts
script (.sh file) contains human readble text commands. It **requires interpreter (like /bin/bash or /usr/bin/python) to read and execute the files.
The interpreter itself however is an exectuable.

## Whats /bin/bash?
It is path to Bash interpreter program itself. In windows thats like cmd or powershell file. So this executable reads and executes files. When u 
execute a script, ur running this /bin/bash executable and **passing ur script as input**.

## so if we have application code when we compile it becomes jar file like for java project so this isnt executable?
There is a diff between compiled language like c/c++ and how java works. Jar file is not a true executable.

JAR file contains java bytecode and other resources. V imptly, **it is not native machine code that CPU can execute directly**. 

1) needs interpreter: to run jar file u need interpreter (i.e. jvm)
2) true executable: so theres 2 definitions to exectuable. 1st is the machine code file and the 2nd is a file containing tbc
