# 2022-pangenome-graphs-intro

Lecture given at the "Introduction to Pan-genomics" workshop, Lake Como School of Advanced Studies, July 4-8, 2022.

## Rationale

We'll show how some common pangenome graphs can be constructed in practice, on a simple example. 

## Data

Data is two E. coli genomes, present in the `data/` folder.

## de Bruijn graph

Create compacted dBG with k=31 using [bcalm2](https://github.com/GATB/bcalm):

    ../tools/bcalm -in ../data/two_ecolis.fasta -kmer-size 31 -abundance-min 1

Convert bcalm2's FASTA (with edge information) to GFA:

    ../tools/convertToGFA.py two_ecolis.unitigs.fa two_ecolis.unitigs.gfa 31

Simplify graph by removing small bubbles using [gfatools](https://github.com/lh3/gfatools):

    ../tools/gfatools asm -b 100 -u two_ecolis.unitigs.gfa > two_ecolis.unitigs.bu.gfa

Trying a larger k value (k=300):

    ../tools/bcalm-k320 -in ../data/two_ecolis.fasta -kmer-size 300 -abundance-min 1

## Variation graph

Create a raw pangenome graph using minimap2 + [seqwish](https://github.com/ekg/seqwish):

    minimap2 -c -X ../data/two_ecolis.fasta ../data/two_ecolis.fasta > two_ecolis.paf
    ../tools/seqwish  -s ../data/two_ecolis.fasta -p two_ecolis.paf -g two_ecolis.gfa

Simplify using [smoothxg](https://github.com/pangenome/smoothxg) (Takes a while!):

    ../tools/smoothxg -g two_ecolis.gfa -o two_ecolis.smooth.gfa
