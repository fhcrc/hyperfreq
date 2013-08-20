# hyperfreq

A Bayesian APOBEC3-induced hypermutation analysis tool implemented in Python.


## CLI usage

The following examples illustrate some of the basic usage of hyperfreq. Note that if one navigates to the root of this code base, these commands should all execute using the test data in `tests/data`.

### Analysis

To run an analysis, use the `hyperfreq analyze` command. Running `hyperfreq analyze -h` will give you a full list of options. Here are some examples to get you started and give you a rough sense of hyperfreq's capabilities.

    # Simple anlysis comparing each sequence to a consensus sequence contructed from the entire alignment
    hyperfreq analyze tests/data/alignment.fasta

    # Instead, compare each sequence to the consensus for a cluster specified in a clusters file
    hyperfreq analyze tests/data/alignment.fasta -c tests/data/clusters.csv

    # Specify the reference sequence(s) you want each sequence to be compared to
    hyperfreq analyze tests/data/alignment.fasta -r tests/data/ref_seqs.fasta

These commands will all output data to a file named `hyperfreq_analysis.call.csv`. The prefix and location of this file can be specified using the `-P` and `-o` flags.

### Multiple pattern analysis

By default, these analyses look for GG context hypermutation, suggestive of APOBEC3G activity. One can specify multiple contexts for analysis using the `-p` or `--patterns` flag(s). Pattern options include

 **pattern** | **associated with**
------------ | ------------------------------------------------------------------------
        `GG` | A3G activity
        `GA` | A3F (and other A3) activity in humans
        `GR` | combined A3G and A3F activity (as often observed in hypermutated HIV
        `GM` | rhesus macaque A3DE activity (as observed in XMRV and SFV infections) \*
        `GV` | combined rhesus A3DE and A3G activity

Note that R, M and V are IUPAC degenerate codes for A or G; A or C; and A, C or G, respectively.

When running multiple patterns, the `call.csv` file contains a column called `call_pattern` which represents the pattern in which the evidence of hypermutation appears to be strongest. Other data in the `call.csv` file will contain counts, and statistics specifically for the pattern considered the call pattern. If the `-F/--full-output` flag is specified, a separate file is output for each pattern analyzed (for example `hyperfreq_analysis.GG.csv`).

\* [Zhang et al](http://www.sciencedirect.com/science/article/pii/S0042682211004375)

   
### Splitting sequences for HM free alignments

Given an alignment, we can cut out sites/columns suspected of hypermutation by using the `split` command. Running this command requires specifying an alignment and a CSV file with a column named `column`, specifying which positions in the alignment to be cut out. In addition to the `hyperfreq_analysis.call.csv` file, `hyperfreq analyze` also produces a `hyperfreq_analysis.sites.csv` file which has such a column, as well as information regarding which sequences were hypermutated at which sequence positions.

For example,

    # for an alignment with hypermutated columns removed
    hyperfreq split alignment.fasta hypermutated_columns.csv

For more thorough usage, run `hyperfreq split -h` at the command line.


## Library usage

If you want to write your own scripts, you can do so by importing the appropraite modules

    from hyperfreq import HyperfreqAlignment
    from Bio import SeqIO

    # Create a hyperfreq alignment object
    seqs = SeqIO.parse('some_file.fasta', 'fasta')
    aln = HyperfreqAlignment(seqs)

    # Obtain an analysis generator which can be iterated over.
    analysis = aln.analyze()

    # Iterate over each sequence in the analysis, and do whatever you like!
    for seq_result in analysis:
        print seq_result['sequence'], "hm status:", seq_result['hm_pos']

It's also possible to define your own mutation patterns using the `MutPattern` and `MutPatternSet` classes. It may be possible in the future to more flexibly specify patterns more flexibly via the CLI, but for now, doing so requires using this code base as a library in writing your own scripts.


## Installing

Hyperfreq depends on `biopython` and [`betarat`](https://github.com/fhcrc/betarat) libraries. If you use pip for installation, `biopython` should be installed for you automatically. Currently, `betarat` will have to be manually installed, which can be done by following [these directions](https://github.com/fhcrc/betarat).

## Pip

If you are using pip, you can do the following from the directory where hyperfreq has been downloaded (or checked out).

    pip install

## Setuptools

If the above does not work for you, you probably do not have pip installed (or perhaps no python, in which case [see this](http://www.python.org/getit/)). You can either [install it](http://www.pip-installer.org/en/latest/installing.html), or do the following.

First [install biopython](http://biopython.org/DIST/docs/install/Installation.html).

Next, download or check out the hyperfreq code base. Using your terminal, navigate to the location in which you've checked out this code and execute

    python setup.py install

## In the future...

we may make it possible to autmoate this entire process through pip. In the mean time, let us know if you have any difficulties.

