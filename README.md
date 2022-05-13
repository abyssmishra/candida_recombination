# Recombination analysis

This data is accessible through a jupyter notebook: [![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/RFillinger/candida_recombination/master)

Copy markdown link to clipboard 

**ddRAD-Seq is a cheap, high-depth sequencing technique that generates genotyping data across the whole genome. The goal of this project is to quantify and map recombination events using ddRAD-Seq data, or any genomic data of progeny from two parents that can be characterized with markers** 

#### Ploidy analysis an filtration

###### For ploidy analysis: 
We used [Ymap](http://lovelace.cs.umn.edu/Ymap/) for ploidy analysis. 

###### ddRAD-Seq demultiplexing and alignment
Sequencing libraries were prepared using [ddRad-Seq](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0037135) methods and sequenced with Illumina MiSeq using 50bp single-end sequencing

A tool called `barcode_splitter` (found [here](https://pypi.org/project/barcode-splitter/))is employed for out single-end reads: 
 > barcode_splitter --bcfile barcode_file.txt lotsa_samples.fastq --idxread 1
 * `barcode_file.txt` is a tab-separated file containing the sample names and respective barcodes

|Sample Name|Sample Barcode|
|:--------:|:--------:|
| Sample_1 | TCGAT    |
| Sample_2 | TGCAT    |
| Sample_3 | CAACC    |
| Sample_4 | AAGGA    |
| Sample_5 | AGCTA    |
| Sample_6 | ACACA    |


#### Alignments and `.bam` file processing

Alignments were performed using [bowtie2](https://doi.org/10.1093/bioinformatics/bty648)
> bowtie2 -x reference_directory/ -q single_end_reads.fastq -S output_file.sam -p 8 

#### STACKS

Manual for v1 STACKS: [STACKS V1](https://catchenlab.life.illinois.edu/stacks/manual-v1/) (Data as of 6/7/21 has been analyzed and prepared using this version)

##### Processing Stacks Genotype Output

**STACKS**: 

###### pstacks: 
To generate markers for each individual progeny, use `pstacks_automator_9.py`  
> python3 pstacks_automator_9.py ../alignments/filamentation/pstacks_input.txt ../alignments/filamentation/ filamentation/
> python3 pstacks_automator_9.py ../alignments/fluconazole/pstacks_input.txt ../alignments/fluconazole/ fluconazole/

###### cstacks: 

To generate a catalog with individual parents, use this command:
> cstacks -b 2 -o filamentation -p 8 -s filamentation/529L_ddRAD-Seq_parent -s filamentation/SC5314_ddRAD-Seq_parent
> cstacks -b 2 -o fluconazole/ -p 8 -s fluconazole/P60002_ddRAD-Seq_parent -s fluconazole/SC5314_ddRAD-Seq_parent


To generate a catalog with both parents and every progeny use the `cstacks_automator_10.py` program:
> python3 cstacks_automator_10.py filamentation/pstacks_input.txt ../stacks_output/filamentation/ ../stacks_output/filamentation/ 2 
> python3 cstacks_automator_10.py fluconazole/pstacks_input.txt ../stacks_output/fluconazole/ ../stacks_output/fluconazole/ 2 

###### sstacks: 

The 1 at the end of the command indicates the first batch number which uses the parents-only catalog I made with cstacks. 
Replacing this 1 with a 2 will 
> python3 sstacks_automator_11.py ../alignments/filamentation/pstacks_input.txt filamentation/ filamentation/ 1
> python3 sstacks_automator_11.py ../alignments/fluconazole/pstacks_input.txt fluconazole/ fluconazole/ 1

Genotypes command used in **Gort**:

`genotypes -b 1 -P ./genotype_input_files/ -c -t GEN --min_hom_seqs 10 --min_het_seqs 0.4 --max_het_seqs 0.05`

`genotypes` is the *Stacks* tool being employed here. It takes p-stacks and c-stacks (two tools used in the *Stacks* pipeline) and combines them into a single file.

`-b` denotes the "batch" of files being used. In this case, the first batch is used to track all the 529L cross SC5314 files previously made in *Stacks*.

`-P` is the location of the directory containing all of your *Stacks* files you would like to input into *genotypes*.

`-o` is the format of the output file you would like to receive from *Stacks*. In this case, I want to use output data from *genotypes* in the **r/qtl** pipeline, so I've typed in `rqtl`.

`-t` --This is optional-- is the format of the genotypes that you would like to have returned to you. Here, I've input F2 meaning I wish my data to be represented as an F2 intercross file in the **r/qtl** format.

`-c` tells *genotypes* to use your input to decide how to call each marker. 

`--min_hom_seqs` is the minimum number of reads required at a stack to call a homozygous genotype. The default is 5 reads, I've required a minimum of 10 reads to call a stack for a sample. 

`--min_het_seqs` below this minor allele frequency a stack is called a homozygote, above it (but below `--max_het_seqs`) it is called unknown (default 0.05).

`--max_het_seqs` minimum frequency of minor allele to call a heterozygote (default 0.1)

##### Retrieving and formatting your data

Data output from stacks is in a `.tsv` or "tab separated value" format in long tables. The orientation of the files needs to be transposed onto its side in order to be used in the next step. There are two files that need to be transposed: a file marked `.genotypes.tsv` and a file marked `.catalog.tags.tsv`

To transpose the data, open the file with Microsoft Excel, copy all the data onto your clipboard and paste it using the transposition function. 

Once the `.catalog.tags.tsv` and `.genotypes.tsv` files have been transposed, save both of them as a `.csv` instead of a `.tsv` file to in order to use them in the `allele_switch_finder.py` program. 

##### Combining files for recombination analysis

`allele_switch_finder.py` is a python program that combines the `.catalog.tags.csv` and `.genotypes.csv` files together to get them ready for **r/qtl**.

To use `allele_switch_finder.py`, use the following command: 

`python3 ./allele_switch_finder.py ./genotypes.csv ./catalogs.tags.csv parent_string_1 parent_string_2 output_name.csv`

`python3` tells the command line that you want to access the python3 library. This is needed to run the program that is written in python3. 

`./allele_switch_finder.py` is the path to the `allele_switch_finder.py` program. This tells the command line where to find the program file in order to execute the program. The path will likely not be as simple as the current directory: `./`. Instead, you will probably need to know how to write paths into the command line in order to execute this command. 

`./genotypes.csv` is the location of your transposed genotypes file along a file path. This is so the program knows where to find the file to use it. 

`./catalogs.tags.csv` is the location of the catalogs file. 

`parent_1_string` is a series of characters exclusive to any parental samples that were processed by STACKS. This tells the program to find **any** genotyped samples in your library and combine them into one sample parent that is a consensus of all your parental samples. **It is important that no other progeny sample names contain this particular string or else they will get lumped into the consensus parental strain.**

`parent_2_string` and `parent_1_string` are the respective names on the parents

`output_name.csv` names an output for the file which can have a path added to it if you need the output written to a special location. 


#### Recombination analysis with marker data

Run this program by using the following command: 
> python3 recombination_analyzer.py SCx529L/529LxSC_4way.csv
>> Removed  1890  markers ( 73.6 % )
>> Removed using remove_markers.csv: 4
>> Markers replaced or removed using strange_markers.csv:  8475
>> Total progeny:  75
>> Total recombinations:  2625

> python3 recombination_analyzer.py SCxP60002/P60002xSC_4way.csv
>> Removed  1725  markers ( 70.0 % )
>> Removed using remove_markers.csv: 0
>> Markers replaced or removed using strange_markers.csv:  5380
>> Total progeny:  70
>> Total recombinations:  274


#### Recombination and SNP analysis less-than-diploid progeny WGS data: 

I found inconsistencies with the P60002 parental; there are far fewer SNPs in this data set (35 distinguishable SNPs) than those in our original sequencing of the 21 clinical isolates (30,000), so we are leaving these data out of the analysis. 

In `LTD_Progeny_Markers`, I convert SNP data or progeny that have some haploid chromosomes to 4way data so their recombination landscapes can be run with `recombination_analyzer.py` to see definitively whether or not there is recombination in euploid chromosomes.

Start this pipeline by running: 
> python3 ltd_wgs_converter.py WGS_outputSNPs.csv

Then run it through the pipeline: 
> python3 recombination_analyzer.py LTD_Progeny_Markers/ltd_529L_4way.csv
>> Removed  82816  markers ( 81.6 % )
>> Removed using remove_markers.csv: 174
>> Markers replaced or removed using strange_markers.csv:  211
>> Total progeny:  5
>> Total recombinations:  2287





