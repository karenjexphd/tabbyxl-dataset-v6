# TabbyXL: Dataset for the Performance Evaluation of a Software Platform for Rule-Based Spreadsheet Data Extraction and Transformation

This dataset is designed to evaluate `TabbyXL, version 1.1.0`, a software platform for the rule-based transformation of spreadsheet data from arbitrary to relational tables, that is freely available at [GitHub](https://github.com/tabbydoc/tabbyxl/releases/tag/v1.1.0). 

## Contents
* [Data description](#data-description)
    * [Files](#files)
    * [Ground-truth data](#ground-truth-data)
* [Experiment description](#experiment-description)
* [Steps to reproduce](#steps-to-reproduce)
    * [with CRL2J](#with-crl2j)
    * [with Drools-DSL](#with-drools-dsl)
    * [with Drools](#with-drools)
    * [with Jess](#with-jess)
* [Results of the Performance Evaluation](#results-of-the-performance-evaluation)
* [References](#references)

## Data Description

Our source data are based on the existing dataset of tables called `Troy_200` ([Nagy, 2016](#references)) that contains 200 arbitrary tables as CSV files collected from 10 different government statistical websites. They were collected for the experiment on data extraction from tables (see the paper [Embley, 2016](#references)). We use its earlier version that stores the original tables with style features (fonts, alignment, and indentation) as Excel spreadsheets (available at [http://tango.byu.edu/data](http://tango.byu.edu/data)).

The dataset contains the following material:
* all of `Troy_200` tables with style features put into a single spreadsheet file;
* the ground-truth data we prepared for the automatic performance evaluation of the role and structural stages of the table analysis;
* Rulesets in [CRL](https://github.com/tabbydoc/tabbyxl/wiki/CRL-Language) (see [Shigarov, 2017](#references)), [DSLR](https://docs.jboss.org/drools/release/7.7.0.Final/drools-docs/html_single/#_domain_specific_languages), [DRL](https://docs.jboss.org/drools/release/7.7.0.Final/drools-docs/html_single/index.html#_droolslanguagereferencechapter), and [CLP](https://www.jessrules.com/jess/docs/71/basics.html) formats designed for transforming `Troy_200` arbitrary tables into the relational form;
* Log files with the results of the program running and with the results of the performance evaluation of `TabbyXL`.

The dataset provides all required data to reproduce the experiment including the program running and automatic performance evaluation of `TabbyXL`, using the following options:
* `CRL2J` option: `TabbyXL` automatically generates Java source code from CRL rules with our CRL interpreter and compile it to Java bytecode, and then runs this generated program with JRE ([Java SE Runtime Environment 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)).
* `Drools-DSL` option: `TabbyXL` automatically maps CRL rules to DRL ones with the DSL specification, and runs the executing them with [Drools Expert](https://www.drools.org) rule engine.
* `Drools` option: `TabbyXL` runs the executing DRL ruleset  corresponding to our CRL ruleset with [Drools Expert](https://www.drools.org) rule engine.
* `Jess` option: `TabbyXL` runs the executing CLP ruleset corresponding to our CRL ruleset with [Jess](http://www.jessrules.com) rule engine.

### Files

The experiment dataset includes the following files

|Directory/File              | Description                                                             |
|----------------------------|-------------------------------------------------------------------------|
|`data/gt`                   | 200 ground-truth tables for automatic performance evaluation            |
|`data/tables.xlsx`          | 200 arbitrary tables in the single workbook (as a source)               |
|`result/crl2j`              | `CRL2J` option results                                                  |
|`results/crl2j/extracted`   | 200 relational tables automatically extracted from the source           |
|`results/crl2j/eval.log`    | Log file with results of the performance evaluation                     |
|`results/crl2j/results.log` | Log file with results of the program running                            |
|`result/crl2j/rules.crl`    | CRL ruleset                                                             |
|`result/drools-dsl`         | `Drools-DSL` option results                                             |
|`result/drools-dsl/config/crl.dsl`| DSL specification for CRL-to-DRL mapping                         |
|`result/drools-dsl/config/drools-dsl.properties`| Configuration file for using `Drools Expert` rule engine|
|`results/drools-dsl/extracted`   | 200 relational tables automatically extracted from the source  |
|`results/drools-dsl/eval.log`    | Log file with results of the performance evaluation            |
|`results/drools-dsl/results.log` | Log file with results of the program running                   |
|`result/drools-dsl/rules.dslr`   | CRL ruleset                                                    |
|`result/drools`              | `Drools` option results                                            |
|`result/drools/config/drools.properties`| Configuration file for using `Drools Expert` rule engine|
|`results/drools/extracted`   | 200 relational tables automatically extracted from the source      |
|`results/drools/eval.log`    | Log file with results of the performance evaluation                |
|`results/drools/results.log` | Log file with results of the program running                       |
|`result/drools/rules.drl`    | DRL ruleset                                                        |
|`result/jess`                | `Jess` option results                                              |
|`result/jess/config/jess.properties`| Configuration file for using `Jess` rule engine             |
|`results/jess/extracted`   | 200 relational tables automatically extracted from the source        |
|`results/jess/eval.log`    | Log file with results of the performance evaluation                  |
|`results/jess/results.log` | Log file with results of the program running                         |
|`result/jess/rules.clp`    | CLP (CLIPS) ruleset                                                  |
|`README.md`                | this file                                                            |

### Ground-Truth Data

The collected ground-truth data covers both the role and structural analysis. Their form is designed for human readability. Moreover, they are independent of the presence of critical cells in tables.

Each ground-truth (in the folder `data/gt`) table is accompanied with two recordsets: `ENTRIES` and `LABELS`. The first of them specifies entries. Each record presents an entry as a triple `<value, provenance, set of associated labels>`. For example, a fragment of `ENTRIES` recordset is shown below

|`ENTRY`|`PROVENANCE`|`LABELS`                      |
|-------|------------|------------------------------|
|`243`  |`T11`       |`"2002 [B11]", "balance [T4]"`|
|`2871` |`S11`       |`"2002 [B11]", "imports [S4]"`|

In `LABELS` recordset each record presents a label as a triple `<value, provenance, parent reference`> as demonstrated below

|`LABEL`  |`PROVENANCE`|`PARENT`              |
|---------|------------|----------------------|
|`balance`|`T4`        |`other ict goods [R3]`|
|`imports`|`S4`        |`other ict goods [R3]`|

### Ruleset

We designed the ruleset to transform the tables of the dataset into the relational form. It consists of 16 following rules:
* `Rule-1` replaces each cell value matching `\#` or `s` on `0`.
* `Rule-2` replaces each cell value such as `F`, `x`, `NA`, horizontal ellipsis, or dash on `null`. 
* `Rule-3` replaces dashes or hyphens at the beginning of a negative number on the regular minus symbol.
* `Rule-4` generates labels from non-empty cells of the topmost row. 
* `Rule-5` searches for head rows top down beginning from the topmost one, examining pairs of neighbor rows. It expects that column labels in the pair form a hierarchy. If a non-empty cell `c` located on `i`-row in the top-right region spans several columns and non-empty cells `c1,...,cn` are located in these nested columns on `i+1`-row, then the cell `c` contains a parent label for labels produced from the cells `c1,...,cn`.
* `Rule-6` creates labels from non-empty cells in the leftmost column. 
* `Rule-7` continues to generate labels from cells located below the head in the case when their values are not numbers.
* `Rule-8` generates entries from numeric values of the remaining cells located below the head.
* `Rule-9` we use to fix indentation. It sets up the indent for each cell when its string value begins from a hyphen-minus character. The next two rules recover a hierarchy of row labels located in the leftmost column. 
* `Rule-10` searches for parent-child pairs among row labels assuming that each new level of a hierarchy appends one additional indent (two spaces) in cells of nested labels.
* `Rule-11` associates a child label with parent one when the parent cell with a boldface text is located above the child cell with regular text. Exceptions are made for third cases where a parent candidate is `Total`, `All`, or `I alt`. They are ignored.
* The three rules: `Rule-12`, `Rule-13` and `Rule-14`, place labels into synthetic categories: `ColCat`, `RowCat1,...,RowCatN`.
* `Rule-15` associates an entry with a terminal label when they are originated from cells located in the same column.
* `Rule-16` connects an entry and all labels when their cells are in the same row.

## Experiment Description

We put all of Troy_200 tables with style features into the single spreadsheet file (`data/tables.xlsx`). Each of 200 tables is located in a separate sheet. The pair of tags `$START` and `$END` points out to its location inside the sheet. 

The [ruleset](#ruleset) was implemented in 4 formats:
* CRL (see `result/crl2j/rules.crl`) 
* DSLR (CRL as DSL-dialect for Drools) (see `result/drools-dsl/rules.dslr`)
* DRL (see `result/drools/rules.drl`)
* CLP (`result/jess/rules.clp`).

We transformed automatically all tables of the single spreadsheet into the relational form, using `TabbyXL` with the listed options: 
* `CRL2J` _option_: we automatically generated Java program from CRL ruleset, using use CRL-to-Java translation, and ran it with JRE ([Java SE Runtime Environment 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)). See the folder `results/crl2j`.
* `Drools-DSL` _option_: we executed the ruleset presented in CRL-dialect and automatically mapped to DRL with [`Drools Expert`](https://www.drools.org) rule engine and our DSL-specification of CRL-dialect. See the folder `results/drools-dsl`.
* `Drools` _option_: we executed our ruleset in DRL format with [`Drools Expert`](https://www.drools.org) rule engine. See the folder `results/drools`.
* `Jess` _option_: we executed the ruleset in CLP (CLIPS) format with [`Jess`](http://www.jessrules.com) rule engine. See the folder `results/jess`.

The folder `results/crl2j/extracted` | `results/drools-dsl/extracted` | `results/drools/extracted`| `results/jess/extracted` contains the relational tables extracted automatically by `CRL2J` | `Drools-DSL` | `Drools` |`Jess` option respectively. Similarly to the ground-truth data, each extracted table (in folder `results/../extracted`) is accompanied with two recordsets: `ENTRIES` and `LABELS`. This provides the matching the results against the ground-truth data to evaluate the performance automatically.

We also stored the log files with the results of the program running of `TabbyXL` (`results/../results.log`).

We evaluated the performance of `TabbyXL` for all options, using standard metrics: `recall`, `precision`, and `F-measure`. We adopted them as follows. When R is a set of instances in the result table and S is a set of instances in the corresponding source table, then: `recall` = R&cap;S/S and `precision` = R&cap;S/R. An instance refers to an entry, label, entry-label pair, or label-label pair. These metrics were separately calculated for each type of instances (entries, labels, entry-label pairs, and label-label pairs). 

The results of the performance evaluation of all options are stored in the log files (`results/../eval.log`).

The performance evaluation confirms the applicability of the implemented rulesets to process a bunch of different arbitrary tables of the same genre (government statistical websites). The experiment demonstrates that `TabbyXL` can be used for developing programs for the transformation of spreadsheet data into the relational form.

## Steps to Reproduce

**Step 1.** Download and install [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads) or more.

*Note that `TabbyXL` uses Java compiler included in JDK to compile Java code automatically generated from CRL rules with `CRL2J` option. This requires to use JDK, not JRE. However, JRE is enough for reproducing this experiment with `Drools-DSL`,  `Drools`, or `Jess` option. In that case, you can use [Java SE Runtime Environment 8](http://www.oracle.com/technetwork/java/javase/downloads) or more.*

**Step 2.** Download and unpack zip archive that contains the experiment data files into your directory. 

*Skip this step if you read README.md from unpacked the zip archive with the dataset.*

**Step 3.** Download and unpack [`tabbyxl-1.1.0.zip`](https://github.com/tabbydoc/tabbyxl/releases/tag/v1.1.0) into this directory.

*Note that you can also build `TabbyXL-1.1.0-jar-with-dependencies.jar` (the required executable JAR file with dependencies) from [the source code](https://github.com/tabbydoc/tabbyxl/releases/tag/v1.1.0) ([read the instruction on building with Apache Maven](https://github.com/tabbydoc/tabbyxl/blob/master/README.md#building-with-apache-maven)).*



**Step 4.** Check your directory. If you see the following structure then it is okay:
```
+-- data/
¦   +-- tables.xlsx
¦   L-- gt/
+-- results/
¦   +-- crl2j/
¦   ¦   +-- extracted/
¦   ¦   +-- eval.log
¦   ¦   +-- results.log
¦   ¦   L-- rules.crl
¦   +-- drools-dsl/
¦   ¦   +-- config/
¦   ¦   ¦   +-- crl.dsl
¦   ¦   ¦   L-- drools-dsl.properties
¦   ¦   +-- extracted/
¦   ¦   +-- eval.log
¦   ¦   +-- results.log
¦   ¦   L-- rules.dslr
¦   +-- drools/
¦   ¦   +-- config/
¦   ¦   ¦   L-- drools.properties
¦   ¦   +-- extracted/
¦   ¦   +-- eval.log
¦   ¦   +-- results.log
¦   ¦   L-- rules.drl
¦   L-- jess/
¦       +-- config/
¦       ¦   +-- lib/
¦       ¦   ¦   L-- [jess.jar]
¦       ¦   L-- jess.properties
¦       +-- extracted/
¦       +-- eval.log
¦       +-- results.log
¦       L-- rules.clp
+-- README.md
L-- TabbyXL-1.1.0-jar-with-dependencies.jar
```

**Step 5.** Change to your directory that contains the unpacked data and `TabbyXL`

```bash
cd <path to your directory>
```
**Step 6.** Run `TabbyXL` in your console as explained below.

### with CRL2J

**Step 6.1.1.** In order to obtain results, run the executable JAR with the following command:

```bash
java -jar TabbyXL-1.1.0-jar-with-dependencies.jar -input data/tables.xlsx -ruleset results/crl2j/rules.crl -ignoreSuperscript true -useCellText false -debuggingMode false -output results/crl2j/extracted -useShortNames true
```

*Note that, with this option, you should run `java` included in JDK, not JRE. Otherwise, you get the following message:*

```java
Exception in thread "main" java.lang.IllegalStateException
        at ru.icc.td.tabbyxl.crl2j.compiler.CharSequenceCompiler.<init>(CharSequenceCompiler.java:44)
        at ru.icc.td.tabbyxl.crl2j.RuleCodeGen.compileAllRules(RuleCodeGen.java:38)
        at ru.icc.td.tabbyxl.TabbyXL.loadCRL2J(TabbyXL.java:638)
        at ru.icc.td.tabbyxl.TabbyXL.runRulesetWithCRL2J(TabbyXL.java:648)
        at ru.icc.td.tabbyxl.TabbyXL.main(TabbyXL.java:487)
```


*If you see this message, then, please, check that you run `java` included in JDK. You also can try to use `"%JAVA_HOME%/bin/java"` instead of `java` as follows.*

```bash
"%JAVA_HOME%/bin/java" -jar TabbyXL-1.1.0-jar-with-dependencies.jar -input data/tables.xlsx -ruleset results/crl2j/rules.crl -ignoreSuperscript true -useCellText false -debuggingMode false -output results/crl2j/extracted -useShortNames true
```

See the extracted data in the output directory `results/crl2j/extracted`.

**Step 6.1.2.** In order to evaluate the obtained results, run the executable JAR with the following command:

```bash
java -cp TabbyXL-1.1.0-jar-with-dependencies.jar ru.icc.td.tabbyxl.evaluation.Evaluator results/crl2j/extracted data/gt
```

See the results of the performance evaluation in the console output.

### with Drools-DSL

**Step 6.2.1.** In order to obtain results, run the executable JAR with the following command:

```bash
java -jar TabbyXL-1.1.0-jar-with-dependencies.jar -input data/tables.xlsx -ruleset results/drools-dsl/rules.dslr -ignoreSuperscript true -useCellText false -debuggingMode false -output results/drools-dsl/extracted -useShortNames true -ruleEngineConfig results/drools-dsl/config/drools-dsl.properties
```

*Note that the configuration file `results/drools-dsl/config/drools-dsl.properties` contains the reference to the DSL-specification `results/drools-dsl/config/crl.dsl` in `DSL` attribute as shown below:*

```
RULE_SERVICE_PROVIDER = http://drools.org/
RULE_SERVICE_PROVIDER_IMPL = org.drools.jsr94.rules.RuleServiceProviderImpl
SOURCE = dslr
DSL = results/drools-dsl/config/crl.dsl
```
*If you change the current directory for running the program then you should also put the correct reference to the DSL-specification in `DSL` attribute.*

See the extracted data in the output directory `results/drools-dsl/extracted`.

**Step 6.2.2.** In order to evaluate the obtained results, run the executable JAR with the following command:

```bash
java -cp TabbyXL-1.1.0-jar-with-dependencies.jar ru.icc.td.tabbyxl.evaluation.Evaluator results/drools-dsl/extracted data/gt
```

See the results of the performance evaluation in the console output.

### with Drools

**Step 6.3.1.** In order to obtain results, run the executable JAR with the following command:

```bash
java -jar TabbyXL-1.1.0-jar-with-dependencies.jar -input data/tables.xlsx -ruleset results/drools/rules.drl -ignoreSuperscript true -useCellText false -debuggingMode false -output results/drools/extracted -useShortNames true -ruleEngineConfig results/drools/config/drools.properties
```

See the extracted data in the output directory `results/drools/extracted`.

**Step 6.3.2.** In order to evaluate the obtained results, run the executable JAR with the following command:

```bash
java -cp TabbyXL-1.1.0-jar-with-dependencies.jar ru.icc.td.tabbyxl.evaluation.Evaluator results/drools/extracted data/gt
```

See the results of the performance evaluation in the console output.

### with Jess

**Step 6.4.1.** Install [Jess](http://www.jessrules.com) firstly. Download it from [the official site](http://www.jessrules.com/jess/download.shtml). Unpack the library file `jess.jar` from the downloaded archive (`Jess71p2.zip`) and place it into the directory `results/jess/config/lib`.

**Step 6.4.2.** In order to obtain results, run the executable JAR with the following command on Windows:

```bash
java -cp results/jess/config/lib/jess.jar;TabbyXL-1.1.0-jar-with-dependencies.jar ru.icc.td.tabbyxl.TabbyXL -input data/tables.xlsx -ruleset results/jess/rules.clp -ignoreSuperscript true -useCellText false -debuggingMode false -output results/jess/extracted -useShortNames true -ruleEngineConfig results/jess/config/jess.properties
```

or run the executable JAR with the following command on Linux:

```bash
java -cp results/jess/config/lib/jess.jar:TabbyXL-1.1.0-jar-with-dependencies.jar ru.icc.td.tabbyxl.TabbyXL -input data/tables.xlsx -ruleset results/jess/rules.clp -ignoreSuperscript true -useCellText false -debuggingMode false -output results/jess/extracted -useShortNames true -ruleEngineConfig results/jess/config/jess.properties
```

See the extracted data in the output directory `results/jess/extracted`.

**Step 6.4.3.** In order to evaluate the obtained results, run the executable JAR with the following command:

```bash
java -cp TabbyXL-1.1.0-jar-with-dependencies.jar ru.icc.td.tabbyxl.evaluation.Evaluator results/jess/extracted data/gt
```

See the results of the performance evaluation in the console output.

### Results of the Performance Evaluation

As a result of the performance evaluation, using any option (`CRL2J`, `Drools-DSL`, `Drools`, or `Jess`), you will see the following results at the end of the output:

SUMMARY OF RESULTS:

|           |`entries`                 |`labels`               |`entry-label pairs`      |`label-label pairs`|
|-----------|--------------------------|-----------------------|-------------------------|-------------------------|
|`recall`   |`0.9813217 (16602/16918)` |`0.9965013 (4842/4859)`|`0.9772999 (34270/35066)`|`0.93888354 (1951/2078)`
|`precision`|`0.99957854 (16602/16609)`|`0.9363759 (4842/5171)`|`0.9965396 (34270/34389)`|`0.9784353 (1951/1994)`

SUMMARY OF ERRORS:

```
Tables processed with errors, total = 25
False negatives, total = 1256 in 25 tables
False positives, total = 498 in 14 tables

List of tables processed with errors:
	1	C10006.xlsx	(false negatives, total = 3;	false positives, total = 0)
	2	C10025.xlsx	(false negatives, total = 50;	false positives, total = 46)
	3	C10034.xlsx	(false negatives, total = 15;	false positives, total = 13)
	4	C10035.xlsx	(false negatives, total = 6;	false positives, total = 0)
	5	C10036.xlsx	(false negatives, total = 3;	false positives, total = 0)
	6	C10038.xlsx	(false negatives, total = 1;	false positives, total = 1)
	7	C10047.xlsx	(false negatives, total = 12;	false positives, total = 14)
	8	C10052.xlsx	(false negatives, total = 6;	false positives, total = 0)
	9	C10053.xlsx	(false negatives, total = 4;	false positives, total = 0)
	10	C10071.xlsx	(false negatives, total = 1;	false positives, total = 0)
	11	C10079.xlsx	(false negatives, total = 7;	false positives, total = 8)
	12	C10082.xlsx	(false negatives, total = 948;	false positives, total = 316)
	13	C10083.xlsx	(false negatives, total = 3;	false positives, total = 0)
	14	C10091.xlsx	(false negatives, total = 5;	false positives, total = 0)
	15	C10094.xlsx	(false negatives, total = 3;	false positives, total = 0)
	16	C10111.xlsx	(false negatives, total = 10;	false positives, total = 3)
	17	C10117.xlsx	(false negatives, total = 4;	false positives, total = 2)
	18	C10120.xlsx	(false negatives, total = 87;	false positives, total = 34)
	19	C10121.xlsx	(false negatives, total = 8;	false positives, total = 0)
	20	C10132.xlsx	(false negatives, total = 18;	false positives, total = 18)
	21	C10133.xlsx	(false negatives, total = 26;	false positives, total = 16)
	22	C10140.xlsx	(false negatives, total = 15;	false positives, total = 13)
	23	C10146.xlsx	(false negatives, total = 9;	false positives, total = 9)
	24	C10147.xlsx	(false negatives, total = 2;	false positives, total = 0)
	25	C10159.xlsx	(false negatives, total = 10;	false positives, total = 5)
```

If you see the same output with all three options then it means that this experiment has been successfully reproduced.

## References
* Nagy G. TANGO-DocLab web tables from international statistical sites, (Troy_200), 1, ID: Troy_200_1. [http://tc11.cvc.uab.es/datasets/Troy_200_1](http://tc11.cvc.uab.es/datasets/Troy_200_1)
* Embley D., Krishnamoorthy M., Nagy G., & Seth S. (2016). Converting heterogeneous statistical tables on the web to searchable databases. Int. J. on Document Analysis and Recognition, 19(2), 119-138. [https://doi.org/10.1007/s10032-016-0259-1](https://doi.org/10.1007/s10032-016-0259-1)
* Shigarov A. & Mikhailov A. (2017) Rule-Based Spreadsheet Data Transformation from Arbitrary to Relational Tables. Information Systems. Vol. 71, pp. 123-136. [https://doi.org/10.1016/j.is.2017.08.004](https://doi.org/10.1016/j.is.2017.08.004)

## Authors

* Alexey Shigarov (shigarov@icc.ru)
* Vasiliy Khristyuk (khristyuk@icc.ru)