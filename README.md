# Hyperfreq

Because, ya know, we like to get hyperfreqy...

This software is python based hypermutation analysis tool.

## CLI

Analysis
    
    # Simple anlysis comparing each sequence to a consensus sequence contructed from the entire alignment
    hyperfreq analyze alignment.fasta

    # Instead, compare each sequence to the consensus for a cluster specified in a clusters file
    hyperfreq analyze alignment.fasta --clusters clusters.csv

    # SOON! Specify the reference sequence(s) which you want each sequence to be compared to
    hyperfreq analyze alignment.fasta --reference-sequences ref_seqs.fasta

    
Splitting sequences for HM free alignments
    
    # Given a csv file with a column named `site` which specifies hypermutated columns, cut those columns out
    # for an alignment wiht hypermutated columns removed
    hyperfreq split alignment.fasta hypermutated_columns.csv --column site

## Library usage

If you want to write your own scripts, you can do so by importing the appropraite modules

import hyperfreq
from hyperfreq import HyperfreqAlignment
from Bio import SeqIO

seqs = SeqIO.parse('some_file.fasta', 'fasta')
aln = hyperfreq.HyperfreqAlignment(seqs)

aln.analyze_hypermuts()

for seq in aln:
    print seq.name, "hm status:", seq.hm_pos

print "Total positive:", [seq.hm_pos for seq in aln].sum()

