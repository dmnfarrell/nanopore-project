# nanopore sequencing project notes

## Areas of interest

* minknow built-in vs readfish
* which flow cell version for variant calling R9/R10.3. which are we using?
* how to install and use guppy on ubuntu 21.04 - need older version?!
* guppy models compare variant calling result to illumina SNPs for our duplicated samples
    * HAC
    * fast
    * custom trained model?
* try other base calling tools - Bonito
* simulation of fast5 data for e.g. contamination - test with readfish
* storage - large files create issue, SLOW5 for storage of raw files?

## Objectives

* test variant calling for m.bovis vs illumina

## Links 

* https://community.nanoporetech.com/posts
* https://community.nanoporetech.com/technical_documents/data-analysis/v/datd_5000_v1_revo_22aug2016/basecalling-minion-sequenc
* https://labs.epi2me.io/notebooks/Viral_and_Bacterial_Variant_Calling.html
* [readfish](https://github.com/LooseLab/readfish)
* [GPU settings for base calling](https://community.nanoporetech.com/posts/playing-with-gpu-settings)
* [Running GPU basecalling on Ubuntu 21.04](https://hackmd.io/@Miles/ryVAI_KWF)
* [Nanopore basecalling on Google Colab](https://gist.github.com/sirselim/13f70ae69f2a512e7d9e1f00f9704f53)
* [NanoSim](https://github.com/bcgsc/NanoSim)

## Papers

* [Enabling metagenomic surveillance for bacterial tick-borne pathogens using nanopore sequencing with adaptive sampling](https://www.biorxiv.org/content/10.1101/2021.08.17.456696v1.full)
* 

## Guppy GPU base calling 

### Optimising settings

* When performing GPU basecalling there is always one CPU support thread per GPU caller, so the number of callers (--num_callers) dictates the maximum number of CPU threads used.
* Max chunks per runner (--chunks_per_runner): The maximum number of chunks which can be submitted to a single neural network runner before it starts computation. Increasing this figure will increase GPU basecalling performance when it is enabled.
* Number of GPU runners per device (--gpu_runners_per_device): The number of neural network runners to create per CUDA device. Increasing this number may improve performance on GPUs with a large number of compute cores, but will increase GPU memory use. This option only affects GPU calling.
* runners x chunks_per_runner x chunk_size < 100000 * [max GPU memory in GB]

### Testing

For phage lambda genome using an RTX 3060 with 12GB RAM. Use nvtop and htop to view memory and cpu usage. Too many gpu_runners_per_device will fill GPU memory.

HAC 

|setting| value|
|--------|------|
|model_file| template_r9.4.1_450bps_hac.jsn|
|chunk_size | 2000|
|cpu_threads_per_caller |20|
|num_callers |20|
|chunks_per_runner |512|
|gpu_runners_per_device| 5|

Basic test results

|model|time(s)|memory(GB)|
|-----|--------|---------|
|Fast|74|8.4|
|HAC|522|9.8|

### Mbovis

The HAC model with above settings took 44 minutes to process a set of M.bovis reads.
