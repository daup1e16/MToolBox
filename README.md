###MToolBox (v.1.0)
MToolBox is a highly automated bioinformatics pipeline to reconstruct and analyze human mitochondrial DNA from high throughput sequencing data. MToolBox includes an updated computational strategy to assemble mitochondrial genomes from Whole Exome and/or Genome Sequencing (PMID: 22669646) and an improved fragment-classify tool (PMID:22139932) for haplogroup assignment, functional and prioritization analysis of mitochondrial variants. MToolBox provides pathogenicity scores, profiles of genome variability and disease-associations for mitochondrial variants. MToolBox provides also a Variant Call Format file (version 4.0) featuring, for the first time, allele-specific heteroplasmy.
The MToolBox pipeline includes:

- an extended version of a previously published computational strategy for mtDNA genome assembly (PMID: 22669646). The pipeline has been integrated with the detection of insertions and deletions (indels) and the assessment of the heteroplasmic fraction (HF) and related confidence interval (CI) for each mt variant. HF and CI are integrated as genotype specific meta-information in a Variant Call Format (version 4.0) file;
- the mt-classifier tool, for haplogroup prediction, mt variant functional annotation and prioritization


###SYSTEM REQUIREMENTS

- UNIX-based OS. ***WARNING: MToolBox v.1.0 current installation is only compatible with Linux OS***

All the following dependancies will be installed by default running the `install.sh` script (see the ###INSTALLATION section in this README):
- Python2.7 (www.python.org, provided within the Anaconda distribution, https://www.continuum.io/downloads) 
- GSNAP (http://research-pub.gene.com/gmap/)
- samtools (https://sourceforge.net/projects/samtools/files/samtools/)
- MUSCLE (http://www.drive5.com/muscle/downloads.htm)
- ZLIB (http://zlib.net/)

###MTOOLBOX SCRIPTS

*MToolBox.sh* is the shell script invoking all the following programs:
- *Sam2Fastq.jar* to convert either SAM or BAM files to FASTQ format. This module is included in the ext_tools folder;
- *mapExome.py* to map reads on RSRS and hg19. It invokes GSNAP;
- *SortSam.jar*, MarkDuplicates.jar, SamFormatConverter.jar (PicardTools suite; http://picard.sourceforge.net) for SAM/BAM manipulation at several stages of the pipeline and elimination of PCR duplicates. These modules are included in the ext_tools folder;
- *GenomeAnalysisTK.jar* (GATK suite; PMID: 20644199) for indels realignment. This module is included in the ext_tools folder;
- *assembleMTgenome.py* to assemble the mitochondrial genome and perform variant calling and heteroplasmy quantification, by invoking:
- *mpileup* (samtools)
- *mtVariantCaller.py* module;
- *VCFoutput.py* to report variants in the VCF file (version 4.0). It invokes the vcf module (elease 0.60 PyVCF; https://githubcom/jamescasbon/PyVCF/);
- *mt-classifier.py* for haplogroup prediction;
- *variants_functional_annotation.py* for functional annotation;
- *prioritization.py* for variant prioritization;
- *summary.py* to report statistics about coverage of reconstructed genomes, predicted haplogroups, number of homoplasmic, heteroplasmic and prioritized variants


###OTHER FILES REQUIRED 

By default, MToolBox adopts the RSRS (Reconstructed Sapiens Reference Sequence, PMID: 22482806) as mitochondrial reference genome and hg19 as nuclear reference genome. Alternatively, the user can choose to use the rCRS (revised Cambridge Reference Sequence).
Fasta files of hg19 nuclear genome and mitochondrial DNA used by MToolBox are available at https://sourceforge.net/projects/mtoolbox/ where have been uploaded for users'convenience. However, with the v.0.1 of the pipeline they are now downloaded and installed by default by the `install.sh` MToolBox script, together with the required GSNAP databases.

The MToolBox folder includes the *MITOMAP_HMTDB_known_indels.vcf* file, containing 127 known indels annotated in MITOMAP and HMTDB and the related *intervals_file.list* used by GATK *GenomeAnalysisTK.jar* module.
The MToolBox folder also includes 2 tab-separated files, *patho_table.txt* and *sitevar_modified.txt*, containing variant-specific and site-specific information, respectively, used in the annotation step.

###INSTALLATION

Add the MToolBox path to your system PATH with the following command:
```
export PATH="/path/to/MToolBox/:$PATH"
```
then, from the MToolBox folder, run the `install.sh`script:
```
install.sh
```
to get a FULL installation of all the MToolBox dependancies. This may take a while, so please be patience until the full process is successfully completed. We ***STRONGLY*** raccomend to use the default versions provided by the install script:
- GSNAP 2015-12-31.v7 
- Anaconda distribution 2-2.5.0
- Zlib v 1.2.8
- MUSCLE version 3.8.31
- samtools version 1.3

The default Kmer value for GSNAP database generation is 15. 

However, if you want to change any of the previous versions or parameters, please specify the following options:
```
install.sh -g <gsnap_version> <anaconda_version> -z <zlib_version> -m <muscle_file> -s <samtools_version> -k <kmer_to_build_gsnap_db>
```

To install/update just one of the MToolBox software dependancies, please do:
```
install.sh -i <software_name>
```
Where the software names are:
```
gsnap
anaconda
muscle
zlib
samtools
gsnap_db
```
To get the install help:
```
install.sh -h
```
###RUNNING MTOOLBOX

Add MToolBox to your system PATH with the following command:
```
export PATH="/path/to/MToolBox/:$PATH"
```

Then, in the directory where you want to execute MToolBox, run the pipeline as following:
```
MToolBox.sh -i <conf.sh>
```

Please note that you MUST specify the ```-i``` option that takes as an argument a config file, inside which the user MUST specify 2 mandatory arguments:
```
input_type=
ref=
 ```
where `input_type` can accept `bam|fastq|fastq.gz|fasta|sam`, `ref` can accept `RSRS|RCRS`.
An example of the config file required by MToolBox is provided by the `test_config.sh` file. 
All the other arguments are OPTIONAL, if the default installation of MToolBox was successfully completed.
In case the user would like to use some custom installation of the MToolBox software dependancies, please provide the ***FULL PATH*** to the installation as argument value. For further help, please read carefully the instructions within the `test_config.sh` file.

The run MToolBox command must be executed inside the folder containing input files (where also output files will be placed), otherwise set the `input_path` and `output_name` value in the config file. 

For a complete list of *MToolBox.sh* options please run as follows:

```	
MToolBox.sh -h
```
Please note that only one of the supported input types at the time can be used within a single MToolBox run. MToolBox also accepts compressed fastq files (FASTQ.GZ).

**IMPORTANT**: if you wish to run again MToolBox in the same folder, it is preferable that you delete all the files produced during the previous execution.


###MTOOLBOX PROGRAMS OPTIONS

Besides the mandatory ```-i``` option, the execution of MToolBox can be fine-tuned by tweaking parameters of the *mapExome.py* and *assembleMTgenome.py* scripts by running MToolBox as follows:
```
MToolBox.sh -i <config.sh> -m "<mapExome_options>" -a "<assembleMTgenome_options>"

```
**Most relevant options**:

```-t``` (*mapExome.py*) to set the number of threads used by gsnap. Default is 8.

```-t``` (*assembleMTgenome.py*) to set the minimum distance from the read end required to retain an indel for variant calling. Default is 5. Please note that only values >= 5 are allowed.

```-z``` (*assembleMTgenome.py*) to set the minimum heteroplasmy threshold for variants to be reported in the FASTA consensus sequence. Default is 0.80.

**For the full list of MToolBox options, please run as follows**:
```
MToolBox.sh -h
```
**For the full list of mapExome and assembleMTgenome options, please run as follows**:
```
mapExome.py -h
assembleMTgenome.py -h
```
**Usage examples**:

```
MToolBox.sh -i test_config.sh -m "-t 20" -a "-t 10 -z 0.6"
```
This command will run MToolBox on the set of samples specified within the `test_config.sh` file, setting the number of GSNAP threads 20 (-m "-t 20.."), the minimum nucleotide distance allowed from read end to retain indels to 10 (-a "-t 10") and the heteroplasmy threshold to retain nucleotide variants in the FASTA consensus sequence of 0.6 (-a "-z 0.6"). 

###MTOOLBOX OUTPUTS

MToolBox default outputs are:
- *VCF_file.vcf* contains all the mitochondrial variant positions against RSRS/rCRS and other meta-information. 
- *mt_classification_best_results.csv* reports for each sequence the best haplogroup prediction. If the sorting results in more than one best haplogroup prediction with equal probability, the output will enclose all of them.
- *prioritized_variants.txt* contains annotation only for prioritized variants for each sample analyzed, defined as variants recognized by the three reference sequences (rCRS, RSRS and MHCS), sorted per increasing nucleotide variability.
- *summary_<date_time>.txt* reporting a brief summary of selected options, predicted haplogroups, number of total and prioritized variants for each sample and, for NGS data only, coverage of reconstructed genomes, number of homoplasmic and heteroplasmic variants.
- *OUT_<sample_name>* folders containing:
- *outmt.sam*: reads mapped onto the human mitochondrial DNA;
- *logmt.txt*: GSNAP log file for mitochondrial DNA mapping;
- *outmt.fastq*: fastq file of single reads extracted from outmt.sam file;
- *outmt1.fastq*: fastq file of paired reads extracted from outmt.sam file;
- *outmt2.fastq*: fastq file of paired reads extracted from outmt.sam file;
- *outhumanS.sam*: single reads mapped onto the entire human genome;
- *loghumanS.txt*: GSNAP log file for human genome mapping (single reads);
- *outhumanP.sam*: paired reads mapped onto the entire human genome;
- *loghumanP.txt*: GSNAP log file for human genome mapping (paired reads); - OUT.sam: alignments of reads uniquely mapped on mitochondrial genome;  
- *OUT2.sam*: alignments of reads uniquely mapped on mitochondrial genome, after processing with IndelRealigner and/or MarkDuplicates. This file will be generated anyway, even if these two processes have been disabled;
- *mtDNAassembly-table.txt*: the main table describing the assembly position by position;
- *mtDNAassembly-Contigs.fasta*: a fasta file including all reconstructed contigs;
- *mtDNAassembly-coverage.txt*: a text file including the coverage per contig and per mitochondrial known annotation;
- *logassemble.txt*, which is the log file of the assembleMTgenome.py script 
- *sorted.csv* contains a table with each haplogroup whose prediction is > 90%. It contains the following fields:
	1. *N* = the number of SNPs in the fragment sequence vs RSRS;
	2. *Nph* = the number of SNPs (among N) mapped in Phylotree;
	3. *Nph_tot* = the number of SNPs defining the haplogroup in the whole genome;
	4. *Nph_exp* = the number of SNPs defining the haplogroup in the fragment region;
	5. *P_Hg* = the prediction percentage value for the haplogroup (Nph/Nph_exp*100);
	6. *Missing sites* = the mutation events that are not present in the query genome but were expected from its respective path to the RSRS. These mutations may also point to a sequencing error;
- *merged\_diff.csv* file reports the SNPs between the query genome and each of the three sequences RSRS, rCRS and hg\_MHCS (*Macro-Haplogroup Consensus Sequence*);
- *\<sample\_name>.csv* contains a table where, for all the haplogroups present in the Phylotree Build 15, are reported the same data as in the file *\<sequence\_name>.sorted.csv*, except for the *Missing Sites* field;
- *annotation.csv* is a further elaboration of the file *\merged\_diff.csv*, providing, for each mt variant allele between the query genome and each of the three sequences RSRS, rCRS and hg\_MHCS, several annotations:
	1. *Sample* = sample name;
	2. *Variant Allele* = nucleotide position in mitochondrial genome followed by the variant allele;
	3. *HF* = Heteroplasmic Fraction, as reported in the VCF file;
	4. *CI_lower;CI_upper* = lower and upper limits of the confidence interval of the heteroplasmic fraction;
	5. *RSRS* = if "yes", the variant is recognized by RSRS;
	6. *MHCS* = if "yes", the variant is recognized by the Macro-Haplogroup Consensus Sequence;
	7. *rCRS* = if "yes", the variant is recognized by rCRS;
	8. *Haplogroup* = the best predicted haplogroup;
	9. *Other Haplogroups* = if "+", the variant defines other haplogroups beside the sample specific haplogroup;
	10. *Locus* = mitochondrial gene locus;
	11. *Nt Variability* = SiteVar variability value;
	12. *Codon Position* = nucleotide position within the codon;
	13. *Aa Change* = amino Acid Change;
	14. *Aa variability* = MitVarProt amino acid variability value;
	15. *tRNA annotation* = specific information regarding mitochondrial tRNA genes (position in tRNA; tRNA type; cloverleaf secondary region; mature nucleotide; involvement of the specific position in tRNA folding);
	16. *Disease score* = an overall Disease Score, generated as a weighted average of pathogenicity prediction scores for non-synonymous variants. For details, see the related publication (PMID: 26621530);
	17. *RNA predictions* = score added for 49 variants in rRNA genes (PMID: 24092330) and 207 variants in tRNA genes (PMID: 21882289; PMID:23696415). Scores were correlated on a scale from 0 to 1. Threshold for rRNAs=0.51. Threshold for tRNAs= 0.31. Low pathogenicity under the fixed thresholds;
	18. *MutPred pred* = MutPred predictions (High pathogenicity, Low pathogenicity);
	19. *MutPred Score* = MutPred Pathogenicity Score (0.000-1.000);
	20. *PolyPhen-2 HumDiv Pred* = Polyphen-2 HumDiv predictions (Benign, Possibly damaging, Probably damaging, Unknown);
	21. *PolyPhen-2 HumDiv Prob* = Polyphen-2 HumDiv probabilities (0.000-1.000);
	22. *PolyPhen-2 HumVar Pred* = Polyphen-2 HumVar predictions (Benign, Possibly damaging, Probably damaging, Unknown);
	23. *PolyPhen-2 HumVar Prob* = Polyphen-2 HumVar probabilities (0.000-1.000);
	24. *PANTHER Pred* = PANTHER predictions (Neutral, Disease, Unclassified) by SNPs&GO software;
	25. *PANTHER Prob* = PANTHER probabilities (0.000-1.000) by SNPs&GO software;
	26. *PhD-SNP Pred* = PhD-SNP predictions (Neutral, Disease, Unclassified) by SNPs&GO software;
	27. *PhD-SNP Prob* = PhD-SNP probabilities (0.000-1.000) by SNPs&GO software;
	28. *SNPs&GO Pred* = SNPs&GO predictions (Neutral, Disease, Unclassified) by SNPs&GO software;
	29. *SNPs&GO Prob* = SNPs&GO probabilities (0.000-1.000) by SNPs&GO software;
	30. *Mitomap Associated Disease(s)* = MITOMAP annotation of disease-associated mutations;
	31. *Mitomap Homoplasmy* = MITOMAP annotation of homoplasmy condition;
	32. *Mitomap Heteroplasmy* = MITOMAP annotation of heteroplasmy condition;
	33. *Somatic Mutations* = MITOMAP annotation of cell or tissue type for somatic mutations;
	34. *SM Homoplasmy* = MITOMAP annotation of homoplasmy condition in somatic mutations;
	35. *SM Heteroplasmy* = MITOMAP annotation of heteroplasmy condition in somatic mutations;
	36. *ClinVar* = ClinVar annotation of associated disease(s);
	37. *OMIM Link* = link to OMIM entry;
	38. *dbSNP ID* = rs ID reported in dbSNP database;
	39. *Mamit-tRNA link* = link to Mamit-tRNA site annotation;
	40. *PhastCons20Way* = PhastCons conservation score calculated on 20 vertebrates using hg38+rCRS as reference sequence;
	41. *PhyloP20Way* = PhyloP conservation score calculated on 20 vertebrates using hg38+rCRS as reference sequence;
	42. *AC/AN 1000 Genomes* = Ratio between allele count and allele number of possibly pathogenic variants found in 1000 Genomes using MToolBox;
	43. *1000 Genomes Homoplasmy* = annotation of homoplasmy status in 1000 Genomes variants;
	44. *1000 Genomes Heteroplasmy* = annotation of the heteroplasmy status in 1000 Genomes variants.

**WARNING**! Please note that the heteroplasmy fractions and the related confidence interval will be reported only for variants found against the reference sequence chosen for read mapping.


###NOTE ON FILE NAMES

The basename for output folder and files will be parsed from the input filename, for each sample. 

BAM|SAM files: BAM or SAM files MUST be renamed as *\<sample\_name\>.ext*, eg:
```
mv HG00096.chrom20.ILLUMINA.bwa.GBR.low_coverage.20101123.bam HG00096.bam
```
and "HG00096" will be the output basename.

FASTQ files: FASTQ files MUST be renamed as \<sample\_name\>.R1.fastq, \<sample\_name\>.R2.fastq for PAIRED-END data and \<sample\_name\>.fastq for SINGLE END data. FASTQ compressed input files could be accepted with *.fastq.gz extension.

**IMPORTANT**: Please note that MToolBox cannot recognize more than one PAIRED-END couple of fastq files (R1+R2) and one SINGLE-END fastq file per sample.


###MTOOLBOX GUI

For small datasets, you could use MToolBox at MSeqDR website https://mseqdr.org/mtoolbox.php (PMID: 25542617).


###CONTACTS AND CITATION

Contacts:
dome.simone [at] gmail.com ; 
claudia.calabrese23 [at] gmail.com


##If you use MToolBox, please cite:
Calabrese C, Simone D, Diroma MA, Santorsola M, Guttà C, Gasparre G, Picardi E, Pesole G, Attimonelli M. MToolBox: a highly automated pipeline for heteroplasmy annotation and prioritization analysis of human mitochondrial variants in high-throughput sequencing. *Bioinformatics*. 2014 Nov 1;30(21):3115-7. doi: 10.1093/bioinformatics/btu483. Epub 2014 Jul 14. PubMed PMID: 25028726; PubMed Central PMCID: PMC4201154.

**If you would like to use also MToolBox GUI, please visit**:

https://mseqdr.org/mtoolbox.php

and refer to:

Falk MJ, Shen L, Gonzalez M, Leipzig J, Lott MT, Stassen AP, Diroma MA, Navarro-Gomez D, Yeske P, Bai R, Boles RG, Brilhante V, Ralph D, DaRe JT, Shelton R, Terry SF, Zhang Z, Copeland WC, van Oven M, Prokisch H, Wallace DC, Attimonelli M, Krotoski D, Zuchner S, Gai X; MSeqDR Consortium Participants; MSeqDR Consortium participants. Mitochondrial Disease Sequence Data Resource (MSeqDR): a global grass-roots consortium to facilitate deposition, curation, annotation, and integrated analysis of genomic data for the mitochondrial disease clinical and research communities. *Mol Genet Metab*. 2015 Mar;114(3):388-96. doi: 10.1016/j.ymgme.2014.11.016. Epub 2014 Dec 4. Review. PubMed PMID: 25542617; PubMed Central PMCID: PMC4512182.

**If you would like to have more details about the variants prioritization method implemented in MToolBox, please refer to**:

Santorsola M, Calabrese C, Girolimetti G, Diroma MA, Gasparre G, Attimonelli M. A multi-parametric workflow for the prioritization of mitochondrial DNA variants of clinical interest. *Hum Genet*. 2016 Jan;135(1):121-36. doi:10.1007/s00439-015-1615-9. Epub 2015 Nov 30. PubMed PMID: 26621530; PubMed Central PMCID: PMC4698288.

##Get support for MToolBox (command-line version)
Please join the MToolBox google group:
https://groups.google.com/forum/?hl=IT#!forum/mtoolbox-users


