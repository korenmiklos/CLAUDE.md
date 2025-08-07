# CLAUDE.md

This file provides global user preferences and guidance to Claude Code (claude.ai/code) when working with code on this system.

## Software tools

### Shell (mandatory)

All scripts should be runnable from the command line. The default shell of the user is `fish`, but scripts should be compatible with `bash` as well. All scripts are called from the root of the folder, and relative paths should be used to refer to files in subfolders.

### Make (mandatory)

A single Makefile in the root of the folder should be used to run all scripts. The Makefile should have targets for each major step in the workflow, such as data wrangling, analysis, and reporting. Use automatic make variables like $@ and $<, pattern matching, and make functions when relevant. Target directories should be first created with `mkdir -p` in each recipe.

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

Scripts should be modular with single responsibility. Organize by purpose in subfolders (max 7 scripts/subfolders each). Scripts read inputs at start, write outputs at end. Use relative paths only. Follow SOLID principles, avoid hardcoding parameters. All scripts runnable from Makefile with outputs to `temp/` or `output/` as appropriate.

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

## Writing style

When writing documentation or reports, use the detail-oriented, matter-of-fact, active academic style shown in the writing sample, @instructions/WRITING.md.

## Claude-specific instructions

You are always under version control, so feel free to experiment with changes to files. When debugging, create any number of test files, but clean up after yourself and delete files that are not used in actual unit tests. Always ask the user when staging data files (.dta, .csv, .parquet and the like) to git. These can be very large or contain sensitive information.

Every time the user writes `kansei`, it mease complete and polish the project to perfection: run the full workflow in `make`, update the text if relevant, update the README and CLAUDE.md documentation, commit and push to GitHub. To project a sense of achievement, answer in language a master Japanese swordsman would use after finishing his best sword.
