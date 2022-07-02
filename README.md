# 2022-pangenome-graphs-intro

Lecture given at the "Introduction to Pan-genomics" workshop, Lake Como School of Advanced Studies, July 4-8, 2022.

Slides: https://docs.google.com/presentation/d/1kf0DGkjrt6kCmGZ9h6-PExMjfQSALwoGVdOp2WNT9wI/edit?usp=sharing

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

    ./tools/smoothxg -g two_ecolis.gfa -o two_ecolis.smooth.gfa

Further simplify by removing bubbles:

    gfatools asm -b 1000 -u two_ecolis.gfa > two_ecolis.bu.gfa
    gfatools asm -b 1000 -u two_ecolis.smooth.gfa > two_ecolis.smooth.bu.gfa

## Minigraph

Construct by aligning o157 on the K12 reference using [minigraph](https://github.com/lh3/minigraph):

    ../tools/minigraph -cxggs -t8 ../data/k12.fasta ../data/o157.fasta > o157_on_k12.gfa

## Minimizer-space de Bruijn graphs

Construct with k=10, d=0.001:

    ../tools/rust-mdbg -k 10 -d 0.001 ../data/two_ecolis.fasta --reference --minabund 1

Compact the (minimizer-space) de Bruijn graph:

    ../tools/gfatools asm -u graph-k10-d0.001-l12.gfa > graph-k10-d0.001-l12.u.gfa

Reincorporate bases in mdBG:

    ../tools/to_basespace -g graph-k10-d0.001-l12.u.gfa -s graph-k10-d0.001-l12
