---
description: benchmark generation and contrastive evaluation with other work
---

# Benchmark

### Benchmark Generation

Call `batchForCIA.py` to parse an instruction dictionary according to the Obfuscator's type. It will apply data flow analysis to each trace to extract kernel instructions, then verify the correctness by symbolic execution based on [angr](https://github.com/angr/angr). The analysis result is a JSON file including filename, original instruction and kernel instruction list, you can keep original trace list as needed. The benchmark is composed of these JSON files.

#### **Command line arguments：**

```
$ python batchForCIA.py -o Obfuscator -f file_dir -j json_store [-s -l]
```

* `-o` Obfuscator Type, like VMProtect
* `-f` directory of trace files
* `-j` directory for result json files
* `-s` select if symbolic execution
* \-`l` less mode : no original trace list. If you don't need a complete instruction trace in the HTML report, just select this to get a much smaller size

#### Global Variables : <a href="#quan-ju-bian-liang" id="quan-ju-bian-liang"></a>

| name    | function                                                                                                                                 |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| insPath | Path for the instruction dictionary. The script will select the corresponding dictionary from the directory. Default: "./ins\_records/". |

`batchForCIA.py`will parse the instruction dictionary and call `caller.py` iterally with a trace file and its original instruction. The main analysis functions are implemented in `TraceAnalysis.py`.

### Reports Generation & Contrastive Evaluation

By Calling `fileAnalysis.py`, you can generate an HTML report for every item in the benchmark to assist the analysts in the reverse-engineering work. In addition, you can evaluate the processed results of generic-deobfuscator and VMHunt based on the benchmark. This script will output a spreadsheet to record contrast results.

#### **Command line arguments：**

```
$ python fileAnalysis.py -o Obfuscator -p filePath [-a -r reportPath -c -e excelName -l -m -d deobfPath -v vmhuntPath -t]
```

* `-o` Obfuscator Type, like VMProtect
* `-p` directory of json files in the benchmark
* `-a` select to extract original trace list from the benchmark (not less mode), then you can get a complete HTML report with `-r`
* `-r` the directory for HTML reports. Only generate when non-empty
* `-c` select to do contrastive evaluation on results of generic-deobfuscator and VMHunt
* `-e` filename of the spreadsheet, which contains the contrast results of the evaluation. You don't need this if there is no evaluation. Only generate when non-empty
* `-l` select to output logs in console
* `-m` defualt mode (used in tests)
* `-d` directory of the processed results of generic-deobfuscator. Only work if non-empty and `-c` selected
* `-v` directory of the processed results of VMHunt. Only work if non-empty and `-c` selected
* `-t` test if the benchmark is complete. Work alone apart from other functions

#### Global Variables : <a href="#quan-ju-bian-liang" id="quan-ju-bian-liang"></a>

| name    | function                                                                                                                |
| ------- | ----------------------------------------------------------------------------------------------------------------------- |
| insPath | directory of the benchmark. The script will load a corresponding dictionary with filenames. Default: "./ins\_records/". |

`fileAnalysis.py` will parse the benchmark's json files in order with functions provided by `ContrastForVM.py`. If you need a constractive evaluation on VMHunt and/or generic-deobfuscator, just select `-c` and get the processed results prepared in specific formats.

