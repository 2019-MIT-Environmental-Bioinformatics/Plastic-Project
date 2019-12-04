# Plastic-Project  
Bioinformatics 2019 Final Project   
Bethany Fowler and Jordan Pitt

This is the repository for Bethany and Jordan's final project. For this project, we set out to replicate the analysis in [Zettler et al. 2013](https://pubs.acs.org/doi/full/10.1021/es401288x). The original paper 
characterizes the microbial communities present on plastic marine debris using Amplicon Pyrotag Sequencing. In particular it examines the differences between the microbial communities present on particles of polypropylene, polyethylene and in non-particle seawater. 
The study includes 3 samples collected from each of these environments within the North Atlantic Subtropical Gyre. 


## Accessing Data

The raw 16s data collected by Zettler et al. is available in the NCBI Short Read Archive BioProject: PRJNA207080. It can be found [here](https://www.ncbi.nlm.nih.gov/sra). 

To download this data for use, we activate our downloading conda environment (plastic_file_downloading_conda.yml in the envs directory). 
We then use the command 
```
fastq-dump -O sra/ SRR907634
```
For samples SRR907634 through SRR907642. This generates the nine .fastq samples desired for our analysis. 
The data are not paired-end reads, so we only have one file per sample. 

## Importing Data into Qiime

For the rest of the analysis we use our qiime2-2019.7 environment (see qiime2_conda.yml in the envs directory), in order to use the Qiime2 tools. 
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

The visualization that this command generates (plastic_unfiletered_vis.qzv) can be found in the output/qiime_vis directory of this repository. 
We used this visualization to choose our trimming cutoffs for the following steps. 

Next, we denoise and filter the data, calculate diversity metrics, and assign taxonomy. We do this using the following scripts (available in our scripts directory) in the order below. 
The outputs of each of these files are available in the output directory and are bulleted beneath the script name. The corresponding logs are available in the logs directory.  

1. denoising_wrapper.txt
	* Used to trim read and filter out low quality reads
	* denoised_outputs/
		* denoising_stats.qza
		* representative_sequences.qza
		* table.qza

2. visualize_denoised.txt
	* Allowed us to visualize what the data looked like after trimming to ensure good quality
	* qiime_vis/
		* metadata_tabulate.qzv
		* table_summary.qzv
		* table_tabulate.qzv 

3. blast_wrapper_silva.txt
	* Used to assign taxonomy to the representative_sequences.qza using the silva data base (used the version already on the cluster)
	* silva_blasted_both/
		* classification.qza

4. visualize_blasted.txt
	* Allowed us to generate a bar plot with the different taxa across the samples. Can look through multiple taxonomic levels
	* qiime_vis/
		* blasted_metadata.qzv
		
5. phylogeny_wrapper.txt
	* Generated a phylogenetic tree based off of the representative_sequences.qza. This is used in the diversity metrics.
	* phylo_tree/
		* aligned-rep-seqs.qza
		* masked-aligned-rep-seqs.qza
		* rooted-tree.qza
		* unrooted-tree.qza

6. alpha_rarefaction_wrapper.txt
	* Allowed for visualization of alpha rarefaction curves to check how many reads were needed to determine richness 
	* qiime-vis/
		* alpha-rarefaction.qzv

7. core_metrics_wrapper.txt and diversity_metrics_wrapper.txt 
	* Generates files looking at the alpha and beta diversity as well as their significance. QZV files include some PCoA files for visualization of diversity
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

	
## Dicussion of Results
See our jupyter-notebooks/Plastic_Project_Final_Comparison.ipynb for a write up of our anaysis and the discussion of our results. 

Contact Bethany Fowler (bfowler@whoi.edu) if you have any further questions about how to reproduce our work for this project! 

