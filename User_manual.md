#miRPro user manual

Version: 1.1
Date: 2016/06/16

###Table of Contents

1. [How to cite the software](#how-to-cite-the-software)
2. [How to install prerequisite tools](#how-to-install-prerequisite-tools)
3. [How to install mirPRo](#how-to-install-mirpro)
4. [How to use mirPRo](#how-to-use-mirpro)
5. [How to test mirPRo](#how-to-test-mirpro)
6. [Where to find the result files](#where-to-find-the-result-files)
7. [How to use result files to perform statistical analysis](#how-to-use-result-files-to-perform-statistical-analysis)

####How to cite the software
Shi J, Dong M, Li L, Liu L, Luz-Madrigal A, Tsonis PA, Rio-Tsonis KD and Liang C (2015) mirPRo - a novel standalone program for differential expression and variation analysis of miRNAs. Sci Rep, 5:14617.  http://www.nature.com/articles/srep14617

####How to install prerequisite tools

#####1. FASTX-Toolkit

Go to http://hannonlab.cshl.edu/fastx_toolkit/download.html and follow the instruction in "Program Installation" to install the toolkit (tested version 0.0.14).

**Warning**: the prerequisite of FASTX-Toolkit includes pkg-config, gcc, and wget. Also, the compatible version of 
libgtextutils (tested version 0.7) is needed before you install FASTX-Toolkit. 

#####2. RNAfold

Go to http://www.tbi.univie.ac.at/RNA/index.html#download and download the ViennaRNA Package (tested version 2.1.9).
Alternatively, you can install Ubuntu package from PPA:

    sudo apt-add-repository ppa:j-4/vienna-rna
    sudo apt-get update
    sudo apt-get install vienna-rna

#####3. randfold

Go to http://bioinformatics.psb.ugent.be/supplementary_data/erbon/nov2003/ and download "Minimum free energy of 
folding randomization test software - randfold version 2 (C version)", and after unzipping, you can follow the readme
file to finish installation. 

**Warning**: randfold require Sean Eddy's SQUID library http://selab.janelia.org/software.html installed first. 

#####4. Novoalign

Go to http://www.novocraft.com/support/download/ and download Novoalign (tested version V3.02.12).

For instance, download novocraftV3.02.12.Linux3.0.tar.gz and unzip, then move the whole folder into /usr/local/genome/novocraft folder.  

To enable the path for this software, append the following statement within the file .bashrc:

    export PATH=$PATH:/usr/local/genome/novocraft/2015-02-13

#####5. HTSeq

Go to http://www-huber.embl.de/users/anders/HTSeq/doc/install.html#installation-on-linux and following the instruction
to install HTSeq (tested version 0.6.1p1).

#####6. gcc and g++ (version 4.8+)

    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update; sudo apt-get install gcc-4.8 g++-4.8
    sudo update-alternatives --remove-all gcc
    sudo update-alternatives --remove-all g++
    sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 20
    sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.8 20
    sudo update-alternatives --config gcc
    sudo update-alternatives --config g++

#####7. Test if installation is successful

To test the success of installation, type the following commands in the terminal:

    fastq_quality_filter -h
    fastx_collapser -h
    novoindex
    novoalign
    randfold
    RNAfold -h
    htseq-count
    gcc -v
    g++ -v

####How to install mirPRo

#####1. System requirement

This program has been tested on Ubuntu 12.04 and Ubuntu 14.04. 

#####2. Install mirPRo package

Download the most recent mirPRo package, e.g., mirPRo.XXX.tgz and unzip it.

The executable programs are in the folder `mirPRo.XXX/bin/`. In addition, you can find source code, script, and user manual inside mirPRo package.

#####3. Add mirPRo executable files to $PATH (optional)

To enable the path for this software, append the following statement within the file .bashrc:

    export PATH=$PATH:/home/user/Downloads/mirPRo/bin

####How to use mirPRo

#####1. Important step or information before using mirPRo

#####1.1. Sequencing adapter information for small RNA-Seq

For instance, we can obtain the following information for RNA-Seq project that utilizes one pair of adapters (5P-Adapter and 3P-Adapter). Their sequences are presented in both plus strand and minus strand

    (1) Plus strand
    [5P-adapter]=====[3P-Adapter]
    GTTCAGAGTTCTACAGTCCGACGATC ========= AGATCGGAAGAGCACACGTCT                                    
    (2) Minus strand
    [5P-adapter]=====[3P-Adapter]
    GATCGTCGGACTGTAGAACTCTGAAC ========= TCTAGCCTTCTCGTGTGCA 

What will be used in mirPRO adapter trimming? the 3P-Adapter in plus strand

#####1.2. Creation of index file for reference genome sequence using novoindex

novoindex is a program within Novoalign package (pre-requisite tool). You can use the following command to
generate the indexed file for a reference genome:

    novoindex -k 12 -s 1 XYZ.idx XYZ.fa

#####1.3. mirPRo needs both mature and precursor miRNA data

You need to provide the complete mature and hairpin miRNA data in FASTA format downloaded from 
[miRBase](http://www.mirbase.org/).

    README	Release notes - read these first!
    miRNA.dat	all published miRNA data in EMBL format
    hairpin.fa	Fasta format sequences of all miRNA hairpins
    mature.fa	Fasta format sequences of all mature miRNA sequences
    miRNA.diff	Changes between the last release and this
    miRNA.dead	List of entries that have been removed from the database
    miFam.dat	Family classification of miRNA hairpin sequences
 
We need to use mature.fa, hairpin.fa and miFam.dat three files.

#####1.4. Arf format
This file format was created by [miRDeep2](https://www.mdc-berlin.de/36105849/en/research/research_teams/systems_biology_of_gene_regulatory_elements/projects/miRDeep/documentation), and is also used in our program to record the read mapping information.

    ******************************"arf" file format******************************************
    Each line in such a file contains 13 columns. Example line:

    PAN_123456_x969696    21    1    21    ATACAATCTACTGTCTTTCCT    chr22    21    46508682    46508702    ATACAATCTACTGTCTTTCCT    +    1    mmmmmmmmmmmmmmmmmmmmm

    1: read identifier
    2: length of read sequence
    3: start position in read sequence that is mapped
    4: end position in read sequence that is mapped
    5: read sequence
    6: identifier of the genome-part to which a read is mapped to. This is either a scaffold id or a chromosome name
    7: length of the genome sequence a read is mapped to
    8: start position in the genome where a read is mapped to
    9: end position in the genome where a read is mapped to
    10: genome sequence to which a read is mapped
    11: genome strand information. Plus means the read is aligned to the sense-strand of the genome. Minus means it is aligned to the antisense-strand of the genome.
    12: Number of mismatches in the read mapping
    13: Edit string that indicates matches by lower case 'm', mismatches by upper case 'M', ambiguous nucleotides by 'N', insertions by 'I', deletions by 'D', soft clips by 'S'.
    *****************************************************************************************

#####2. Known miRNA and novel miRNA analysis

Run "bin/mirpro".

    mirpro helps quantify known miRNAs and optionally predict novel miRNAs from miRNA sequencing data.
    Usage: mirpro [options]

    [mandatory parameters]
    -i <string>  miRNA sequencing file, FASTQ format; can be multiple files; e.g., -i file_1.fastq -i file_2.fastq ... -i file_n.fastq;
    -m <string>  mature miRNA file downloaded from miRBase, FASTA format;
    -p <sring>  precursor miRNA file downloaded from miRBase, FASTA format;
    -s <string>  miRBase 3-letter code for the species you want to analyze ; e.g., 'mmu' for mouse; 'hsa' for human; if your species is not in miRBase, set 'null', and set '--novel 1' to predict novel miRNA;
    -a <string>  adapter sequence in miRNA sequencing data; if data has no adapter, set 'null;'
    --novel <int>  whether to predict novel miRNAs; 1: yes; 0: no; if yes, option '-g' and '--other' must be set (see below);

    [optional parameters]
    -d <string>  directory for output files; default = '.';
    -t <int>  the number of threads will be used in the program; default = 1; it should <= the number of input fastq file;
    -g <string>  genome file for this species, used for novel miRNA prediction, FASTA format;
    --gtf <string>  the annotation file in GTF format for species under study; if it is given, '-g' must be set, and read mapping against whole genome will be performed (maximum novelalign penalty score = 60), and the number of mapped reads of different features in the annotation file will be calculated;
    -f <string>  file of precursor family information downloaded from miRBase (miFam.dat); if given, additional known miRNA expression quantification result will be shown based on precursor family;
    --seed <int>  whether to perform miRNA seed region (2-8 nt) check, and keep reads that have no mapping mistake in the seed region; 1: yes: 0: no; default = 1;
    --map-detail <int>  whether to show read mapping details in each sample; 1: yes; 0: no; default = 1;
    -v <int>  whether to variation of reads counted as mature miRNA; 1: yes; 0: no; default = 1;
    --arm <int>  whether to show dominant forms (5p or 3p; read counts with ratio > 2 and difference > 20) of mature miRNAs with two possible forms generated from same precursors in all samples;1: yes; 0: no; default = 1;

    [optional parameters for read clean and known miRNA quantification]
    -q <int>  whether to perform read quality filter by FASTX-Toolkit; 1: yes; 0: no; if yes, 'fastq_quality_filter -q 20 -p 95' will be used; default = 0;
    -c <int>  minimum count of clean collapsed read that will be kept; default = 1;
    --adapter-mistake <int>  allowed nucleotide mistakes in adapter detection, including indel or mismatch; default = 1;
    --adapter-len <int>  minimum adapter length in sequence; default = 10;
    --clean-len <int>  minimum read length after removing adapter; default = 15;
    --map-len <int>  minimum mapped length of reads which are mapped to precursor miRNAs; default = 15;
    -o <int>  the gap opening penalty in novoalign mapping; default = 40;
    -e <int>  the gap extend penalty in novoalign mapping; default = 6;
    --map-score <int>  maximum mapping penalty score; default is 60;
    --5-upstream <int>  max number of nucleotides shift in the upstream of the 5' end of the mature miRNA; default = 3;
    --3-upstream <int>  max number of nucleotides shift in the upstream of the 3' end of the mature miRNA; default = 3;
    --5-downstream <int>  max number of nucleotides shift in the downstream of the 5' end of the mature miRNA; default = 3;
    --3-downstream <int>  max number of nucleotides shift in the downstream of the 3' end of the mature miRNA; default = 3;

    [optional parameters for novel miRNA prediction]
    --other <string>  name of miRBase 3-letter code of other related species, multiple species can be set; e.g., '--other mmu --other rno'; if you don't have a related species, set 'null';
    --index <string>  novelalign genome index file for studied species, if given, program will run faster, skipping the step of building index; the index file must be built using the same version of novoalign with that used by mirpro;
    -n <int>  cut-off score (-10 to 10) for novel miRNA prediction; lower score means higher sensitivity; higher score means higher true positive rate; default = 0;
    -r <int>  whether to perform randfold to calculate p-value innovel miRNA prediction; 1: yes; 0: no; default = 0; if yes, runtime will increase significantly, but result will be more reliable;

**Important Notes**

For prediction of novel miRNAs, you need to specify the following parameters:

    --novel 1 --other [related_speceis] -g [genome_sequence] --index [novelalign_genome_index]

If you set '-r 1', each time you perform novel miRNA prediction, you will get different results because the third-party program "randfold" can generate different ourput p-values each time you run it with the same input. For those novel miRNAs around the cut-off score, they might be infulenced because of the inconsistent "randfold" p-values.

To enable read cataloging function, you need to specify the following parameters:

    -gtf [gtf_file]
    
You can download the gene annotation file in GTF format from ensembl website: http://useast.ensembl.org/info/data/ftp/index.html?redirect=no.

To show miRNA gene family expression data, you need to specify the following parameters:

    -f [miRNA_family_data]
    
You should use the file "miRNA.dat" downloaded from miRBse as the miRNA family data.

To enable multiple threads, you need to specify the following parameters:

    -t [number_of_threads]
    
It should <= the number of input fastq file.

#####3. "Arm switching" detection

Run "bin/mirpro_arm_switch", using the file `"[output_directory]/result/arm.csv"` as input.
The output result file records the miRNAs that have "arm switching" phenomenon across different treatments.

    This program can detect 'arm switching' phenomenon with the file 'result/arm.csv' in the output directory of mirpro as input.
    Usage: mirpro_arm_switch [options]
    [mandatory parameters]
    -i <string>  input file including the dominant miRNAs (3p or 5p) in all samples.
    -o <string>  output result file.
    -t <string>  treatment for each sample in the input file; can be multiple; e.g., -t treatment_1 -t treatment_2 ... -t treatment_n; the treatments and samples in the input file must be in the same order; at least two treatments must be given.

**Important Notes**

Before you run this program, please open the file "result/arm.csv", and check the order of the sample file names in the third row. You have to provide the treatment name for each sample in the same order. 
For example, assume we have the following samples and corresponding treatments:

Sample	Treatment
Data1	T1
Data2	T2
Data3 	T3
Data4	T4

And in the third row of the file "result/arm.csv", we have the following order for our samples: "Data1, Data2, Data3, Data4".
For the input of mirpro_armSwitch, you have enter the treatment parameter in the order: "-t T1 -t T2 -t T3 -t T4" as the parameters for treatment.

#####4. Read cataloging function for clean miRNA sequencing reads (separated module)

To use this module, you need to install SAM tools first (https://sourceforge.net/projects/samtools/?source=navbar). Then run "bin/mirpro_feature_pro".
The output result files include the read cataloging information in csv format and unmapped read sequences in FASTA format.

    This program can perform read cataloging (using htseq-count) for aligned RNA sequencing reads in SAM/BAM file according to the given genome annotation file in GTF/GFF3 format. htseq-count and samtools are required for this program.
    Usage: mirpro_feature_pro [options]

    [mandatory parameters]
    -i <string>  alignment_file; SAM/BAM format;
    -t <string>  annotation_file; GTF/GFF3 format;
    -o <string>  output_directory;

    [optional parameters]
    -c <yes/no>  whether the aligned reads in SAM/BAM file is collapsed; default = 'no'; for paired-end data, collapsed aligned reads are not supported in this program;
    -x <yes/no>  whether to use the output sam file of htseq-count to categorize read features (slow, but more accurate); default = 'no'; if 'no', this program will use the output count of htseq-count to categorize read features; for collapsed aligned reads, this will be automatically set to 'yes'; for paired-end data, this can be only set to 'no';
    -s <yes/no/reverse>  whether the data is from a strand-specific assay; default = 'yes';
    -r <string>  For paired-end data, the alignment have to be sorted either by read name or by alignment position. If your data is not sorted, use the samtools sort function of samtools to sort it. Use this option, with 'name' or 'pos' to indicate how the input data has been sorted. Default = 'name'.
    -m <string>  mode to handle reads overlapping more than one feature; possible values are 'union', 'intersection-strict' and 'intersection-nonempty' (default = 'union');
    -a <int>  skip all reads with alignment quality lower than the given minimum value; default = 0;
    --type  <string>  feature type (3rd column in GFF file) to be used, all features of other type are ignored (default, suitable for RNA-Seq analysis using an Ensembl GTF file: 'exon');
    --samout <string>  name of SAM output file including all SAM alignment records, annotating each line with its assignment to a feature or a special counter (as an optional field with tag 'XF'); default = 'htseq_count_out.sam';
    --idattr <string>  GFF attribute to be used as feature ID. This option is only valid for GTF file. The default, suitable for RNA-Seq analysis using an Ensembl GTF file, is gene_id. If the feature ID is not set to gene_id, the htseq-count output will be given, but the read cataloging statistics will not be given. For GFF3 file, the program will convert it to equivalent GTF file, and run htseq-count setting the feature ID to gene ID.

**Important Notes**

For SAM/BAM file without collapsed reads, if you want to use the output count of htseq-count to perform read cataloging:

    mirpro_feature_pro -i [alignment_file] -t [annotation_file] -o [output_directory]

For SAM/BAM file without collapsed reads, if you want to use the output sam file of htseq-count to perform read cataloging (more accurate, but slow):

    mirpro_feature_pro -x yes -i [alignment_file] -t [annotation_file] -o [output_directory]

For SAM/BAM file with collapsed reads, the output sam file of htseq-count must be used to perform read cataloging:

    mirpro_feature_pro -c yes -i [alignment_file] -t [annotation_file] -o [output_directory]

In the output count of htseq-count, the count of `"__alignment_not_unique"` represents the number of all alignments for non-uniquely aligned reads (not the number of non-uniquely aligned reads). So if using this file to perform read cataloging (-x no, by default), you will not get the accurate number of non-uniquely aligned reads. While using the output sam file of htseq-count to perform read cataloging (-x yes), in our final result, `"__alignment_not_unique"` can represent the number of non-uniquely aligned reads.

####How to test mirPRo

#####1. Links of datasets (used in our paper) to test our program:

Reference sequences:
mouse: ftp://ftp.ensembl.org/pub/release-81/fasta/mus_musculus/dna/Mus_musculus.GRCm38.dna.toplevel.fa.gz
human: ftp://ftp.ensembl.org/pub/release-81/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz

Gene annotation files:
mouse: ftp://ftp.ensembl.org/pub/release-81/gtf/mus_musculus/Mus_musculus.GRCm38.81.gtf.gz
human: ftp://ftp.ensembl.org/pub/release-81/gtf/homo_sapiens/Homo_sapiens.GRCh38.81.gtf.gz

miRNA data:
mouse: http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE31667
human: http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE60292

Known miRNA data (miRBase release 21)
mature.fa: ftp://mirbase.org/pub/mirbase/CURRENT/mature.fa.gz
hairpin.fa: ftp://mirbase.org/pub/mirbase/CURRENT/hairpin.fa.gz
miFam.dat: ftp://mirbase.org/pub/mirbase/CURRENT/miFam.dat.gz
 
#####2. Example commands for mouse data:

(1) create index file for reference genome:

    novoindex -k 12 -s 1 genome.idx genome.fa

(2) use mirpro to analyze known and novel miRNAs (rat as related species for novel miRNA prediction):

    mirpro -i SRR333597.fastq -i SRR333598.fastq -i SRR333599.fastq -i SRR333600.fastq -d . -m mature.fa -p hairpin.fa -s mmu -a ATCTCGTATGCCGTCTTCTGCTTG -f miFam.dat --novel 1 --other rno -g genome.fa --index genome.idx -t 4

(3) use mirpro_arm_switch to detect "arm switching" phenomenon:
These are the sample names and corresponding treatments:

Sample	Treatment
SRR333597	WT
SRR333598	WT
SRR333599	Tg
SRR333600	Tg

    mirpro_arm_switch -i result/arm.csv -o result/armSwitch.csv -t WT -t WT -t Tg -t Tg

#####3. Example commands for human data:

(1) create index file for reference genome:

    novoindex -k 12 -s 1 genome.idx genome.fa

(2) use mirpro to analyze known and novel miRNAs (mouse as related species for novel miRNA prediction):

    mirpro -i SRR1542714.fastq -i SRR1542715.fastq -i SRR1542716.fastq -i SRR1542717.fastq -i SRR1542718.fastq -i SRR1542719.fastq -d . -m mature.fa -p hairpin.fa -s hsa -a null -f miFam.dat --novel 1 --other mmu -g genome.fa --index genome.idx -t 6

(3) use mirpro_arm_switch to detect "arm switching" phenomenon:
These are the sample names and corresponding treatments:

Sample	Treatment
SRR1542714	control
SRR1542715	ET1
SRR1542716	control
SRR1542717	ET1
SRR1542718	control
SRR1542719	ET1

    mirpro_arm_switch -i result/arm.csv -o result/armSwitch.csv -t control -t ET1 -t control -t ET1 -t control -t ET1

**Important Notes**

(1) To skip adapter trimming process, set "-a null"; 

(2) To skip seed region check, set "--seed 0";

(3) To test whether other patterns (not poly(A) or poly(U) tail) of 5'/3' non-templated nucleotide additions include the adapter sequence, use "script/test_tail.pl". The input file can be "result/3_other_form.csv" or "result/5_other_form.csv".

    Usage: perl test_tail.pl <input_file> <adapter sequence>

For example:
perl test_tail.pl result/3_other_form.csv ATCTCGTATGCCGTCTTCTGCTTG
The result will show:
found number: 0
It means that there is no adapter sequences in other patterns of 3'non-templated nucleotide additions.

####Where to find the result files

In the output directory:
"run/" includes all the processed files.
"result/" includes include all the final results.

#####1. Most useful results

In "result/":

3_other_form.csv: sequences and counts of other patterns of non-templated nucleotide in 3' end in all samples.
5_other_form.csv: sequences and counts of other patterns of non-templated nucleotide in 5' end in all samples.
arm.csv: includes the information of dominant forms (5p or 3p; read counts with ratio > 2 and difference > 10) of mature miRNAs with two possible forms generated from the same precursors in all samples.
filter_miRNA_mature_report: records the number of mature miRNA species of studied species.
filter_miRNA_precursor_report: records the number of precursor miRNA species of studied species.
filter_miRNA_mature_XXX_report: records the number of mature miRNA species of related species "XXX".
read_cataloging.csv: the numbers of reads with different features listed in the annotation file in al samples.
mapping_report.csv: statistics of mappings in all samples.
novel_mature.fa: the predicted mature miRNA sequences in all samples in fasta format. The id is "XXX-novel-miR-YYY" ("XXX" is species name; "YYY" is a unique number same with its precursor). The description include the id (XXX) of the mature miRNA in the related species that has the same seed region with this novel mature miRNA in the format "seed:known_XXX". "seed:no" means that there is no such mature miRNA in the related species.
novel_precursor.fa: the predicted precursor miRNA sequences in all samples in fasta format. The id is "XXX-novel-mir-YYY" ("XXX" is species name; "YYY" is a unique number). The descriptions include the genome locations in the format "chromosome:strand:start:end".
novel_precursor.str: the structure of predicted precursor miRNAs in dot-bracket format. 
novel_quantifier_report.csv: the quantification report for novel miRNAs.
precursor_mature_relation.csv: the relationship between pre-miRNA and mature miRNA (5p or 3p arm in the precursor).
quantifier_report.csv: the report of quantification for known miRNA.
result_family.csv: the counts for known miRNA gene families in all samples.
result_mature.csv: the counts for known mature miRNAs in all samples.
result_precursor.csv: the counts for known pre-miRNAs (expression value = its mature miRNA count) in the sample.
result_novel_mature.csv: the counts for novel mature miRNAs in all samples.
result_novel_precursor.csv: the counts for novel pre-miRNAs (expression value = its mature miRNA count) in the sample.
statistics.csv: the counts and percentages (in total reads) of the processed reads in all samples.
variation_report.csv: the counts and percentages (in total mature miRNA reads) of the miRNA reads that have sequence differences compared with corresponding mature miRNAS in all samples.

#####2. Other detailed results for each sample

In "result/XXX/" (XXX is the name of one sample):

XXX_addition_nucleotide_pattern.csv: sequences and counts of other patterns of non-templated nucleotide in 5' and 3' end in the sample.
XXX_arm.csv: includes the information of dominant forms (5p or 3p; read counts with ratio > 2 and difference > 10) of mature miRNAs with two possible forms generated from the same precursors in the sample.
XXX_clipped_read_num: the number of reads after adapter trimming.
XXX_collapsed_read_num: the number of reads after collapsing.
XXX_each_mature_variation.csv: the the counts and percentages (in counted reads) of each miRNAs that have sequence differences compared with corresponding mature miRNAS in the sample. 
XXX_family.csv: the counts for known miRNA gene families in the sample.
XXX_mature.csv: the counts for known mature miRNAs in the sample.
XXX_pre.csv: the counts for known pre-miRNAs (expression value = its mature miRNA count) in the sample.
XXX_mature_miRNA.arf: the arf file of the mappings of the reads that are counted as mature miRNA to hairpins.
XXX_mature_miRNA_mapping.csv: the alignments of the reads that are counted as mature miRNA to hairpins."M/I/D/N" means the count of "mismatches/insertions/deletions/nucleotide N" in the alignment.
XXX_multi.csv: the reads that can be mapped to more than one pre-miRNAs after quantification.
XXX_not_aligned.fa: the clean collapsed reads that can't be mapped to the genome with the maximum penalty score = 60 in novoalign.
XXX_novel_mature.csv: the counts of novel mature miRNAs in the sample.
XXX_novel_pre.csv: the counts of novel pre-miRNAs (expression value = its mature miRNA count) in the sample.
XXX_mature_miRNA.arf: the arf file of the mappings of the reads that are counted as novel mature miRNA to hairpins.
XXX_novel_multi.csv: the reads that can be mapped to more than one novel pre-miRNAs after quantification.
XXX_novel_quantifier_report.csv: the report of quantification of novel mature miRNAs.
XXX_novel_unique.csv: the reads that can mapped to only one novel pre-miRNAs after quantification.
XXX_original_read_num: the count of raw reads in the sample.
XXX_quality_read_num: the count of the reads with good quality.
XXX_quantifier_report.csv: the report of quantification of known mature miRNAs.
XXX_survey.csv: the survey for novel miRNAs in the sample.
XXX_total_mapping.csv: the alignments of the reads that are mapped to hairpins."M/I/D/N" means the count of "mismatches/insertions/deletions/nucleotide N" in the alignment.
XXX_unique.csv: the reads that can be mapped to only one pre-miRNAs after quantification.
XXX_variation_report.csv: the counts and percentages (in counted reads) of the miRNAs that have sequence differences compared with corresponding mature miRNAS in the sample.

In "result/XXX/mapping_report/":

3-softclip_mapped.arf: the arf file of read-to-hairpin mappings with only 3' soft clip. 
5-softclip_mapped.arf: the arf file of read-to-hairpin mappings with only 5' soft clips. 
both_end_softclip_mapped.arf: the arf file of read-to-hairpin mappings with both 5' and 3' soft clips. 
deletion_mapped.arf: the arf file of read-to-hairpin mappings with only deletions. 
insertion_mapped.arf: the arf file of read-to-hairpin mappings with only insertions. 
map_report.csv: the statistics of mappings in the sample.
mismatch_mapped.arf: the arf file of read-to-hairpin mappings with only mismatches.
multi_mapped.arf: the arf file of read-to-hairpin mappings of reads with multiple mappings.
perfect_mapped.arf: the arf file of perfect read-to-hairpin mappings.

####How to use result files to perform statistical analysis

R package [DESeq](https://bioconductor.org/packages/release/bioc/html/DESeq.html) or [DESeq2](http://bioconductor.org/packages/release/bioc/html/DESeq2.html) can be used for downstream statistical analysis.

(1) For known miRNA gene differential expression analysis, please use "result/result_precursor.csv"  as the input count table;
(2) For known and novel miRNA gene differential expression analysis, please merge "result/result_precursor.csv" and "result/result_novel_precursor.csv" to one count table and use it as the input;
(3) For known miRNA gene family differential expression analysis, please use "result/result_family.csv"  as the input count table;
(4) For known mature miRNA differential expression analysis, please use "result/result_mature.csv"  as the input count table;
(5) For known and novel mature miRNA differential expression analysis, please merge "result/result_mature.csv" and "result/result_novel_mature.csv" to one count table and use it as the input;
