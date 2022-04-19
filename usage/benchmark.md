---
description: benchmark generation and contrastive evaluation with other work
---

# Benchmark

### Benchmark Generation

Call `batchForCIA.py` to parse an instruction dictionary according to the Obfuscator's type. It will apply data flow analysis to each trace to extract kernel instructions, then verify the correctness by symbolic execution based on [angr](https://github.com/angr/angr). The analysis result is a JSON file including filename, original instruction and kernel instruction list, and you can keep original trace list as needed. The benchmark is composed of these JSON files.

#### **Command line arguments：**

```
$ python batchForCIA.py -o Obfuscator -f file_dir -j json_store [-s -l]
```

* `-o`      Obfuscator Type, like VMProtect
* `-f`      Directory of trace files
* `-j`      Directory for result json files
* `-s`      Run symbolic execution
* \-`l`       Less mode : no original trace list. If you don't need a complete instruction trace in the HTML report, select this to get a much smaller size

#### Global Variables : <a href="#quan-ju-bian-liang" id="quan-ju-bian-liang"></a>

| name    | function                                                                                                                                 |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| insPath | Path for the instruction dictionary. The script will select the corresponding dictionary from the directory. Default: "./ins\_records/". |

`batchForCIA.py`will parse the instruction dictionary and call `caller.py` iterally with a trace file and its original instruction. The analysis functions are implemented in `TraceAnalysis.py`.

### Reports Generation & Contrastive Evaluation

By Calling `fileAnalysis.py`, you can generate an HTML report for every item in the benchmark to assist the analysts in the reverse-engineering work. In addition, you can evaluate the processing results of generic-deobfuscator and VMHunt with the benchmark. This script will output a spreadsheet to record contrast results.

#### **Command line arguments：**

```
$ python fileAnalysis.py -o Obfuscator -p filePath [-a -r reportPath -c -e excelName -l -m -d deobfPath -v vmhuntPath -t]
```

* `-o`      Obfuscator Type, like VMProtect
* `-p`      Directory of json files in the benchmark
* `-a`      Extract original trace list from the benchmark (not less mode), then you can get a complete HTML report with `-r`
* `-r`      Directory for HTML reports. Generate when non-empty
* `-c`      Contrastive evaluation on results of generic-deobfuscator and VMHunt
* `-e`      Filename of the spreadsheet which contains the results of the evaluation. You don't need this if there is no evaluation. Generate when non-empty
* `-l`      Output logs in console
* `-m`      Defualt mode (used in test)
* `-d`      Directory of the processing results of generic-deobfuscator. Work when non-empty and `-c` selected
* `-v`      Directory of the processing results of VMHunt. Work when non-empty and `-c` selected
* `-t`      Test if the benchmark is complete. Work alone

#### Global Variables : <a href="#quan-ju-bian-liang" id="quan-ju-bian-liang"></a>

| name    | function                                                                                                                |
| ------- | ----------------------------------------------------------------------------------------------------------------------- |
| insPath | directory of the benchmark. The script will load a corresponding dictionary with filenames. Default: "./ins\_records/". |

`fileAnalysis.py` will parse the benchmark's json files with functions provided by `ContrastForVM.py`. If you need a contrastive evaluation on VMHunt and/or generic-deobfuscator, just select `-c` and get the processing results prepared in specific formats.

