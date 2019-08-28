# RGenIE analysis
## Introduction
This R package for GenIE analysis is designed to analyze the next-generation sequencing output from a set of GenIE experimental replicates.

GenIE (genome-editing interrogation of enhancers) is an experimental method to evaluate the effects of individual SNPs on gene transcription. It is based on targeted CRISPR-Cas9 genome editing in cultured cells to produce indels at a target locus, optionally with a homology-dependent recombination (HDR) construct to create precisely defined genomic changes. Following this, both genomic DNA (gDNA) and RNA are extracted from the same pool of cells, then cDNA is generated. Both gDNA and cDNA are amplified with locus-specific PCR primers, in multiple replicates, and libraries of the experimental replicates are prepared for next-generation sequencing. The sequencing data from each replicate for each region should be aligned to either the full reference genome or to a locus-specific reference genome.

This R package does the following:
- conducts quality control (QC) steps on the sequence data, with diagnostic output
- produces plots showing the edits generated
- compares edits in cDNA to gDNA, to identify effects on transcription rates
- reports statistical analyses 
- estimates power to detect effects at a given edited allele frequency & effect size

**Full documentation will be completed once the package is published and released.**

## Example

Command:

```Rscript genie.R --regions input_regions.tsv --replicates replicates.tsv --out myexperiment```

Output files:
- myexperiment.plots.pdf
- myexperiment.region_stats.tsv
- myexperiment.replicate_stats.tsv
- myexperiment.site_profiles.tsv
- myexperiment.uns.tsv
- myexperiment.replicate_udps.tsv
- myexperiment.merged_udps.tsv
- myexperiment.replicate_udps.tsv

## Plots output

### 1. Experiment summary

![](https://raw.githubusercontent.com/Jeremy37/RGenIE/master/docs/experiment_summary.png)

This plot shows a summary of the estimated effect sizes and editing rates for all regions input for analysis.


### 2. Statistics grep summary

![](https://raw.githubusercontent.com/Jeremy37/RGenIE/master/docs/grep_summary.png)

This plot lists statistics comparing cDNA expression with gDNA, determined using sequencing matching (“grep”) for the HDR and WT allele profiles. At the top left are shown the mean fraction of reads identified as HDR and WT for cDNA and gDNA. At the top right is the estimated cDNA:gDNA fold change in HDR:WT ratio. For each sample the HDR:WT ratio is computed, and the estimated cDNA:gDNA ratio is simply the average HDR:WT ratio in cDNA divided by that for gDNA. The statistical significance is computed with Welch’s t test, which tests for a difference in HDR:WT ratio in cDNA vs. gDNA, allowing for different variance in each group.


### 3. Statistics alignment summary

![](https://raw.githubusercontent.com/Jeremy37/RGenIE/master/docs/alignment_summary.png)



### 4. Unique Deletion Profile (UDP) summary plot

![](https://raw.githubusercontent.com/Jeremy37/RGenIE/master/docs/deletion_alleles.png)

This plot shows, in the bottom panels, every unique deletion allele in the window of interest, where all replicates of each type have been merged together, separately for gDNA and cDNA. The histogram above this shows the count of unique deletion alleles covering each nucleotide position; some of these have many reads while others have few. The top histogram shows the count of reads with a deletion at each nucleotide. In the example above, the cDNA replicates have fewer distinct UDPs than the gDNA.

The vertical green line shows the “highlight position” defined in the regions input file. This is usually a SNP targeted for replacement with HDR. The dashed vertical grey line shows the “cut site” defined in the regions input file.


### 5. Deletion profile plot

![](https://raw.githubusercontent.com/Jeremy37/RGenIE/master/docs/deletion_profile.png)

This plot shows an alternative view of the read count histogram from the UDP plot. The top plot shows the fraction of all reads with a deletion at each position across the window of interest. cDNA and gDNA are overlaid to make comparisons easier. The bottom plot shows the ratio of reads with a deletion at each position to WT reads at the same position, across the window of interest.

In this example, whereas the top plot shows a slightly greater fraction of deletion reads in cDNA, the bottom plot shows that this is a much larger upregulation relative to WT than is apparent from the top plot. This is because editing was highly efficient at this locus, and deletion alleles are highly upregulated, so that WT levels are quite low.

### 6. Alleles plot

![](https://raw.githubusercontent.com/Jeremy37/RGenIE/master/docs/alleles_plot.png)

This plot shows the UDP-normalised score (UNS) for each of the top 40 deletion alleles (arranged by read count in gDNA). This score is the ratio mentioned above:
	(counts for allele cDNA / WT cDNA) / (counts for allele gDNA / WT gDNA)

UNS is plotted on a log scale, with the confidence in the score (determined by log-scale number of gDNA counts) represented in the size of the circle next to the allele. The HDR allele is highlighted in green, and is shown regardless of whether it is in the top 40 alleles by gDNA count. A confidence interval for each allele is also shown, computed using Welch’s t test just as for the HDR allele. The maximum number of alleles shown can be set in the options. 
