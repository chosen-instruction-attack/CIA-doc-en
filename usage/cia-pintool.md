---
description: Dynamic instrumentation with Intel Pin to record execution trace
---

# CIA Pintool

After filling out `config.json`, run:

```shell-session
$ PINDIR/pin -t <pintool.dll> <args> -- <name.exe>
```

There are 6 pintools in all for test and use.

### 0x1 instracelog

This one is to record every instruction executed between anchor instructions. 2 modes:

* **Script :**        Adapted to the toolchain processing.
* **Readble :**    Easy to read and used for testing.

**Path :** `Pintool/instracelog.cpp`

**Command line arguments ：**

* \-i       A text file whose content is an anchor instruction, like `cmpxchg eax, eax`
* \-o      Filename of the output trace

**Script mode instruction format ：**

```
address; instruction length; EAX; EBX; ECX; EDX; ESI; EDI; ESP; EBP; EFLAGS; memory reading address; memory writing address; 0;
```

**Readable mode instruction format ：**

```
address; assembly; EAX; EBX; ECX; EDX; ESI; EDI; ESP; EBP; EFLAGS; memory reading address; memory writing address;
```

PS: For memory reading address and memory writing address, the value is 0 if there is no memory reading or writing operation. Same for other pintools below.

### 0x2 instracelog\_detail

Add hex and assembly information.

**Path :** `Pintool/instracelog_detail.cpp`

**Command line arguments ：**

* \-i       A text file whose content is an anchor instruction, like `cmpxchg eax, eax`
* \-o      Filename of the output trace

**Instruction format ：**

```
address; instruction length; EAX; EBX; ECX; EDX; ESI; EDI; ESP; EBP; EFLAGS; memory reading address; memory writing address; hex; assembly
```

### 0x3 instracelog\_64

Version for x86\_64 programs.

**Path :** `Pintool/instracelog_64.cpp`

**Command line arguments ：**

* \-i       A text file whose content is an anchor instruction, like `cmpxchg eax, eax`
* \-o      Filename of the output trace

**Instruction format ：**

```
address; instruction length; RAX; RBX; RCX; RDX; RSI; RDI; RSP; RBP; R8; R9; R10; R11; R12; R13; R14; R15; RFLAGS; memory reading address; memory writing address; hex; assembly
```

### 0x4 instracelog\_full

Record all instructions executed, including those beyond anchor instructions.

**Path :** `Pintool/instracelog_full.cpp`

**Command line arguments ：**

* \-o      Filename of the output trace

### 0x5 instracelog\_insdel

Some anchor instructions, like "syscall", will affect the execution environment and bring unwanted effects. This pintool can remove the anchor instruction at runtime with API `INS_DELETE` to avoid such effects.

**Path :** `Pintool/instracelog_insdel.cpp`

**Command line arguments ：**

* \-s      The C source code of the sample
* \-i       A text file whose content is an anchor instruction, like `cmpxchg eax, eax`
* \-o      Filename of the output trace

### 0x6 anchorfinder

This is used to determine if an instruction is an anchor instruction when the size of trace is too large for local storage. 2 modes for determinnation: (<mark style="color:red;">details</mark>)

* **3anchor :**        If an instruction occurs 3 times
* **retanchor :**     If the previous instruction is "ret"

**Path :** `Pintool/anchorfinder.cpp`

**Command line arguments ：**

* \-s      The C source code of the sample
* \-i       A text file whose content is an anchor instruction, like `cmpxchg eax, eax`

**Instruction format ：**

```
program name:instruction;if retanchor;number of occurences;total number of instructions
```

### Other arguments

These arguments are predefined in the above pintools, you can adjust to the situation.

* `ALLINS_MAX` - The upper limit of the number of recorded instructions in a trace file. Just adjust to your storage size. Default value: 70000000.
* `THRESHOLD` - The lower limit number of recorded instructions between anchor instructions. The obfuscator may produce bad samples, which would crash at runtime and result in a few instructions between anchor instructions. This argument is to exclude these samples.
