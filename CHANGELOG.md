
# Upcoming changes:

## Strange markers and copy number variations: 

* Manually curate strange marker identities in `strange_marker_results.xlsx` (in the P6 cross) 


## Lab meeting: 

* Can we separate the influence of previous LOH on recombination? Approach: separate markers that are AA/BB (Parent 1 v. 2) v. AB/CD (Parent 1 v. 2). Is there more recombination events assc with one group versus the other based on their frequency?

* **Divide the number of recombination events per 100Kb bin by the number of markers and progeny and replot**

* Quantify transition from aa<->bb v. aa<->n/bb<->n


## Linear modelling and statistics: 

* Chi-squared comparison for finding hotspots

* The total number of recombinations in the recombination bar charts need to be relativised to the number of progeny. More progeny = more recombinations.

* Find markers with extreme biases towards one parent or another.


## General code improvement: 

* Add input capabilities for chromosome input file, It's the last piece that needs to be modularized. 


## Aesthetics:

* Change chromosome colors in the centromere recombination graphs. RGB already means something in other graphs.


## Idiot checks and miscellaneous: 

* Make sure areas of recombination in the graphs correspond to haplotype calls in the data. Start by looking in areas of the graph that have "low recombination" and then look at areas with "high recombination." They should match pretty well.

* Make a Jupyter notebook for the `.git` repository.

* Continue to fill out the `README`. I'll add more things to add here: 
	1) STACKS pipeline preparation for data:
		* pstacks - I used my `pstacks_automator_9.py` program
		* cstacks - I used my `cstacks_automator10.py` program
			* Segmentation faults in cstacks are caused by inputting empty pstacks files into the command; I just moved files that caused seg faults into a separate directory. 


## 210820 Meeting notes: 

* Non-disjunction could give rise to increased retention of non-SC5314 alleles for Chr beyond selection

---

# Completed changes: 

### 11/8/2021

* Completed manual curation of strange markers in both crosses. 

* Moved main repository out of `code.osu.edu` and into [GitHub](https://github.com/RFillinger). 

* Uploaded all data into GitHub


### 9/28/21

* Fix `allele_demystifier.py` to properly map strain `strain_name.alleles.tsv` file data to `batch_X.catalog.tags` files
	* Right now, when I try to map the data, the marker read data from the `alleles.tsv` files don't match the `.bam` files

* Manually curate strange marker identities in `strange_marker_results.xlsx` (in the 529LxSC) 

* Implemented specific marker changes in deploidy using `strange_markers.csv` after manually curating putative tandem repeats

* Made a black list for markers that are misattributed genotypes for SC5314 and 529L
	* Blacklisted SCx529L: 5163, 5755, 7662


### 9/21/21

* `allele_demystifier.py` now outputs allele information about markers related to the final catalog numbers
	* This information is stored in "all_inclusive" allele files. 


### 9/20/21

* Fixed `allele_demystifier.py` and replaced `cf_data_maker.py` with it. 
	* `allele_demystifier.py` has corrected catalog numbers that enable identification of "strange markers" (markers with suspiciously high recombination events) in recleaned data files
	* `allele_demyustifier.py` can't handle any alignments outside of those performed with *C. albicans* A21


### 9/17/21

* Began work on `allele_demystifier.py`


### 9/15/21

* Annotated `recombination_analysis()` (for the love of god)


### 9/13/21

* Change RGB in the allele colors; they're too close. (Red, dark orange, yellow/ something else)
* Add chromosome characteristics to a file
* Include schematics on how to count recombination events from the centromere
* We have to re-visualize the increase recombination from the centromere
	* not currently clear re: the comparison to LOH
* For tract bin sizes we can do an average between retained markers
	* make bins based on this and re-visualize
* Number of recombinations per progeny - need to do
* Look at MAF (minor allele frequency) across recombinant progeny - is it roughly 50:50


### 8/25/21

* Changed the `chr_lengths` data structure in `recombination_analysis.py` to match Josh's data bins. 
* Did analysis comparing linear fits to Josh's data and my new binned data. 
* Clean up the stacked bar charts showing parental contributions to progeny (sort them so they appear in the proper order). 


### 8/24/21

* Linear modelling of Josh's LOH data to ~~confirm that it indeed does obey an x^2 rule.~~ 
	* I modelled Josh's LOH recombination data from the 21 clinical isolates, but data may be drastically over represented; creating an overfit for a linear model. 


### 8/19/21

* Fixed `centromerely_math()`! Changed recombination sites from "end bin" to "start bin"


### 8/16/21

* Discovered big problems with previous attempts at counting recombinations with `recombination_analysis()` regarding fragment size and site placement. 
* Rewrote `recombination_analyzer()` using `analysis_ubermensch.csv` 4 times over previous 2 weeks. 
* Verified recombination counts by looking at `f2_recleaned` files and counting recombination events and comparing them to bins in `recom` files.
* Created bugs in even numbered chromosomes of centromere graphs (they over-extend to the right for some reason. I think it has to do with the start bin and end bin in `recom_Stacker()`)
* Made a controlled test file for the recombination algorithm. I've started one called `analysis_ubermensch.csv` for you to create 4-way data with.


### 8/3/21

* Abandoned newer version of `recombination_analysis()`. Too complex and the original ~~appears to work just fine~~. Original doesn't work.   


### 8/2/21

* Refined centromere rules in `recombination_analysis()` function. It appears to work as expected in all scenarios. It is quite complicated. I would recommend splitting chromosomes along the centromere and processing each half instead. 


### 7/30/21

* Adjusted `recombination_analysis()` function in `recombination_analyzer()` so recombinations that cross the centromere aren't counted. Still doesn't take everything into account, but is a step in the right direction. 


### 7/22/21

* Goodness-of-fit chi-squared model on recombination data. 


### 7/20/21

* Selected path for chi-squared analysis for recombination hotspot search. 


### 7/15/21

* Add track length graphing capability to `recombination_bins.R` with `track_lengths()` function.
* Add loop to `shared_geno_graphs()` in `recombiantion_bins.R` to get all the stacked bar graphs for a cross in one go (prioritize parents to the bottom; alter factor order). This still doesn't sort the graph properly, though. 
* Selected top 5 progeny most like each parent and with the most heterozygous markers from each cross. 


### 7/14/21

* Begin making a stacked bar chart of allelic proportion for each progeny in `marker_tally_ho()` in `recombination_analyzer.py` and `shared_geno_graphs()` and `recombination_bins.R`


### 7/13/21

* Troubleshot `deploidy()` with real data (test data was working fine, while real data was not)


### 7/12/21

* Added progeny with < 100,000 reads to the blacklist file
* Competed and successfully tested `deploidy()` in `recombination_analyzer.py` 
* Made `recombination_bins.R` compatable with chromosome arms lacking recombination events
* Also used `suppressWarnings()` around my graphing functions in `recombination_bins.R` which might not be the greatest idea ever, but it's so clean now!


### 7/8/21

* Wrote `deploidy()` function in `recombination_analyzer.py` to remove unwanted strains or indidivudal aneuploid chromosomes. 


### 7/2/21

* Added p-values to the graph and added code for them to be printed (though they are currently commented out). 
* Began coding function to remove unwanted strains and aneuploid chromosomes. 


### 7/1/21

* Make ab_lines to see how well the cumulative recombination events of chromosome arms fit a linear model.
* Add R^2 values to the graphs. 


### 6/30/21 

* Troubleshot `recombination_bins.R` and completed x-axis labelling. Still needs cleaning, but labelling and delineations are complete. 
* Began writing `linear_modelling()` function in `recombination_bins.R`


### 6/29/21

* Wrote a function in R to generate universal positions of chromosomes in `recombination_bins.R`


### 6/28/21

* Changed chromosome arm strings in `centromerely_math()` to default variables `left_arm` and `right_arm`. 
* Improved chromosome separators on allele tally graphs. 


### 6/25/21

* Make vertical lines in the allele tally graphs into ticks and remove the last one. 
* Improved labelling in centromere graphs


### 6/24/21

* Make graphs of cumulative recombinations around the centromeres on each chromosome in `recombination_bins.R`. 
* Take a look at recombination characteristics between the two crosses. This one really isn't *done* per se, but it's on its way. 


### 6/23/21 

* Wrote a functional `centromerely_math()` function in `recombination_analyzer.py`.


### 6/22/21

* Found centromere features in the `genes.gff` file. They will be listed as *CEN* features.
* Began writing `centromerely_math()` function in `recombination_analyzer.py`.


### 6/21/21

* Fixed issue with the first bin of chromosome 1 by using the end point of the recombinatons. This also has the benefit of ignoring the start of the first event on each chromosome. 


### 6/18/21

* Rewrite `recombination_bins.R` to handle the new output from `recombination_analysis.py`.


### 6/17/21

* Rewrote `recom_Stacker()` function. I'm still getting an absurdly high peak in the first bin. I think something's funky with how I'm binning the recombination counts. 


### 6/16/21

* Rewrote `recombination_analysis()` to be simpler, better annotated, and gather marker-tallying data as well as recombination data. 


### 6/15/21

* Debugging the recombination problems occurring in `recombination_analyzer.py` `recom_Stacker()` function and `recombination_analysis()` function.
> python3 -m pdb recombination_analyzer.py SCx529L/529L_SC5314_4way.csv


### 6/11/21

* Incorporate exclusive recombination event graphs into a bar chart 
	* This will include the data from allele frequency graphs, but only the start and ending bin will be included.
* Passed read depth information for ploidy analysis to Anna. 


### 6/10/21

* Write a script to auto mate the command: 
> bedtools coverage -hist -a ../SC5314_marker_positions.bed -b progeny.bam > progeny_histogram.tsv 
* Obtain marker-specific read depths for all markers.
* I also confirmed bedtools coverage read depth measurements for samples 529LxSC_P19_B6_196 and 529LxSC_P58_E9_154 using IGV for specific markers.**There are some markers that have 2 bp overlap, but are still counted as one, meaning that some markers across all samples will have abarrant read counts.** I don't think this is a huge concern beacause the depths will be averaged out from one to the next, but it may be a problem if it occurs frequently in any given sample. 
* Updated `CHANGEOG.md` and `README.md`


### 6/8/21

* Make a `combined_SC5314.fastq` and `combined_529L.fastq` to convert `.bam` files into `.bed` files for use with BedTools' `coverage` tool to obtain marker depths for each progeny to use with Anna's ploidy program.
* Executed commands for obtaining read counts at specific marker positions to be used for ploidy count.
* Appended these to `README.md` 


### 6/7/21

* `README.md` has been improved slightly. 
* Super SC5314 reference have been combined for creation of standard `.bed` file to be used for ploidy estimations.


### 6/4/21

* Automated sample read count acquisition with `read_counter.py` which can generate a `.csv` containing the total number of aligned reads for each sample simply by telling it a diretory to go to. This can be improved to remove samples immediately or, make sample blacklists and/or whitelists. Currently, all samples are included with their respective read counts, which will require downstream handling. 


### 6/3/21

* Change to progeny filter again: nearly every sample falls below the 100,000 read threshold. I'm also only looking at alleles files. I should look at the `.bam` files instead
	* `samtools view -c -F 260 SAMPLE.bam` is the command used to find read depth of aligned reads in a `.bam` file. 

* Improved `main()` function input handling in `recombination_analyzer.py` by separating paths and file names. `recombination_analyzer.py` and `recombination_bins.R` will now output into the directory containing the original 4-way data file. 


### 6/2/21

* Generate filter for progeny `.bam` file inclusion. 
	* This should probably be implemented through `allele_analysis.py` 
	* 100,000+ reads minimum
	* I've put this threshold into the `allele_analysis.py`, but if there are any additional thresholds, I would recommend making a dedicated program for these.
	* Samples with fewer than 100,000 reads are have their sample names added an "exclude" file that can be used to exclude sample files later in the pipeline. 


### 6/1/21 

* Added graphing fixes for `recombination_bins.R` and `recombination_analyzer.py` graphs depicting allele frequency across all progeny. 
	* Parents are still present in these graphs. 

* Incorporated color-coded lines into the allele frequency graphs