A product of self-learning compiler & interpreter construction. It consists of a handcrafted recursive descent parser, an interpreter and a code generator emitting x86 assembly, which can be further assembled and linked to form native executables.
```
+------------------------------------------------------------------------------+
|                                                                              |
|                     scanner*/parser*                                         |
|  PL/0 source code -------------------> concrete parse tree*                  |
|                                                 |                            |
|                                                 | AST generator*             |
|                                                 |                            |
|                                                 V                            |
|            interpreter* <............. abstract syntax tree*                 |
|                                                 |                            |
|                                                 | x86 assembly generator*    |
|                                                 |                            |
|                                                 V                            |
|                                            x86 assembly                      |
|                                                 |                            |
|                                                 | NASM assembler             |
|                                                 |                            |
|                                                 V                            |
|                                            object file                       |
|                                                 |                            |
|                                                 | GCC (serves as a linker)   |
|                                                 |                            |
|                                                 V                            |
|        operating system <.............   native executable                   |
|                                                                              |
|                                                                              |
| *    implemented in Python                                                   |
| -->  code transformation                                                     |
| ..>  executed by                                                             |
|                                                                              |
+------------------------------------------------------------------------------+
```

Directory layout:
```
build/                          ... generated stuff
    cygbin/                     ... executables for Cygwin from build/cygobj
    cygobj/                     ... object files for Cygwin from build/x86asm
    elfbin/                     ... executables for ELF/Linux from build/elfobj
    elfobj/                     ... object files for ELF/Linux from build/x86asm
    x86asm/                     ... x86 assembly from PL/0 source code under pl0
lib/                            ... I/O routine
    cygobj/                     ... asm_io.o, linked with build/cygobj
    elfobj/                     ... asm_io.o, linked with build/elfobj
    x86asm/
        asm_io.asm              ... included by build/x86asm
        asm_io.inc
pl0/                            ... PL/0 example code
    factorial.pl0               ... factorial using recursion
    gcd.pl0                     ... greatest common divisor
    minimal.pl0                 ... minimal legal PL/0 program
    square.pl0
    uninitialized.pl0           ... an example with legal syntax but wrong semantics
src/                            ... Python implementation
    pypl0_astgen.py             ... AST generator
    pypl0_astnodes.py           ... AST nodes definition
    pypl0_interp.py             ... AST interpreter
    pypl0_main.py               ... *ENTRY POINT* of the system
    pypl0_parser.py
    pypl0_scanner.py
    pypl0_utils.py              ... pretty print the parse tree and the AST
    pypl0_x86codegen.py         ... x86 assembly generator
    pypl0_x86codegentpl.py      ... string templates used by pypl0_x86codegen
unittest/                       ... UT code, to be added...
Makefile.cygwin                 ... Makefile for Cygwin
Makefile.elf                    ... Makefile for ELF/Linux
Makefile.in                     ... included Makefile
README                          ... this documentation
```

Just grab everything by
```
svn checkout http://pypl0.googlecode.com/svn/trunk/ pypl0-read-only
```
.

Main functionalities are centralized in src/pypl0\_main.py, Run
```
python src/pypl0_main.py -h
```
for help. Python 2.5 or higher is needed.

The makefiles are used to facilitate building native executables from PL/0
source code. Currently Cygwin and Linux/ELF formats are supported. Just rename
the right makefile to "Makefile" or use the "-f" option to designate the right
one (see below), and type "make" in the command line under the top directory. Note that you need Python 2.5 or higher, NASM 2.07 (http://nasm.us, NASM 2.00 should suffice but not tested) and GCC 4.3 for the makefile to work.

The PL/0 source code can be either interpreted, or compiled to native code to be executed. For example, on a Linux box
```
python src/pypl0_main.py interp pl0/factorial.pl0
```
and
```
make -f Makefile.elf
build/elfbin/factorial.pl0
```
yield the same result.