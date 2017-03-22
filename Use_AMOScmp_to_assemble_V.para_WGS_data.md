# Use AMOScmp to assemble V.para WGS data

1. retrive test SRA data from NCBI, this data contain 0.98G base PE150 Illumina HiSeq2500 seq data.
    ``` bash
    prefetch -v SRR4035056
    fastq-dump SRR4035056 --gzip --split-3 -v
    ```
2. split fastq files to FASTA & QUAL files, ~13m.
    ``` bash
    read_fastq -e base_33 -i SRR4035056_1.fq | write_454 -o SRR4035056_1.fa -q SRR4035056_1.qual -x
    read_fastq -e base_33 -i SRR4035056_2.fq | write_454 -o SRR4035056_2.fa -q SRR4035056_2.qual -x
    cat SRR4035056_?.fa > SRR4035056.fa
    cat SRR4035056_?.qual > SRR4035056.qual
    ```
3. run toAmos, ~13m
    ``` bash
    toAmos -o V.para.afg -s SRR4035056.fa -q SRR4035056.qual
    ```
4. download reference genome seq as V.para.1con
5. run AMOScmp, ~50m
    ``` bash
    AMOScmp V.para
    ```
6. visualize contigs & scaffolds
    ```bash
    hawkeye V.para.bnk
    ```
7. calculate contig coverage on the reference genome
    ``` r
    library(stringr)
    library(GenomicRanges)
    library(dplyr)

    layout <- read.table("V.para.layout.header", as.is = T, sep="\t", header = F)
    head(layout)

    genome_len <- c(3288558, 1877212)
    names(genome_len) <- c("NC_004603.1", "NC_004605.1")
    rngs <- RangesList()
    for (r in unique(layout$V3)) {
      starts <- integer()
      widths <- integer()
      poss <- layout$V4[layout$V3 == r]
      for (i in 1:length(poss)) {
        pi <- str_extract_all(string = poss[i], pattern = "[-]*\\d+", simplify = T) %>% as.integer() * c(1,-1)
        starts <- c(starts, pi[1])
        widths <- c(widths, pi[2]-pi[1]+1)
      }
      rngs[[r]] <- IRanges(start = starts, width = widths)
      c <- coverage(rngs[[r]])
      print(paste(r, "coverage", (1 - length(c[c==0]) / genome_len[r])*100, "%"))
    }
    ```
    ```
    # results
    NC_004603.1 coverage 89.32%
    NC_004605.1 coverage 90.39%
    ```
8. check coverage of each gene in reference genome
    ``` r
    # gene coverage
    # read annotated gene feature table
    feature_file <- "../features/feature_table.txt"
    feature_lines <- readLines(feature_file)
    gene_started <- F
    start <- 0
    end <- 0
    gene <- ""
    locus_tag <- ""
    gene_id <- ""
    feature_table <- data.frame()
    for (i in 1:length(feature_lines)) { # 
      if (grepl(pattern = ">Feature", x = feature_lines[i])) {
        acc <- str_extract(feature_lines[i], pattern = "NC_[0-9.]+")
      }
      if (grepl(pattern = "\\d+\t\\d+\tgene", x = feature_lines[i])) {
        feature_table <- rbind(feature_table, data.frame(acc, start, end, gene, locus_tag, gene_id))
        start <- 0
        end <- 0
        gene <- ""
        locus_tag <- ""
        gene_id <- ""
        start_end <- str_extract_all(string = feature_lines[i], pattern = "\\d+", simplify = T) %>% as.integer()
        start <- min(start_end)
        end <- max(start_end)
        gene_started <- T
      }
      if (gene_started) {
        if (grepl(pattern = "\t\t\tgene\t", x = feature_lines[i])) {
          gene <- str_extract(string = feature_lines[i], pattern = "\\w+$")
        }
        if (grepl(pattern = "\t\t\tlocus_tag\t", x = feature_lines[i])) {
          locus_tag <- str_extract(string = feature_lines[i], pattern = "\\w+$")
        }
        if (grepl(pattern = "\t\t\tdb_xref\tGeneID:", x = feature_lines[i])) {
          gene_id <- str_extract(string = feature_lines[i], pattern = "\\d+$")
        }
      }
    }
    feature_table$cov <- NA
    for (i in 2:nrow(feature_table) ) {
      gene_range <- IRanges(start = feature_table$start[i],end = feature_table$end[i])
      r <- feature_table$acc[i]
      ints <- intersect(gene_range, rngs[[r]])
      if (length(ints) >0) {
        feature_table$cov[i] <- sum(width(ints)) / width(gene_range)
      } else {
        feature_table$cov[i] <- 0
      }
    }
    table(feature_table$cov==0)
    table(feature_table$cov==1)
    ```
    | 0    | 0~100 | 100   |
    | ---- | ----- | ----- |
    | 389  | 235   | 4366  |
    | 7.8% | 4.7%  | 87.5% |

### Tools used to make this:
 * [AMOS](http://amos.sourceforge.net/wiki/index.php/AMOS) for freestanding genome assemblers
 * [Biopieces](http://maasha.github.io/biopieces/) for read_fastq & write_454 tools
 * [markdown-it](https://jbt.github.io/markdown-editor/#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) for Markdown parsing

### Related file formats
 * [prefix.contig](http://amos.sourceforge.net/wiki/index.php/Bank2contig) Each contig is preceded by a header starting with ##, followed by the contig identifier, number of reads aligned to it, and the number of bases in the padded consensus. If generated by TIGR Assembler, these records also contain an 8-digit checksum, however most converters generate a blank checksum (it's not used by any code anyway).
