## bead documentation

Bead is available at https://bead.zip

> Copyright Krisztián Fekete, 2015-2020. License: Unlicense (https://unlicense.org/)

### Quickstart
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
