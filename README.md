# Cyrius: WGS-based CYP2D6 genotyper
Cyrius is a tool to genotype CYP2D6 from a whole-genome sequencing (WGS) BAM file. Cyrius uses a novel method to solve the problems caused by the high sequence similarity with the pseudogene paralog CYP2D7 and thus is able to detect all star alleles, particularly those that contain structural variants, accurately. Please refer to our [preprint](https://www.biorxiv.org/content/10.1101/2020.05.05.077966v1) for details about the method.   

## Running the program

This Python3 program can be run as follows:
```bash
star_caller.py --manifest MANIFEST_FILE \
              --genome [19/37/38] \
              --prefix OUTPUT_FILE_PREFIX \
              --outDir OUTPUT_DIRECTORY \
              --threads NUMBER_THREADS
```
The manifest is a text file in which each line should list the absolute path to an input BAM/CRAM file.
For CRAM input, it’s suggested to provide the path to the reference fasta file with `--reference` in the command.  
Additionally, there is an option `--knownFunction` to call only star alleles with known functions, as well as an option `--includeNewStar` to call all star alleles including the newly added, uncurated ones (\*115-\*139) in PharmVar.

## Interpreting the output  

The program produces a .tsv file in the directory specified by --outDir.  
The fields are explained below:  

| Fields in tsv     | Explanation                                                    |
|:------------------|:---------------------------------------------------------------|
| Sample            | Sample name                                                    |
| Genotype          | Genotype call                                                  |   
| Filter            | Filters on the genotype call                                   |   

A genotype of "None" indicates a no-call.  
There are currently four possible values for the Filter column:  
-`PASS`: a passing, confident call.   
-`More_than_one_possible_genotype`: In rare cases, Cyrius reports two possible genotypes for which it cannot distinguish one from the other. These are different sets of star alleles that result in the same set of variants that cannot be phased with short reads, e.g. \*1/\*46 and \*43/\*45. The two possible genotypes are reported together, separated by a semicolon.   
-`Not_assigned_to_haplotypes`: In a very small portion of samples with more than two copies of CYP2D6, Cyrius calls a set of star alleles but they can be assigned to haplotypes in more than one way. Cyrius reports the star alleles joined by underscores. For example, \*1_\*2_\*68 is reported and the actual genotype could be \*1+\*68/\*2, \*2+\*68/\*1 or \*1+\*2/\*68.  
-`LowQ_high_CN`: In rare cases, at high copy number (>=6 copies of CYP2D6), Cyrius uses less strict approximation in calling copy numbers to account for higher noise in depth and thus the genotype call could be lower confidence than usual.     
  
A .json file is also produced that contains more information about each sample.  
  
| Fields in json    | Explanation                                                    |
|:------------------|:---------------------------------------------------------------|
| Coverage_MAD      | Median absolute deviation of depth, measure of sample quality  |
| Median_depth      | Sample median depth                                            |
| Total_CN          | Total copy number of CYP2D6+CYP2D7                             |
| Total_CN_raw      | Raw normalized depth of CYP2D6+CYP2D7                          |
| Spacer_CN         | Copy number of CYP2D7 spacer region                            |
| Spacer_CN_raw     | Raw normalized depth of CYP2D7 spacer region                   |
| Variants_called   | Targeted variants called in CYP2D6                             |
| CNV_group         | An identifier for the sample's CNV/fusion status               |
| Variant_raw_count | Supporting reads for each variant                              |
| Raw_star_allele   | Raw star allele call                                           |
| d67_snp_call      | CYP2D6 copy number call at CYP2D6/7 differentiating sites      |
| d67_snp_raw       | Raw CYP2D6 copy number at CYP2D6/7 differentiating sites       |
