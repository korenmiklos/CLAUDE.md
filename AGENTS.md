# CLAUDE.md

This file provides global user preferences and guidance to Claude Code (claude.ai/code) when working with code on this system.

## Software tools

### Shell (mandatory)

All scripts should be runnable from the command line. The default shell of the user is `fish`, but scripts should be compatible with `bash` as well. All scripts are called from the root of the folder, and relative paths should be used to refer to files in subfolders.

### Make (mandatory)

A single Makefile in the root of the folder should be used to run all scripts. The Makefile should have targets for each major step in the workflow, such as data wrangling, analysis, and reporting. 

### Bead (mandatory)

External data dependencies are managed by `bead`. Metadata is stored in `.bead-meta/` subfolder. Input datasets are in `input/` subfolder. Use `bead input load <name>` for missing data and `bead input update <name>` for updates. The `temp/` subfolder is for temporary files, `output/` for final output files.

### git (mandatory)

The folder is under version control with `git`. `.gitignore` should reflect the set of software tools used. Because `bead` is always used, `.gitignore` should always include `input/` and `temp/` folders. Avoid using `git add .` as this can include files that are not to be shared. Instead, add the files that have changed explicitly by name.

### Stata (optional)

Data wrangling and statistical analysis in Stata. Use .do files callable from command line with `stata -b do <filename>`. Assume Stata 18.

### Julia (optional)

Modeling, simulation, larger data analysis in Julia 1.10+. Scripts runnable from command line via Makefile. Use `Pkg` for package management with `Project.toml`.

### Python (optional)

Python 3.11+ for data analysis when Julia unsuitable. Use `uv` for package management. Prefer raw Python (`CSV`, dictionaries) for simple tasks, `polars` for larger tasks. Scripts runnable from Makefile.

### DuckDB (optional)

DuckDB 1.2 for large datasets via command line. Use raw .sql scripts executable from Makefile.

### LaTeX (optional)

TeX Live 2024 for reporting. Use `pdflatex` (or `lualatex` for complex docs) and `bibtex` with Harvard citation style.

## Folder structure

Root contains `README.md`, `Makefile`, and metadata files (`Project.toml`, `requirements.txt`). Other content in subfolders:
- `input/`: datasets managed by `bead`
- `temp/`: temporary files
- `output/`: final output files
- `code/`: organized by purpose (e.g., `code/create/`, `code/estimate/`, `code/plot/`)
- `docs/`: documentation in Markdown
- `data/external/`: additional data files (avoid if possible) 

The `README.md` should provide an overview of the project, including how to run the scripts, data and software dependencies, and any other relevant information. It should follow the Social Science Data Editors Template README format as described in @instructions/README.md

## File formats

Prefer plain text, UTF-8 encoded. Use CSV for tabular data, .dta for Stata, Parquet for large datasets, .tex for LaTeX, .md for documentation. Figures: PDF (vector) or PNG (raster).

## Software architectural preferences and coding style
### General guidance
The user treats "Don't Repeat Yourself" as dogma. Abstract away as much as you can. Anything that is repeated at least twice should be a variable, a function, a module, a for loop, as appropriate. Do not hardcode any parameters, no magic numbers. Follow SOLID principles: every piece of code should have a single reason for change (avoid statistics logic and graphic design in the same place) and abstract software components (like regression models) should not depend on concrete settings (like how many observations we have). 

Scripts should be modular with single responsibility. Organize by purpose in subfolders (max 7 scripts/subfolders each). Scripts read inputs at start, write outputs at end. Use relative paths only. 

All scripts runnable from Makefile with outputs to `temp/` or `output/` as appropriate.

### Make
- Use named variables to parametrize build and execution. For example, `CORES := 4`. All parameters are declared at the top of the Makefile and are all uppercase.
- Software tool calling should be parametrized for ease of change. For example, `STATA := stata -b do` and `PYTHON := uv run python`.
- Use automatic make variables like `$@` and `$<`, pattern matching, and make functions when relevant.
- Target directories should be first created with `mkdir -p $(dir $@)` in each recipe.
- Recipes should be read from bottom up. In a simple A -> B -> C workflow, recipe C should be on top, followed by B then A.

### Stata

Follow _CEU MicroData Stata Style Guide_, @instructions/STATA.md 

Beyond the CEU MicroData Stata Style Guide, follow additional conventions:

#### Script Structure
- Display versions of Stata packages used in the script, start with `which <package>`
- Use `clear all` for setup (not `set more off` or `cap log close`)

### Variable Creation
- Use `generate byte` for binary variables when memory efficient
- Use `generate str` for string variables with explicit type
- Prefer `egen` functions with `by()` option over `bysort` + `generate`
- Use descriptive temporary variable names (e.g., `fmtag`, `max_n_managers`)

### File Names and Paths
- Always use paths relative to the root of the project. Only use absolute path for URLs and if you are referring to outside the project folder.
- Put paths and filenames in quotes like "temp/sample.dta", not temp/sample.dta
- Include the extension of filenames like "output/figure/plot.pdf", not "output/figure/plot".
- When saving log files, use text format, like `log open docs/report.log, text replace`

### Data Manipulation Patterns
- Use `keep if` early to reduce dataset size
- Use `drop` immediately after variables are no longer needed
- Use `preserve`/`restore` for temporary data operations
- Use `tempfile` and backtick notation for temporary datasets
- Use `collapse` with multiple statistics: `(mean) var1 (firstnm) var2`

### Advanced Techniques
- Use `egen tag()` for creating unique identifiers across groups
- Use `reshape wide` for pivoting data with string suffixes
- Use `merge ... using ..., keep(match) nogen` for clean merging
- Use `inrange()` function instead of `>= & <=` conditions
- Use `inlist()` function instead of `== | ==` conditions
- Use `!missing()` instead of `~missing()` for clarity

### Comments and Documentation
- Use explanatory comments before complex operations. Explain the why, not the what
- Include verification steps with `count if` statements
- Add context for business logic (e.g., "switching years can be noisy")
- Use descriptive variable names that don't require comments

### Julia

Single responsibility functions with type annotations. Use structs and multiple dispatch with readable names. Use `DataFrames` for data manipulation, `CSV` for simple tasks. Run with `julia --project=. code/your_script.jl`.

### Python

Follow _PEP 8_ with type annotations. Avoid pandas. Run with `uv run`.

### Latex
- Use one line per paragraph, do not break lines otherwise.
- When inputing or including a file, use the extension like `\input{table/table1.tex}`
- Do not use `\textbf` for emphasis. Use emphasis sparingly, and then use `\emph`.
- Section headings should be at most 2 levels deep. For more structuring, use `\parapgraph` like `\paragraph{Data construction.} Text here`.
- Equations are numbered by default. You can refer to them with `\eqref`.
- Include Tables and Figures in the main text. Default float option is `[ht!]`, but you can rewrite this if you deem necessary.
- Use Table and Figure captions and labels. Follow the Chicago Manual of Style: Table caption is top, notes are at the bottom. Figure caption is bottom. Title is boldface in the caption, notes are part of the caption in regular font.

## Writing style

When writing documentation or reports, use the detail-oriented, matter-of-fact, active academic style shown in the writing sample, @instructions/WRITING.md.

## Claude-specific instructions

You are always under version control, so feel free to experiment with changes to files. When debugging, create any number of test files, but clean up after yourself and delete files that are not used in actual unit tests. Always ask the user when staging data files (.dta, .csv, .parquet and the like) to git. These can be very large or contain sensitive information.

Before executing user code, either with `make` or other method, always check your working directory. All code should be started from the root of the folder. 

Unless `make` takes very long, use it for executing user-written code. This is a way to build everything and check all steps. 

Every time the user writes `kansei`, it mease complete and polish the project to perfection: review from the issue description and comments (if relevant) that the task is complete, add all the steps to the Makefile, run the full workflow in `make`, update the text if relevant, update the README and CLAUDE.md documentation, commit with a short but meaningful message referencing the issue and push to GitHub. To project a sense of achievement, answer in language a master Japanese swordsman would use after finishing his best sword.
