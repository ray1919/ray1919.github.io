### [jannovar](https://jannovar.readthedocs.io/en/master/) 2020-3-9
 * 可以注释gnomad、cosmic、clinvar的vcf，可以设定3-prime-shifting规则，这个是肿瘤NGS生信质评的要求之一。

### [bamdst](https://github.com/shiquan/bamdst) 2020-7-17
 * 统计bam的比对结果，能计算0x 4x 10x 30x 100x的coverage。

### [NanoCLUST](https://github.com/genomicsITER/NanoCLUST/) 2020-6-15
 * 分析Nanopore的细菌16S测序结果，根据NCBI BLAST的16S数据库，给出各个菌的丰度。
 * 运行时第一次需要用conda创建虚拟运行环境，需要网络支持。后续分析本地运行。
 * 测试数据需要16G内存的系统。
 * 用[Nextflow tower](https://tower.nf/)可以在线监视运行状态。

### [Nirvana](https://github.com/Illumina/Nirvana) 2020-8-26
 * 对临床遗传病VCF文件进行注释，得到注释的JSON文件。
 * 下载数据库比较慢，需要多次尝试（dotnet $DOWNLOADER_BIN --ga $GENOME_ASSEMBLY --out $DATA_DIR）。

### [mirpro user manual](User_manual.md)

### update: 2017-07-27
 * [Useful software](Useful_software.md)

### update: 2017-05-03
 * [Redis installation in Ubuntu](http://blog.fens.me/linux-redis-install/)

### updated: 2017-04-06
 * [Rapid and sensitive classification of metagenomic sequences using centrifuge](Rapid_and_sensitive_classification_of_metagenomic_sequences_using_centrifuge.md)
 * [Use index seq to split Illumina fastq dump](Use_index_seq_to_split_Illumina_fastq_dump.md)

### updated: 2017-03-30
 * [Upgrade Ubuntu PHP5 to PHP7.md](Upgrade_Ubuntu_PHP5_to_PHP7.md)

### updated: 2017-03-29
 * [Trouble-shooting Black screen after Ubuntu upgrade.md]([Trouble-shooting]Black_screen_after_Ubuntu_upgrade.md)

### updated: 2017-03-22
 * [Use AMOScmp to assemble V.para WGS data](Use_AMOScmp_to_assemble_V.para_WGS_data.md)
 * [Convert GTF to BED12](Convert_GTF_to_BED12.md)
 * [Download sra data](Download_sra_data_2016-06-30.md)
