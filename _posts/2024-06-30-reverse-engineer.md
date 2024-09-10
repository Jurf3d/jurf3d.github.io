---
title: "Reverse Engineer Guide For UTAR Students"
date: 2024-06-30 00:00:00 +0800
categories: [Reverse Engineer]
tags: [Reverse Engineer]
# image: /assets/posts/rev/
---
## <center>Reverse Engineering Basics and Pre-Requisites
Before we learn how to reverse engineering, we need to have a few basic pre-requisites: <br>
1. Basic understanding of C programming language <br>
2. Knowing how to code in python <br>                                    
3. Common sense <br>

Reverse engineering is usually the process of taking a program that has been
compiled and rewriting it in a way that is easier for humans to understand.
In CTF, we study the program behavior and algorithms and get the flag based
on our analysis. <br><br>
First off, we need to understand the executable files, which are programs that are ready to run on a computer.
Below will be the basics of how these executables are created and the common types you will encounter during CTF.
### <font color="#A5C9FF"> 1. Creation of Executable Files </font>
Executable files are created through a process called compilation and
linking. Here is a simplified breakdown: <br>
- Source Code: Programmers write code in high-level languages like C or
Python. <br>
- Compilation: The compiler translates this source code into assembly code.
<br>
- Assembly: The assembler then converts the assembly code into machine code,
stored in object files. <br>
- Linking: The linker combines these object files into a single executable
file, adding necessary information about the environment in which the
program will run. <br>

![Desktop View](/assets/posts/rev/image1.png){: width="972" height="589" }
_Flow of the creation of the executable file_
<br>During this process, some information, such as comments and certain labels
are lost. Reverse engineering involves using knowledge and experience to
recover some of this lost information.
### <font color="#A5C9FF"> 2. Types of Executable Files </font>
Different operating systems use different formats for executable files: <br>
- PE (Portable Executable): Used by `Windows`, contains headers, section tables,
and tables for imports and exports (like those used in Dynamic Linker Library (DLLs)). <br>
- ELF (Executable and Linkable Format): Used by `Linux`, includes headers,
section data, and symbol tables. <br>

Executable files are divided into sections, like `.text` for code and `.data`
for data. These sections are mapped to memory segments based on permissions
(read, write, execute). Performing an illegal operation like writing to a
read-only segment causes a `Segmentation Fault`. <br>

---

## <center>Common Assembly Instructions for CTF
When reverse engineering a program, you will often need to understand assembly language as machine code is directly generated from it. Here is a basic introduction to some key assembly concepts to help you get started.<br>

### <font color="#A5C9FF">1. Registers</font>
Registers are small, fast storage areas within the CPU used to hold instructions, data or addresses temporarily. Understanding these is crucial because they play a significant role in how a program operates. The x86/x64 assembly instructions follow the following basic format: `OP Code [operand 1] [operand 2]` <br><br>
In a typical 32-bit processor (like Intel's IA-32 or x86 architecture), you will encounter several important registers:<br>
- General Purpose Registers: `EAX, EBX, ECX, EDX, ESI, EDI`
- Stack Registers: `ESP (Top-of-Stack Pointer), EBP (Bottom-of-Stack Pointer)`
- Instruction Pointer: `EIP (holds the address of the next instruction to execute)`
- Segment Registers: `CS, DS, SS, ES, FS, GS`

| General Purpose Registers          | Name             |
| :----------------------------------| :--------------- |
| E`A`X                              | `A`ccumulator    |
| E`B`X                              | `B`ase           |
| E`C`X                              | `C`ounter        |
| E`D`X                              | `D`ata           |

Here is a [cheat sheet for x86](https://cheatography.com/siniansung/cheat-sheets/linux-assembler/) if you wish to understand more.

The `flag register` in a CPU is a special register where each bit represents a specific condition or result from an arithmetic or logical operation. These flags are used by the CPU to make decisions during program execution. Here are some commonly used flags:

- AF (Auxiliary Carry Flag): This flag is set to 1 when a carry occurs to the `third digit` during an arithmetic operation.
- PF (Parity Flag): This flag is set to 1 if the number of 1s in the lowest byte of the result is even, and 0 if it is odd.
- SF (Sign Flag): This flag is set to 1 when the result of an operation is negative, indicated by the most significant bit (the sign bit) being 1.
- ZF (Zero Flag): This flag is set to 1 if the result of an operation is zero which indicates that the operation resulted in no value.

For 64-bit processors (x86-64), the `E` prefix in register names changes to `R` (e.g., EAX becomes RAX, EBX becomes RBX) and additional registers (R8 to R15) are introduced. Cheat sheet for [x64 bit](https://cs.brown.edu/courses/cs033/docs/guides/x64_cheatsheet.pdf) if you wish to learn more.

### <font color="#A5C9FF">2. Common Instructions for CTF </font>
Only a basic understanding is needed for beginners. No need to memorize, it all comes to practice. You can practice reversing assembly problems [here](https://github.com/kablaa/CTF-Workshop/blob/master/Reversing/Challenges/IfThen/if_then).

| Type of command                    | OP Code | Examples         | Results                                 |
|---------------------------------|----------------|------------------------------|------------------------------------------------------|
| **Data transfer instructions**   | `mov`          | `mov rax, rbx`               | Copies the value of `rbx` into `rax`.                |
|                                 |                | `mov qword ptr [rdi], rax`   | Stores the value of `rax` at the address pointed to by `rdi`. |
| **Addressing instructions**      | `lea`          | `lea rax, [rsi]`             | Loads the effective address from `rsi` into `rax`.   |
| **Arithmetic instructions**      | `add`          | `add rax, rbx`               | Adds the value of `rbx` to `rax`.                    |
|                                 |                | `add qword ptr [rdi], rax`   | Adds the value of `rax` to the data at the address pointed to by `rdi`. |
|                                 | `sub`          | `sub rax, rbx`               | Subtracts the value of `rbx` from `rax`.             |
| **Logical operations instructions** | `and`      | `and rax, rbx`               | Performs a bitwise AND operation between `rax` and `rbx`, storing the result in `rax`. |
|                                 | `xor`          | `xor rax, rbx`               | Performs a bitwise XOR operation between `rax` and `rbx`, storing the result in `rax`. |
| **Function call instructions**   | `call`         | `call 0x401000`              | Calls the function located at the address `0x401000`.|
| **Function return instructions** | `ret`          | `ret`                        | Returns from the current function.                   |
| **Compare instructions**         | `cmp`          | `cmp rax, rbx`               | Compares `rax` and `rbx`, updating flags based on the result. |
| **Unconditional jump command**   | `jmp`          | `jmp 0x401000`               | Jumps to the instruction at address `0x401000`.      |
| **Stack operation instructions** | `push`         | `push rax`                   | Pushes the value of `rax` onto the stack.            |
|                                 | `pop`          | `pop rax`                    | Pops the top value from the stack into `rax`.        |

<br>

| Conditional jump instructions   | Explanation of the instructions                    | Usage             | Flag condition |
|--------------|---------------------------------------------|-----------------------------------|--------------------------|
| `jz/je`      | Jump if zero/equal                          | Jumps if `a == b`                 | ZF = 1 |
| `jnz/jne`    | Jump if not zero/equal                      | Jumps if `a != b`                 | ZF = 0 |
| `jb/jnae/jc` | Jump if below/not above or equal/carry      | Jumps if `a < b`, for unsigned numbers | CF = 1 |
| `ja/jnbe`    | Jump if above/not below or equal            | Jumps if `a > b`, for unsigned numbers |
| `jna/jbe`    | Jump if not above/below or equal            | Jumps if `a <= b`, for unsigned numbers |
| `jnc/jnb/jae`| Jump if not carry/not below/above or equal  | Jumps if `a >= b`, for unsigned numbers | CF = 0 |
| `jg/jnle`    | Jump if greater/not less or equal           | Jumps if `a > b`, for signed numbers |
| `jge/jnl`    | Jump if greater or equal/not less           | Jumps if `a >= b`, for signed numbers |
| `jl/jnge`    | Jump if less/not greater or equal           | Jumps if `a < b`, for signed numbers |
| `jle/jng`    | Jump if less or equal/not greater           | Jumps if `a <= b`, for signed numbers |
| `jo`         | Jump if overflow                            | Jumps if there is an overflow     | OF = 1 |
| `js`         | Jump if signed                              | Jumps if the sign flag is set     | SF = 1 |

---
## <center>Common Tools for CTF
The tools that are frequently used in reverse engineering are introduced in this section.

### <font color="#A5C9FF">1. Ghidra and IDA </font>
We will be using `Ghidra` for this guide. If you want to change to other tools like `IDA`, you can easily do so. Ghidra is a free, open-source reverse engineering tool developed by the NSA. It's a strong alternative to IDA Pro, another popular but expensive reverse engineering tool. While Ghidra's user interface might not be as polished as IDA Pro, it offers almost all of the same essential features. IDA Pro also has a free version, but it lacks many advanced features, making it less suitable for complex projects. <br><br>

Ghidra and IDA Pro are both used to analyze compiled binaries from various programming languages and processor architectures. The main purpose of Ghidra is to `decompile` these binaries, turning them back into an approximate version of the source code. This is particularly useful for understanding the logic of the program. While it excels at decompiling into C and C++ code, Ghidra can handle binaries from other languages as well. Additionally, Ghidra can disassemble binaries, converting machine code back into assembly language.<br><br>

Ghidra offers a wide range of features, including `disassembly, decompilation, graphing, and scripting`. These tools help researchers analyze software behavior, identify vulnerabilities, and detect malicious activities, all without needing access to the original source code. Its interactive and customizable interface makes it a powerful tool for in-depth software analysis.

### <font color="#A5C9FF">2. OllyDbg & x64dbg </font>
`OllyDbg` is a good debugger for Windows 32-bit as its extensibility is a powerful feature. Unfortunately, OllyDbg is no longer compatible with 64-bit, therefore a lot of users have shifted to `x64dbg`.

### <font color="#A5C9FF">3. GNU Debugger (GDB) </font>
GDB (GNU Debugger) is a command-line tool used for debugging programs. It offers powerful features, including the ability to perform source-level debugging for programs that include debug symbols, which make it easier to understand and fix issues in the code. GDB can be extended with additional functionality through Python-based extensions like `gdb-peda, pef, and pwndbg` which enhance its capabilities and make debugging more efficient.

### <font color="#A5C9FF">4. dnSpy </font>

---

## <center>Reversing Technique - Static Analysis
Static analysis is a fundamental reverse engineering technique that involves examining a binary file without actually running it. Instead, you analyze the binary's machine instructions and other information directly. This method allows you to understand how the program works, identify potential vulnerabilities, and gather insights without executing any code. In this section, we will be introducing the usual methods to solve reverse engineering challenges based on `Ghidra`. <br>

### <font color="#A5C9FF">1. Introduction to the Usage of Ghidra </font>
Ghidra should come pre-installed in your Kali Linux. If it is not installed, please follow this [link](https://github.com/NationalSecurityAgency/ghidra) and follow the steps to install it. <br><br>
To run Ghidra, simply type `ghidra` on your `terminal`. After that, the application will show up.

![Desktop View](/assets/posts/rev/image2.png){: width="972" height="589" }
_Ghidra Project Window_

To create a project and start to import `ELF` or `exe` files, follow the steps below: <br>

1. Click on `File` then `New Project` on the `top left` corner of the application
2. Click on `Non-Shared Project` then click `Next`
3. Enter a project name, then click `Finish`

You have successfully created a project. <br><br>
For this part, you can follow along if you want. Open a text editor in your Kali Linux. If you have not install a text editor in your Kali, please do so. In this guide, we will be using `Sublime Text`, you are free to use `Visual Studio Code` or any other application, it does not matter. Next, we will code a simple program and import it into Ghidra and then compare the decompiled code with original source code. <br>

```c
#include <stdio.h>
#include <string.h>

int main() {
    char password[20];
    printf("Enter the password: ");
    scanf("%s", password);

    if (strcmp(password, "s3cr3t_p@ss") == 0) {
        printf("Correct!\n");
    } else {
        printf("Wrong!\n");
    }

    return 0;
}
```
Copy the code into your text editor. Next, open a terminal in the location where you stored the source file and run the following command to compile it using `gcc`. It will produce an executable binary output file named `hello`. <br>

```bash
gcc helloprogram.c -o hello -g -no-pie
```

When you run the program on the terminal `./hello`, the program asked you to enter the password. Since you are the one who compiled the program, you know the password. But in CTF, the source code will `NOT` be provided, hence you need to figure it out in `Ghidra`. <br>

In CTF, `ALWAYS` run `file check` on the binary given first, because it gives essential information on what kind of program you are dealing with.

```bash
└─$ file hello                            
hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=839512af4378c4c8d2a2ccd564919948e24b3742, for GNU/Linux 3.2.0, with debug_info, not stripped
```

The most important part will be the `ELF 64-bit LSB executable`, which shows that it is a `x64` binary program and also it shows that it is `not stripped`, which means it is easier to reverse the program. <br>

Now to `import` the files that you want to reverse, or in this case, the `hello` program: <br>

1. Click on `File` then `Import File`
2. Select the files that you want to reverse
3. Click `Ok`
4. On `Import Result Summary`, click `Ok`
5. Double click on the file that you just uploaded
6. Click on `Yes` to analyze the file

![Desktop View](/assets/posts/rev/image3.png){: width="972" height="589" }

![Desktop View](/assets/posts/rev/image4.png){: width="972" height="589" }

The interface (CodeBrowser) shown is divided into multiple sections, each of which is explained below: <br>

TLDR: Focus on `2, 4 and 5`. <br>
1. Program Tree: Allows a program to be organized into a hierarchy of folders and fragments. This part contains the `memory map` which allows you to jump to different parts of memory. For now, `don't worry about this`.
2. Symbol Tree: This is very `important`. It contains all the functions that have been written by the author.
3. Data Type Manager: Allows users to locate, organize, and apply data types to a program. Read more [here](https://scrapco.de/ghidra_docs/Base/topics/DataTypeManagerPlugin/data_type_manager_description.htm). Also, `don't worry about this` for now.
4. Disassembly Window: This is also `important`. It shows the disassembly for the binary. This is also why you need to have a basic understanding of `Assembly` language.
5. Decompiler: This is very `important`. It shows the `decompiled` view of any functions found in the binary that it has detected. It would be empty at first since no function would be chosen. In short, this is where `decompiled pseudo-C code lives`.
6. Console: As its name suggest, it is a `console` window. For now, `don't worry about this`.

Now, to reverse the program, click on `Functions` in the `Symbol Tree`. Tips for CTF, always look for the `main` function first, as it contains the main logic of the program. In case there is no `main` function, look for `entry` function. This happen is because it is a `stripped` binary. In this case, it is a `not stripped` binary as mentioned above. <br>

#### Side Note: The difference in symbol tree between stripped and not stripped binary.
TLDR:
- Not stripped: program is `easier` to read and reverse
- Stripped: program is `harder` to read and reverse

![Desktop View](/assets/posts/rev/image6.png){: width="300" height="200" }
_Not Stripped Symbol Tree_
![Desktop View](/assets/posts/rev/image7.png){: width="300" height="300" }
_Stripped Symbol Tree_

<br>Anyway, back to reversing the program. As we can see, after pressing the `main` function in the `Symbol Tree`, the `Decompiler` shows us the `pseudo-code` for the program that we just wrote. 

![Desktop View](/assets/posts/rev/image5.png){: width="972" height="589" }

To make it even more readable, we can also `rename` some of the variables or functions that we know. We can do this by `clicking` a `variable` and type `l` on the keyboard to rename it whatever you like.

![Desktop View](/assets/posts/rev/image8.png){: width="400" height="400" }
_Renaming a variable or function_
![Desktop View](/assets/posts/rev/image9.png){: width="400" height="400" }
_After renaming the variables_

As we can see, it is so much more readable and easier to understand the logic of the program now. Now we know that the user prompts the user to enter a `password`, then it `compares` the input with `s3cr3t_p@ss`. If the input is correct, it shows correct otherwise it shows wrong. We can also double-check the password by running the program and input `s3cr3t_p@ss`. <br>

```bash
└─$ ./hello
Enter the password: s3cr3t_p@ss
Correct!
```

And there we go, we have successfully figure out the password for the program. Of course, in CTF, it will not be this easy (unless you are playing beginner CTF). As CTF is becoming more and more popular in cybersecurity, the challenges will be getting harder and harder, even the challenges for local CTF are getting harder. But do not be discouraged, as you gradually continue to play, it will be easier and faster for you to reverse. <br>

### <font color="#A5C9FF">2. Common Code Recognition </font>
Some developed algorithms show up a lot in CTF's reverse engineering challenges. We can examine them considerably more quickly if we can recognize these algorithms. Table below show some of the examples: <br>

| Encryption Algorithm   | Pseudo-code                                                                                           | Description                        |
|:-------------:|:----------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| **RC4**     | `j = (i + 1) % 256;`<br>`j = j + s[i]) % 256;`<br>`swap(s[i], s[j]);`<br>`t = (s[i] + s[j]) % 256;` | Keystream generation  |
| **Base64**  | `b1 = c1 >> 2;`<br>`b2 = ((c1 & 0x3) << 4) | (c2 >> 4);`<br>`b3 = ((c2 & 0xF) << 2) | (c3 >> 6);`<br>`b4 = c3 & 0x3F;`  | Converts 8-bit to 6-bit             |
| **MD5**     | `(X & Y) | (~X & Z)`<br>`(X & Z) | (Y & ~Z)`<br>`X ^ Y ^ Z`<br>`Y ^ (X | (~Z))`                                             | [F functions <br> G functions<br> H functions<br> I functions<br>](https://www.comparitech.com/blog/information-security/md5-algorithm-with-examples/)           |
| **AES**     | `x[j] = s[i][(j+i) % 4]`<br>`4 cycles`<br>`s[i][j] = x[j]`<br>`4 cycles`<br>`Overall 4 cycles`                               | Includes ShiftRows operation       |
| **DES**     | `L = R`<br>`R = F(R, K) ^ L`                                                                                               | Uses Feistel structure             |

You may also read more on this topic [here](https://ctf-wiki.mahaloz.re/reverse/Identify-Encode-Encryption/introduction/#:~:text=Encryption%20algorithms%20commonly%20found%20in,%2C%20MD5%2C%20and%20so%20on.).
