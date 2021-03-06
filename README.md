# QHP-verif-2020
This repository contains the benchmarks and results for the paper titled _"Verification of Quantitative Hyperproperties Using Trace Enumeration Relations"_

The models and the proof scripts are implemented in [Uclid5](https://github.com/uclid-org/uclid). Please refer to [this repository](https://github.com/uclid-org/uclid) for installation instructions and setting up the environment. 

Make sure that uclid5 is installed successfully before continuing. 

To test the environment type ```uclid``` on terminal that should produce the following output:

```
$ uclid

Error: Missing argument <file> ...
uclid 0.9.5
Usage: uclid [options] <file> ...

  -m, --main <Module>      Name of the main module.
  -s, --solver <Cmd>       External SMT solver binary.
  -y, --synthesizer <Cmd>  Command line to invoke SyGuS synthesizer.
  -Y, --synthesizer-run-directory <Dir>
                           Run directory for synthesizer.
  -g, --smt-file-generation <value>
                           File prefix to generate smt files for each assertion.
  -X, --exception-stack-trace
                           Print exception stack trace.
  -f, --sygus-format       Generate the standard SyGuS format.
  -c, --sygus-type-convert
                           Enable EnumType conversion in synthesis.
  -e, --enum-to-numeric    Enable conversion from EnumType to NumericType.
  -t, --test-fixedpoint    Test fixed point
  <file> ...               List of files to analyze.
```
## 1. Overview of the benchmarks
In this work we studied 5 systems with varying complexity. 

1. [Electronic Purse](./electronic_purse) - A fixed amount is debited from an account until the balance is sufficient.
2. [Password Checker](./password_checker) - Checks the user entered password against a _secret_ password.
3. [F-Y Array Shuffle](./array_shuffle) - A variant of Fisher-Yates shuffle.
4. [ZK Hats](./hat_protocol) - Interactive zero knowledge protocol where a prover with two hats convinces a color-blind verifier that the hats are of different colors.
5. [Path ORAM](./pathORAM) - A model of path ORAM introduced by Stefanov et.al. in CCS'13.

For a detailed discussion of the benchmarks, refer to the paper. 

### Folder Organization:
Each of the 5 benchmarks reside in their respective folder, and each folder contains 3 files:

- model.ucl - Contains the model of the system.
- enumeration.ucl - Contains the enumerations and proof for the system.
- counting.* - Contains the counting of the solutions.

The model.ucl and enumeration.ucl files contains uclid5 modules as entry points for executing the files, and need to specified while executions. We discuss how to run the experiments in the next section.

- The main module name in *model.ucl* is _same as the folder name_ of the benchmark. E.g. For Path ORAM, residing in the folder named **pathoram**, the main module in model.ucl file is named - *pathoram*.
- The main module in enumeration.ucl file is named - *enumeration*.

## 2. Running experiments
As the folder structure and organization of the modules are same for all the  benchmarks, the steps presented here can be used for the execution of all the benchmarks.

To verify the models and the enumeration proofs follow the following steps. 

1. ```cd benchmark_name``` to enter the directory of the benchmark.
2. To verify the model: ```uclid -m benchmark_name model.ucl```
3. To verify the enumeration: ```uclid -m enumeration model.ucl enumeration.ucl```
4. To verify counting: ```uclid counting.ucl```


## 3. Experimental Results

We present the overview of experimental results here. We refer the reader to the text of the paper for a detailed discussion of the experimental results.

| Benchmark         | Property Proven               | Model LoC | Proof LoC | Num Annotations | Verification Time |
|-------------------|-------------------------------|-----------|-----------|-----------------|-------------------|
| Electronic Purse  | Deniability                   | 46        | 93        | 9               | 5s                |
| Password Checker  | Quantitative non-interference | 50        | 100       | 10              | 6s                |
| F-Y Array Shuffle | Quantitative information flow | 86        | 145       | 66              | 8s                |
| ZK Hats           | Soundness                     | 91        | 191       | 36              | 8s                |
| Path ORAM         | Deniability                   | 209       | 587       | 142             | 13s               |

The experiments were conducted on an Intel(R) Core(TM) i7-5500U CPU @ 2.40GHz with 4 cores and 16 GB of RAM.


### Example execution for Path ORAM.
For Path ORAM, the counting module is written in [Dafny](https://www.microsoft.com/en-us/research/project/dafny-a-language-and-program-verifier-for-functional-correctness/). Please refer to [this link](https://github.com/dafny-lang/dafny/wiki/INSTALL) for installation instructions.

 ```	
$ cd pathoram
$ ls
	counting.dfy  enumeration.ucl  model.ucl
$ uclid -m pathoram model.ucl
	Successfully parsed 2 and instantiated 1 module(s).
	
	  VERIFYING PROCEDURES
	  ====================
	
	[Verifying] Stash initialization : initialize_stash
	---------------------------------------------------
	5 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	
	[Verifying] Position Map Remap : pmap_remap
	-------------------------------------------
	3 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	
	[Verifying] Reading to Stash from ORAM : read_to_stash
	------------------------------------------------------
	20 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	
	[Verifying] Writing form Stash to ORAM : write_to_oram
	------------------------------------------------------
	12 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	
	[Verifying] Organize Stash : organize_stash
	-------------------------------------------
	22 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	
	[Verifying] ORAM Access : oram_access
	-------------------------------------
	40 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	
	==========================================
	       PROCEDURE VERIFICATION ENDED       
	==========================================
	
	36 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	Finished execution for module: pathoram.
	
$ uclid -m enumeration model.ucl enumeration.ucl
	Successfully parsed 3 and instantiated 1 module(s).
	138 assertions passed.
	0 assertions failed.
	0 assertions indeterminate.
	Finished execution for module: enumeration.
	
$ dafny counting.dfy
	Dafny program verifier version 1.9.7.30401, Copyright (c) 2003-2016, Microsoft.

	Dafny program verifier finished with 11 verified, 0 errors
	