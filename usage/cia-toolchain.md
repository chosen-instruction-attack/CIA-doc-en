---
description: Automatic program generator and anchor instruction counting
---

# CIA toolchain

### Automatic Program Generator

After filling out `config.json`, run:

```shell-session
$ python caller.py -c config/config.json
```

Then you start the process of program generation. The script `caller.py` will act according to the "actions" defined in `config.json`. The actions include:

* **`clean`** -     Clean up generated files
* **`gen`** -         Generate C source code files. Implemented by insset\_test.py
* **`make`** -       Generate 3 pairs of original executable and protected executable. Implemented by Make
* **`pin`** -&#x20;
* **`anchor_pin`** -&#x20;
* **`makelogcmp`** -&#x20;
* **`cmp`** -&#x20;
* **`test`** -&#x20;
* **`makelogcmp_test`** -&#x20;
* **`logcmp`** -&#x20;

The script `caller.py` will call `insset_test.py`, `FetchTestSet.py`, `AnchorFinder.py`, `repair_anchorfinder.py`, `LogCompare.py`automatically according to the configuration.

### Important Paths

* **`makefile_template`** - All makefile scripts, which are adapted to different obfuscators to generate corresponding programs.
* **`testset`** - Lists including instructions for test, sorted by the types. These instructions are produced by [Intel XED](https://intelxed.github.io) and used for program generation with C source code templates.
* **`vmp_template`** - C source code templates. The Markers of each Obfuscator are embedded into the specific template to generate programs according to the instruction lists in **`testset`**. The anchor instruction used in the current templates is `cmpxchg eax, eax,` you can replace it as needed.

### 0x1 insset\_test.py

Receive instruction lists then automatically generate programs' source code files into a specified path with code templates and makefile templates.

**Command line arguments：**

```
$ python insset_test.py -i testset -o output_dir -t c_template -m makefile_template [-a anchor] [-l log_path]
```

* `-i`     Path of instruction lists
* `-o`     C source code files output directory
* `-t`     C source code template (<mark style="color:red;">是目录吗</mark>)
* `-m`     Makefile template
* `-a`     Anchor instruction, default:`cmpxchg eax, eax`
* \-`l`      Location of log file. Default:`""`, print to the console

#### Global Variables :

| name          | function                                                                                                |
| ------------- | ------------------------------------------------------------------------------------------------------- |
| suffix        | Suffix of source code files. Default: ".c".                                                             |
| ins\_hex\_lst | A dictionary with some instructions which have different disassembled results in capstone and pintools. |
| except\_ins   | A dictionary with some instructions which cannot be assembled correctly by gcc.                         |
| ks            | The assembler. Default: keystone in 32-bit.                                                             |

### 0x2 FetchTestSet.py

Start the makefile script to compile the programs, then execute them with pintools to record execution traces. Possible failures include 3 conditions: gcc compilation failed, Obfuscator process failed and the pintool failed to record a log.

**Command line arguments：**

```
python FetchTestSet.py  -d dir1 dir2 ...  [-b base_dir  -a anchor_str  -l logfile]
```

* `-d`     Directory of executables for test, you can select more than one
* `-b`     Parent directory of test programs' directory. Look for in recursive
* `-a`     Anchor instruction
* `-l`     Location of log file. Default:`""`, print to the console

#### Global Variables :

| name             | function                                                                |
| ---------------- | ----------------------------------------------------------------------- |
| suffix           | Suffix of source code files. Default: ".c".                             |
| exe\_suffix      | Suffix of original executables. Default: ".exe".                        |
| log\_suffix      | Suffix of log files. Default: ".log".                                   |
| themida\_suffix  | Suffix of executables protected by Themida. Default: ".vm.exe".         |
| cv\_suffix       | Suffix of executables protected by Code Virtualizer. Default: ".cv.exe" |
| vmp\_suffix      | Suffix of executables protected by VMProtect. Default: ".vmp.exe".      |
| obsidium\_suffix | Suffix of executables protected by Obsidium. Default: ".obs.exe".       |

### 0x3 AnchorFinder.py

Analyze the logs to determine crieria if the tested instruction is an anchor instruction or not.

**Command line arguments：**

```
python AnchorFinder.py -d dir1 dir2... -m mode [-a anchor -b base_dir -l logfile]
```

* `-d`     Directory to store logs, you can select more than one
* `-m`     Anchor instruction determination mode. (3anchor/retanchor)
* `-a`     Anchor instruction. Default:`cmpxchg eax, eax`
* `-b`     Parent directory of test programs' directory
* `-l`     Location of log file. Default:`""`, print to the console
