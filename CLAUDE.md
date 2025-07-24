# User preferences

## Software tools

### Shell (mandatory)

All scripts should be runnable from the command line. The default shell of the user is `fish`, but scripts should be compatible with `bash` as well. All scripts are called from the root of the folder, and relative paths should be used to refer to files in subfolders.

### Make (mandatory)

A single Makefile in the root of the folder should be used to run all scripts. The Makefile should have targets for each major step in the workflow, such as data wrangling, analysis, and reporting.

### Bead (mandatory)

External data dependencies are managed by `bead`. The documentation for `bead` is available in the Appendix of this document. Metadata for `bead` is stored in `.bead-meta/` subfolder in the root of the folder. Attached input datasets are `input/` subfolder. If any input data is missing, `bead input load <name>` should load it. If a dataset is out of date, `bead input update <name>` should update it. The `temp/` subfolder is used for temporary files generated during the workflow. The `output/` subfolder is used for final output files, such as reports and figures.

### git (mandatory)

The folder is under version control with `git`. `.gitignore` should reflect the set of software tools used. Because `bead` is always used, `.gitignore` should always include `input/` and `temp/` folders.

### Stata (optional)

Data wrangling and statistical analysis is mostly done in Stata. All steps are in .do files, which should be callable from the command line, including from the Makefile, with `stata -b do <filename>`. Assume the availability of Stata 18.

### Julia (optional)

Modeling, simulation, larger data analysis tasks are done in Julia. All scripts should be runnable from the command line, and should be compatible with Julia 1.10 or later. The scripts should be organized in a way that allows for easy execution from the Makefile. Julia packages should be managed using `Pkg` and should be listed in a `Project.toml` file in the root of the folder.

### Python (optional)

When Julia is not suitable, Python is used for data analysis and manipulation. All scripts should be runnable from the command line, and should be compatible with Python 3.11 or later. Python packages should be managed using `uv`. User prefers raw Python (`CSV`, dictionaries) for simple data manipulation tasks. For larger tasks, `polars` is preferred. All Python scripts should be organized in a way that allows for easy execution from the Makefile.

### DuckDB (optional)

For especially large datasets, DuckDB is used for data manipulation. Assume the availability of DuckDB 1.2, used via the command line interface. DuckDB scripts should be raw .sql, organized in a way that allows for easy execution from the Makefile. The DuckDB scripts should be compatible with the command line interface and should not rely on any specific Python or Julia libraries.

### LaTeX (optional)

For reporting, LaTeX is used. Assume the availability of TeX Live 2024. `pdflatex` is preferred for compiling LaTeX documents, but for more complex documents, `lualatex` can be used. Use `bibtex` for bibliography management with the Harvard citation style.

## Folder structure

The root folder contains `README.md` and `Makefile` as well as software-specific metadata files such as `Project.toml` for Julia and `requirements.txt` for Python. Every other content lives in subfolders.

The `README.md` document should explain the sources of the data, the software dependencies, and the overall workflow. It should follow the _Social Science Data Editors Template README_ available in the Appendix of this document.

The `input/` subfolder contains all input datasets, which are managed by `bead`. The `temp/` subfolder is used for temporary files generated during the workflow, including intermediate datasets. The `output/` subfolder is used for final output files, such as reports, tables and figures.

Code lives under `code/` organized into subfolders by purpose (not by software). For example, `code/create/` for scripts that create datasets, `code/estimate/` for scripts that analyze data, and `code/plot/` for scripts that create figures. 

The `docs/` subfolder contains documentation files, such as running lab notes or any other relevant documentation. The preferred format for documentation is Markdown. The `data/external/` subfolder is used for any additional data files that are not managed by `bead`, but this should be avoided if possible. 

## File formats

When possible, plain text files are preferred over proprietary binary formats. All text files should be UTF-8 encoded. For interoperability among the various tools, CSV is preferred for tabular data, with the first row as header. Stata files should be in .dta format. For larger datasets to be processed with Julia, Python or DuckDB, Parquet is preferred. For LaTeX documents, the preferred format is .tex with UTF-8 encoding. General text notes and documentation should be in Markdown format (.md) with UTF-8 encoding. Figures should be in PDF, PNG, EPS or SVG format, with PDF preferred for vector graphics and PNG for raster graphics.

## Software architectural preferences and coding style

Software scripts should be modular and reusable. Unless it is a properly documented module or library (Julia or Python), each script should have a single responsibility and should not be too long. When there are many scripts, they should be organized in subfolders by purpose. As a general rule, no script folder should hold more than 7 scripts and more than 7 subfolders.

Data wrangling scripts should read and write data from flat files in the folder. Scripts should read one or more input files at the beginning and end with writing preferably one datafile. If they are closely related, multiple data files can be saved by a single script (such as subsamples for different years or different sectors). The user should be able to unambiguously determine for each data files which script created it. All data wrangling scripts should be runnable from the Makefile. Intermediate data files should be saved in the `temp/` subfolder, and final output files should be saved in the `output/` subfolder.

Analysis scripts should also create one or more output in flat files, such as a LaTeX table or a PNG figure. Intermediate results should be saved in the `temp/` subfolder, and final output files should be saved in the `output/` subfolder. 

Always use relative paths (either relative to the script or relative to the root of the folder). Never use absolute paths.

Generalize functionality as much as possible, but not more. For example, when looking for the largest connected component of a graph of managers and firms, write functions suitable for any bipartite graph, but do not generalize to any graph. Try to follow the SOLID principles of software design, especially the Single Responsibility Principle (SRP) and the Dependency Inversion Principle (DIP). In particular, avoid hardcoding application-specific parameters and file paths in the code. Instead, use configuration files or command line arguments to pass parameters to the scripts.

### Stata

Stata scripts should follow the _CEU MicroData Stata Style Guide_ available in the Appendix of this document.

### Julia

Julia code should be properly organized into single responsibility functions. When appropriate, define structs to encapsulate related data. Always use type annotations in function signatures to improve performance and readability. Lean into multiple dispatch to create generic functions that can handle different types of inputs while maintaining good, human readable names. For example, `solve` is a good name for a function that solves an ordinary differential equation, but also for a function that looks for the root of a nonlinear equation.

Use `Pkg` for package management and ensure that all dependencies are listed in the `Project.toml` file. Use `DataFrames` for data manipulation tasks, and `CSV` for simple tasks. Use `julia --project=. code/your_script.jl` to run Julia scripts from the command line, and ensure that all scripts are organized in a way that allows for easy execution from the Makefile.

### Python

Python scripts should follow the _PEP 8_ style guide for Python code. Use type annotations in function signatures to improve readability and maintainability. Use `uv` for package management and ensure that all dependencies are listed in the `requirements.txt` file. Use `polars` for larger data manipulation tasks, and `CSV` for simple tasks. Avoid using pandas unless absolutely necessary, as it is not preferred by the user. Use `uv run` to run Python scripts from the command line, and ensure that all scripts are organized in a way that allows for easy execution from the Makefile.

## Writing style

When writing documentation or reports, use the detail-oriented, matter-of-fact, active academic style shown in the writing samples in the Appendix of this document.

# Appendix
## bead documentation

Bead is available at https://bead.zip

> Copyright Krisztián Fekete, 2015-2020. License: Unlicense (https://unlicense.org/)

### Overview

#### The problem

We have a highly distributed analysis workflow, but:
 - can not address data precisely
 - do not have available metadata of dependencies (they are in human heads or at best in README-s)
 - do not have a global and complete overview of the workflow
 - depend on humans to reproduce / fix missing/broken scripts when needed - we do not know when it is needed


#### Vision

Tool for transparent data analysis workflow, that is highly distributed.

The tool must help in achieving product reproducibility and doing it in a distributed working style, taking away some of the problems, but restricting almost nothing.

Even partial success is better than either too much restrictions or chaos.

Highly distributed means, that developers are working individually on their part. There is no user visible global namespace, concrete package files can be renamed arbitrarily. Developers in the analysis workflow need to access only directly used data packages (they would need those anyway!).

Transparent means, that the code/data layout is preserved as it looked like at the time the package is created including data dependency information.


#### Assumptions

- data analysis: code is small relative to data
- python app is OK


#### Virtues to achieve (design and implementation guides):

- usable
- small
- simple(?)
- familiar (wherever possible)
- non-restrictive
- resilient
- distributed


#### Requirements

- conceptual simplicity
    - package centric view of workflow
    - stores provenance, code and data output
    - peer to peer, but file exchange is outside the core system (e.g. you can email or share via Dropbox/... your results)
    - tamper resistant packages (secure hash as version)
- cross-platform (Win-Mac-Lin)
    - single file executable (might be platform specific)
- support distributed team-work
- data and data-flow centric
- no restrictions other than the minimal directory structure

- usability:
    - must be easy to work with for non-programmers
    - should be hard to make mistakes
    - updates: easily create updated package versions based on new input
- reproducibility must be verifiable (-> further tooling)
- processing sensitive data should be possible
    - access control with granularity of package-uuid - restricted packages
    - packages might be owned, but ownership surely changes over time


#### Extensions

Transparency is much weaker than reproducible, but it is on that road: the package can be reproducible if there is no change to the code after the output has been generated.

The final goal of course is reproducibility: collecting all the packages created, we can generate the output using only the primary inputs and the metadata in the packages.

Reproducability can be achieved by tooling and feedback (by an automatism - software reproducing agent).

Possible further tooling (third party apps?):
    - export data packages leading to a certain product (= concrete data package version)
        - works with a full dependency graph
    - automatic verification agent: check if there is enough metadata and code to reproduce the data in the package
        - works with a single package and its direct dependencies
        - reports on reproducibility of data packages
        - colored output (red - not reproducible, green - OK)
    - visualize packages and their dependencies as a dependency graph of a repo

### Quickstart

- [create a new bead from existing data](#create-from-data)
- [derive a new bead from existing beads by computation](#derive-new)
- [update a bead for new input data (create a new version)](#update-with-new-data)
- [update algorithm to create a bead (create a new version)](#update-with-new-algorithm)
- [use data in bead without the tool](#oblivous-use)
- visualize data flow
- create a reproducible set of beads
- publish how-to-reproduce document


#### <a name="create-from-data"></a>Create a new bead from existing data

There are existing data that are to be converted to beads.

- `bead new BEAD-NAME`  (create workspace for a new bead)
- `cp data-files BEAD-NAME/output`  (copy data into workspace)
- `cd BEAD-NAME`
- `bead save --to REPOSITORY`  (create bead from workspace)


#### <a name="derive-new"></a>Derive a new bead from existing beads by computation

- `bead new BEAD-NAME`  (create workspace for a new bead)
- `cd BEAD-NAME`
- `bead mount BEAD1 NAME1`  (make data from `BEAD1` [version] available at `input/NAME1`)
- ...
- `bead mount BEADn NAMEn`  (make data from `BEADn` [version] available at `input/NAMEn`)
- create some program under the current directory (e.g. python or STATA scripts) that uses data from `input` and produces some data under `output`
- run the program
- `bead save --to REPOSITORY`  (create bead from workspace)


#### <a name="update-with-new-data"></a>Update a bead for new input data (create a new version)

- `bead develop BEAD-NAME`  (create workspace )
- `cd BEAD-NAME`
- `bead update NEW-INPUT-NAME` (update data at `input/NEW-INPUT-NAME` from the newest version of the mounted bead)
- run the program
- `bead save`


#### <a name="update-with-new-algorithm"></a>Update algorithm to create a bead (create a new version)

- `bead develop BEAD-NAME`  (create workspace )
- `cd BEAD-NAME`
- modify code
- run the program
- `bead save`

#### <a name="oblivous-use"></a>Use data in bead without the tool

A bead is a zip file, so with an unarchiver the data can be extracted and used.

Although it is easy to get access to data, it should be last resort - if you process the data in this way the references to inputs can not be tracked for you.

### Use cases

### Create a new bead

Initial setup:
```shell
$ mkdir /somepath/BeadBox
$ bead box add main /somepath/BeadBox
Will remember box main
```
This is where completed beads will be stored. Create an empty bead with name `name`:

    /somepath$ bead new name
    Created name
    
Add some data to the output of this new bead which we can use later. This bead has no computation, only data.

    /somepath$ cd name/
    /somepath/name$ echo World > output/name
    
    /somepath/name$ bead save
    Successfully stored bead.
    
    /somepath/name$ bead zap
    Deleted workspace /somepath/name

### Working with inputs in a new bead 

Create a new data package:

    /somepath$ bead new hello
    Created hello

    /somepath$ cd hello/

Add data from an existing bead at `input/<input-name>/`:

    /somepath/hello$ bead input add name who-do-i-greet
    name loaded on who-do-i-greet.

Create a program `greet` that produces a greeting, using `input/who-do-i-greet` as an input:

    read name < input/who-do-i-greet/name
    echo "Hello $name!" > output/greeting

Run the program:

    /somepath/hello$ bash greet 

This script has create a text file in `output/greeting`. Let us verify its content:

    /somepath/hello$ cat output/greeting
    Hello World!

### Package the data and send it to an outside collaborator

Save our new bead:

    /somepath/hello$ bead save
    Successfully stored bead.

This stores output, computation and references to inputs. Now the content of `/somepath/BeadBox` is

    /somepath$ ls -1 BeadBox/
    hello_20160527T130218513418+0200.zip
    name_20160527T113419427017+0200.zip

These are regular (and, in this case, small) zip files, which can be transferred by usual means (e.g. emailed) to collaborators. The recipient can process them via the `bead` tool, keep the integrity of provenance information, and adding further dependencies as needed. Even without the tool, she can access the data by directly unzipping the file and inspecting its content.

The output of the computation is stored under `data/*`. An outside collaborator without access to `bead` can just ignore the computation and all other metadata.

    /somepath$ unzip -p BeadBox/hello_20160527T130218513418+0200.zip data/greeting
    Hello World!
    
    /somepath$ unzip -v BeadBox/hello_20160527T130218513418+0200.zip 
    Archive:  BeadBox/hello_20160527T130218513418+0200.zip
		
		This file is a BEAD zip archive.
		
		It is a normal zip file that stores a discrete computation of the form
		
		output = code(*inputs)
		
		The archive contains

		- inputs as part of metadata file: references (content_id) to other BEADs
		- code as files
        - output as files
        - extra metadata to support
        - linking different versions of the same computation
        - determining the newest version
        - reproducing multi-BEAD computation sequences built by a distributed team

        There {is,will be,was} more info about BEADs at
        - https://unknot.io
        - https://github.com/ceumicrodata/bead
        - https://github.com/e3krisztian/bead

        ----

        Length    Method    Size  Cmpr    Date    Time   CRC-32   Name
        --------  ------  ------- ---- ---------- ----- --------  ----
        13        Defl:N       15 -15% 2016-05-27 13:01 7d14dddd  data/greeting
        66        Defl:N       58  12% 2016-05-27 13:01 753b9d15  code/greet
        742       Defl:N      378  49% 2016-05-27 13:02 a4eb5de9  meta/bead
        456       Defl:N      281  38% 2016-05-27 13:02 9a206f53  meta/manifest
        --------          -------  ---                            -------
        1277                  732  43%                            4 files

The following graph summarizes the internal structure of a workspace and the logical links to other beads.
![Internals](./internals.png)

## CEU MicroData Stata Style Guide

Koren, M. (2021). CEU MicroData Stata Style Guide (1.0). Zenodo. https://doi.org/10.5281/zenodo.5383489

Please cite this style guide when you use it in your work. License: Creative Commons Attribution 4.0 International (CC BY 4.0).

> ### Overview
>
> Questions
>
> * How to name variables?
> * What code style do we use?
>
> Objectives
>
> * Use verbose, helpful variable names.
> * Make your code accessible to others.

## Files

#### Use forward slash in path names

Write `save "data/worker.dta"`, not ~~`save "data\worker.dta"`~~. The former works on all three major platforms, the latter only on Windows.

#### Write out file extensions

Write `save "data/worker.dta"` and `do "regression.do"`, not ~~`save "data/worker"`~~ or ~~`do "regression"`~~. Even though some extensions are appended by Stata by default, it is better to be explicit to help future readers of your code.

#### Put file paths in quotes

Write `save "data/worker.dta"` and `do "regression.do"`, not ~~`save data/worker.dta`~~ or ~~`do regression`~~. Both are correct, but the first is more readable, as most editors readily highlight strings as separate from programming statements.

#### Use relative path whenever possible

Write `save "../data/worker.dta"`, not ~~`save "/Users/koren/Tresorit/research/data/worker.dta"`~~. Nobody else will have the same absolute path as you have on your system. Adopt a convention of where you are running scripts from and make paths relative to that location.

## Naming

#### Do not abbreviate commands

Use `generate ln_wage = ln(wage)` and `summarize ln_wage, detail`, not ~~`g ln_wage = ln(wage)`~~ or ~~`su ln_wage, d`~~. Both will work, because Stata allows you abbreviation, but the former is more readable.

#### Do not abbreviate variable names

Use `summarize ln_wage, detail`, not ~~`summarize ln_w, detail`~~. Both will work, because Stata allows you abbreviation, but the latter is very error prone. In fact, you can turn off variable name abbreviation with `set varabbrev off, permanent`.

#### Use verbose names to the extent possible

Use `egen mean_male_wage = mean(wage) if gender == "male"` , not ~~`egen w1 = mean(wage) if gender == "male"`~~. Your variables should be self documenting. Reserve variable labeling to even more verbose explanations, including units: `label variable mean_male_wage "Average wage of male workers (2011 HUF)"`.

#### Separate name components with underscore

Use `egen mean_male_wage = mean(wage) if gender == "male"` , not ~~`egen meanmalewage = mean(wage) if gender == "male"`~~ or ~~`egen meanMaleWage = mean(wage) if gender == "male"`~~. The former is more readable. Transformations like mean, log should be part of the variable name.

#### Do not put units and time in the variable name

Use `revenue` , not ~~`revenue_USD`~~ or ~~`revenue_2017`~~. Record this information in variable labels, though. You _will_ change your code and your data and you don't want this detail to ruin your entire code.

#### It is ok to use short macro names in short code

If you have a `foreach` loop with a few lines of code, it is fine to use a one-character variable name for indexing: `foreach X of variable wage mean_male_wage `. But if you have longer code and `X` would pop up multiple times, give it a more verbose name.

#### It is ok to use obvious abbreviation in variable names

If you are hard pressed against the 32-character limit of variable name length, use abbreviation that will be obvious to everyone seeing the code. Use `generate num_customer`, not ~~`generate number_of_customers_of_the_firm`~~ or ~~`generate n_cust`~~.

## White space

#### Include a space around all binary operators

Use `generate ln_wage = ln(wage)` and `count if gender == "male"`, not ~~`generate ln_wage=ln(wage)`~~ or ~~`count if gender=="male"`~~. The former is more readable.

#### Include a space after commas in function calls

Use `assert inlist(gender, "male", "female")` not ~~`assert inlist(gender,"male","female")`~~. The former is more readable.

#### Indent code that belongs together

```
foreach X of variable wage mean_male_wage {
   summarize `X', detail    
   scalar `X'_median = r(p50)
}
```

not

```
foreach X of variable wage mean_male_wage {
summarize `X', detail    
scalar `X'_median = r(p50)
}
```

#### Each .do file should be shorter than 120 lines

Longer scripts are much more difficult to read and understand by others. If your script is longer, break it up into smaller components by creating several .do files and calling them.

## Writing sample

> This section copyright Halpern, Koren and Szeidl, 2015. Please cite as
> 
> Halpern, László, Miklós Koren and Adam Szeidl. 2015. "Imported Inputs and Productivity" American Economic Review. 105(12), pp. 3660-3703.

Understanding the link between international trade and aggregate productivity is one of the major challenges in international economics. To learn more about this link at the microeconomic level, a recent literature explores the effect of imported inputs—which constitute the majority of world trade—on firm productivity. Studies show that improved access to foreign inputs has increased firm productivity in several countries, including Indonesia (Amiti and Konings 2007), Chile (Kasahara and Rodrigue 2008) and India (Topalova and Khandelwal 2011).1 A next step in this research agenda is to investigate the underlying mechanism through which imports increase productivity. As Hallak and Levinsohn (2008) emphasize, understanding which firms gain most, through what channel, and how the effect depends on the economic environment, are important for evaluating the welfare and redistributive implications of trade policies.

To explore these questions, we estimate a structural model of importer firms in Hungarian firm-level data, and conduct counterfactual policy analysis in our estimated economy. Our starting point is a dataset that contains detailed information on imported goods for essentially all Hungarian manufacturing firms during 1992-2003. Motivated by stylized facts in these data, we formulate a model of firms who use differentiated inputs to produce a final good. Firms must pay a fixed cost each period for each variety they choose to import. Imported inputs affect firm productivity through two distinct channels: as in quality-ladder models they may have a higher price-adjusted quality, and as in product-variety models they imperfectly substitute domestic inputs.2 Because of these forces, firm productivity increases in the number of varieties imported. Our model also permits rich heterogeneity across products and firms.

In the first half of the paper we estimate this model in micro data. In doing so, we face the key empirical challenge that imports are chosen endogenously by the firm. We deal with this identification problem using a structural approach which exploits the product-level nature of the data. Our model implies a firm-level production function in which output depends on capital, labor, materials, and a term related to the number of imported varieties. To estimate this production function, we follow Olley and Pakes (1996) in nonparametrically controlling for firm investment and other state variables, which pick up the unobserved component of productivity. We also build on the approach of De Loecker (2011) to control for demand effects, and follow Gandhi, Navarro and Rivers (2013) in estimating the materials coefficient from input demand. Given these controls, the import effect is identified from residual variation in the number of imported varieties. Intuitively, we estimate the difference in output between two firms that have the same productivity and face the same level of demand, but differ in the number of varieties they choose to import, which, according to our model, happens because they face a different fixed cost of importing.

Our results show that the productivity gains from imported inputs are substantial. In the baseline specification, increasing the fraction of tradeable goods imported by a firm from zero to 100 percent would increase revenue productivity by 22 percent and quantity productivity by 24 percent. We continue to estimate large productivity gains from importing when—as in models in which the cost of importing is sunk, rather than fixed—measures of the firm’s past importing behavior are included as state variables. These results suggest that imported inputs play a significant role in shaping firm performance in the Hungarian economy.

We then turn to decompose the import effect into the quality and imperfect substitution channels. We first note that for a given productivity gain from importing a good, the degree of substitution governs a firm's expenditure share of foreign versus domestic purchases. For example, when foreign and domestic inputs are close to perfect substitutes, even if the productivity gain from imports is small the import share should be high.3 Based on this idea, we then infer the relative magnitude of the two channels by comparing the expenditure share of imports for firms which differ in the number of imported varieties. We find that combining imperfectly substitutable foreign and domestic varieties is responsible for about half of the productivity gain from imports. This finding parallels the evidence in Goldberg, Khandelwal, Pavcnik and Topalova (2009) that combining foreign and domestic varieties increased firms' product scope in India; and also the theoretical arguments of Hirschman (1958), Kremer (1993) and Jones (2011) that complementarities, which amplify differences in input quality, may help explain large cross-country income differences.

We next explore whether the benefits from importing differ between foreign and domestic firms. We say that a firm "has been foreign owned" if either on the current date or on some past date its majority owners were foreigners.4 Because they have know-how about foreign markets and can access cheap suppliers abroad, these firms may gain more from spending on imports. This is an important possibility because firms that had been foreign owned played a central role in Hungary: during 1992-2003, their sales share in manufacturing increased from 21 percent to 80 percent. When we re-estimate our model allowing for differences in the efficiency of import use by ownership status, we find that firms that have been foreign owned benefit about 24 percent more than purely domestic firms from each dollar they spend on imports. We also conduct an event study of ownership changes which yields suggestive evidence that part of the premium in the efficiency of import use is caused by foreign ownership. This result implies a potential complementarity between foreign presence and importing.

Our analysis also yields estimates of the product-level fixed costs of importing. We find that—as in the model of Gopinath and Neiman (2013)—these costs increase in the number of imported products, and also that the fixed cost schedule of firms that have been foreign owned is below that of domestic firms. Lower import costs are thus a second factor generating higher benefits from importing to foreign firms.

In the second half of the paper, we develop two applications to study the economic and policy implications of our estimates. We first quantify the contribution of imports to productivity growth in Hungary during 1993-2002. Our estimates imply a productivity gain of 21.1 percent in the Hungarian manufacturing sector, of which 5.9 percentage points, more than one quarter, can be attributed to import-related mechanisms. Approximately 80 percent of these import-related gains are due to the increased volume and number of imported inputs, while the other 20 percent is the result of increased foreign ownership in combination with foreign firms being better at using imports. Thus imports contributed substantially to economic growth in Hungary, and the complementarity between foreign presence and importing had a sizeable aggregate effect. These results complement the findings of Gopinath and Neiman (2013) who emphasize the role of imported inputs in driving fluctuations in aggregate productivity.

In our second application we use simulations in the estimated economy to explore the productivity implications of tariff policies. Intuitively, a tariff cut, by reducing the cost of foreign inputs, should raise both firm-level and aggregate productivity. Our main result is that the size of the aggregate productivity gain depends positively on two broad features of the environment: (1) the initial import participation of producers; (2) the degree of foreign presence. Perhaps surprisingly, higher initial import participation—either due to low tariffs or to low fixed costs—implies larger gains from a tariff cut. This is because the set of inputs whose prices are affected is larger, and hence firms save more with the tariff cut. In turn, foreign presence matters because, as we have shown, foreign-owned firms are better in using imports.

These patterns lead to complementarities between different liberalization policies. For example, our simulations show that tariff cuts increase productivity more when the fixed costs of importing—such as licensing or other non-tariff-barriers—are also reduced. Because foreign firms are more effective in using imports, a similar complementarity exists between tariff cuts and FDI liberalization. These complementarities seem broadly consistent with the liberalization experience in the early 1990s in India. Consistent with the fixed cost complementarity, tariff cuts in India, which were accompanied by dismantling substantial non-tariff barriers, lead to rapid growth in new imported varieties (Goldberg et al. 2009) and a large increase in firm productivity (Topalova and Khandelwal 2011). And consistent with the foreign ownership complementarity, these effects were stronger in industries with higher FDI liberalization (Topalova and Khandelwal 2011).

Our tariff experiment also highlights the differential implications for domestic input demand of the quality and imperfect substitution mechanisms. When the benefit of imports comes from quality differences, domestic import use—in an intermediate range—is quite sensitive to tariffs. In contrast, when the benefit from imports comes from imperfect substitution, domestic input use is a relatively flat function of tariffs. This difference is intuitive: when foreign goods are close to perfect substitutes, even a small price change can bring about large import substitution. Another force is that losses to domestic input suppliers caused by a tariff cut are partially offset by increased demand for their products due to increased productivity.5 Because our estimates assign a significant role to imperfect substitution, and because of the second force, we obtain a relatively inelastic demand curve for domestic inputs. One lesson from this analysis is that the magnitude of redistributive losses due to import substitution depend strongly on the extent of substitution and on the initial level of tariffs. More broadly, identifying the specific mechanism driving the effect of trade policies can help evaluate the impact of these policies in other dimensions.

Besides the papers cited above, we build on a growing empirical literature exploring firm behavior in international markets, reviewed in Bernard, Jensen, Redding and Schott (2007) and Bernard, Jensen, Redding and Schott (2012). Tybout (2003) summarizes earlier plant-and firm-level empirical work testing theories of international trade. Our structural approach parallels Das, Roberts and Tybout (2007) who study export subsidies, Kasahara and Lapham (2008) who investigate the link between exports and imports, and De Loecker, Goldberg, Khandelwal and Pavcnik (2014) who study the effect of trade liberalization on markups. Our basic theoretical framework also builds on work by Ethier (1979) and Markusen (1989) who develop models connecting imported inputs and productivity.

The rest of this paper is organized as follows. Section 2 describes our data and documents stylized facts about importers in Hungary. Building on these facts, in Section 3 we develop a simple model of importer-producers. Section 4 describes the estimation procedure and Section 5 describes the results. In Section 6 we use the estimates to conduct counterfactual analysis. We discuss some caveats with our approach in the concluding Section 7.

2 Data

2.1 Data and sample definition

Main data sources. Our panel of essentially all Hungarian manufacturing firms during 1992-2003 is created by merging balance sheet data and trade data for these firms. Firms’ balance sheets and profit and loss statements come from the Hungarian Tax Authority for 1992-1999, and from the Hungarian Statistical O ce for 2000-2003. The data for 1992-1999 contain all firms which are required to file a balance sheet with the tax authority, i.e., all but the smallest companies, with the main omitted category firms being individual entrepreneurs without employees. The data for 2000-2003 include all firms with at least 20 employees and a random sample of firms with 5-20 employees. We thus lose some firms in 2000. These firms, however, constitute a relatively small share of output: during 1992-1999, firms with no more than 20 workers were responsible for less than 7.5 percent of total sales. We classify a firm to be in the manufacturing sector if it reports manufacturing as a primary activity for at least half of its lifetime in the data, and exclude all other firms.

Data on firms' annual export and import value, disaggregated by products at the 6-digit Harmonized System (HS) level, come from the Hungarian Customs Statistics. Because the 6-digit classification is noisy, we aggregate the data to the 4-digit level. In the rest of the paper we use the terms "product" and "good" to refer to a HS4 category.6 Because we are interested in the effect of imported inputs, we use data on those imported products which are classified as intermediate goods, industrial supplies or capital good parts in the Broad Economic Categories classification. We merge the balance sheet and trade data using unique numerical firm identifiers.

While we have product level data on imported input purchases, a limitation is that— because balance sheets only measure total spending on intermediate goods—we do not have corresponding product-level data for domestic input purchases. We will rely on our structural model and on input-output tables to work around this data issue. A second limitation is that we do not observe firms’ import purchases from domestic wholesalers such as export-import companies. We can, however, measure the role of such indirect imports for the economy as a whole. In our data the total value of intermediate imports by wholesalers and retailers is about 2 percent of total intermediate input use by all firms in all sectors. This fact suggests that in our data the role of intermediation for inputs is relatively small, and due to lack of additional data we ignore it below.

Processing trade. An important source of measurement error in our data is that some firms engage in processing trade. In exchange for a fee, these firms import, process and re-export intermediate goods which remain the property of a foreign party throughout. Because the processing firm does not own, purchase or sell the underlying goods, processing trade is not recorded on the firm’s balance sheet. However, because these goods cross the border, processing trade is recorded in our trade data. This inconsistency creates problems: in several observations, the value of imported intermediate inputs, as measured by customs, exceeds the value of all intermediate inputs, as measured by the balance sheet. Similarly, some firms’ exports in the customs data are substantially higher than their exports in the balance sheet data.

To deal with this reporting problem, we construct a measure of each firm's processing trade. This measure is defined as the difference, when it is positive, between customs exports and balance sheet exports. We classify a firm as a "processer" in a given year if the ratio of processing trade to balance sheet sales exceeds 2.5 percent. This cutoff is approximately the median across observations in which the ratio is positive. With this definition, about 9 percent of our observations are classified as processers. To obtain measures which reflect the underlying economic activity rather than accounting rules, we then adjust, for all firms, sales and total intermediate spending from the balance sheet by adding our measure of processing trade.

Sample definitions. We create two data samples for our analysis. Our main sample is defined by excluding all firm-year observations in which the firm is classified as a processer. We also define a firm-level sample which is obtained by fully excluding firms which are processers for more than half of the years they are in our sample. The reason for the exclusions is that our adjustment for processing likely introduces considerable noise.7 The benefit of the firm-level sample is that, because it does not permit changes in the set of firms over time due to changes in processing activity, it better reflects aggregate trends in the data. Because it has more observations, unless otherwise noted we will use the main sample in our analysis. After all exclusions, 127,472 firm-year observations remain in this sample.

Variable definitions. For each firm in each year, the balance sheet data contain information on the ownership shares of domestic and foreign owners. We say that firm j in year t "has been foreign owned" if either in that year or in some prior year foreigners had majority ownership. This definition is motivated by the view that foreign ownership has lasting effects on a firm's operations. It also solves the problem that for some firms ownership data is missing in some years. Reflecting the fact that only a quarter of the 5,009 firms that have been foreign owned ever switch back to majority domestic ownership, we sometimes simply refer to a firm which has been foreign owned as "foreign."
Because firms must file balance sheets in the county in which they are headquartered, we can classify each firm in each year as being located in one of the 20 counties in Hungary (19 actual counties and the city of Budapest). The firm-level data also contain information on the firm’s industry. We work with the 2-digit International Standard Industrial Classification (ISIC, revision 3) industry definitions, and for firms that report different industries in different years, we assign the most common industry reported.
Other data sources. We obtain 2-digit industry-level input and output price indices for 1992-2003 from the Hungarian Statistical Office. We also exploit an industry-level input-output table which was constructed for the year 2000 by the Hungarian Statistical Office.
