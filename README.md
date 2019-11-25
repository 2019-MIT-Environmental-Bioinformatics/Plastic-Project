# Plastic-Project  
Bioinformatics 2019 Final Project   
Bethany Fowler and Jordan Pitt

This is the repository for Bethany and Jordan's final project. For this project, we set out to replicate the analysis in [Zettler et al. 2013](https://pubs.acs.org/doi/full/10.1021/es401288x). The original paper 
characterizes the microbial communities present on plastic marine debris using Amplicon Pyrotag Sequencing. In particular it examines the differences between the microbial communties present on particles of polypropylene, polyethylene and in non-particle seawater. 
The study includes 3 samples collected from each of these environments within the North Atlantic Subtropical Gyre. 


## Accessing Data

The raw 16s data collected by Zettler et al. is available in the NCBI Short Read Archive BioProject: PRJNA207080. It can be found [here](https://www.ncbi.nlm.nih.gov/sra). 

To download this data for use, we activate our downloading conda environment. 
We then use the command 
```
fastq-dump -O sra/ SRR907634
```
For samples SRR907634 through SRR907642. This generates the nine .fastq samples desired for our analysis. 
The data are not paired-end reads, so we only have one file per sample. 

## Importing Data into Qiime

For the rest of the analysis we use our qiime2-2019.7 environment, in order to use the Qiime2 tools. 
The first step is to transform our .fastq files into one .qza file. In order to do this, we made our manifest, plastic_manifest_1.tsv, which is currently in our data directory. We then used the command: 
```
qiime tools import --type 'SampleData[SequencesWithQuality]' --input-path plastic_manifest_1.tsv --output-path plastic_qiime_step_1.qza --input-format SingleEndFastqManifestPhred33V2
```
This generates the file plastic_qiime_step_1.qza which is available in output/imported_data.


## Qiime2 Analysis

We visualize the sequences and their quality using the command: 
```
qiime demux summarize --i-data plastic_qiime_step_1.qza --o-visualization plastic_unfiltered_vis.qzv
```

The visualization that this command generates can be found in the output/qiime_vis directory of this repository. 
We used this visualization to choose our trimming cutoffs for the following steps. 

Next, we denoise and filter the data, calculate diversity metrics, and assign taxonomy. We do this using the following scripts (available in our scripts directory) in the order below. 
The outputs of each of these files are available in the output directory and are bulleted beneath the script name. The corresponding logs are available in the logs directory.  

* 1. denoising_wrapper.txt
	* denoised_outputs/
		* denoising_stats.qza
		* representative_sequences.qza
		* table.qza

* 2. visualize_denoised.txt
	* qiime_vis/
		* metadata_tabulate.qzv
		* table_summary.qzv
		* table_tabulate.qzv 

* 3. blast_wrapper_silva.txt
	* silva_blasted_both/
		* classification.qza

* 4. visualize_blasted.txt
	* qiime_vis/
		* blasted_metadata.qzv
		
* 5. phylogeny_wrapper.txt
	* phylo_tree/
		* aligned-rep-seqs.qza
		* masked-aligned-rep-seqs.qza
		* rooted-tree.qza
		* unrooted-tree.qza

* 6. alpha_rarefaction_wrapper.txt
	* qiime-vis/
		* alpha-rarefaction.qzv

* 7. diversity_metrics_wrapper.txt 
	* core-metric-results/
		* rarefied_table.qza
		* shannon_vector.qza
		* unweighted_unifrac_distance_matrix.qza
 		* unweighted_unifrac_pcoa_results.qza
 		* weighted_unifrac_distance_matrix.qza
 		* weighted_unifrac_pcoa_results.qza
		* bray_curtis_distance_matrix.qza
		* bray_curtis_pcoa_results.qza
		* evenness_vector.qza
		* faith_pd_vector.qza
		* jaccard_distance_matrix.qza
		* jaccard_pcoa_results.qza
		* observed_otus_vector.qza
	* qiime_vis/
		* unweighted-emperor.qzv
		* unweighted-unifrac-distance-sign.qzv
		* unweighted_unifrac-emperor.qzv
		* weighted_unifrac-emperor.qzv
		* jaccard_emperor.qzv
		* bray_curtis_emperor.qzv
		* shannon_group-sig.qzv

	
