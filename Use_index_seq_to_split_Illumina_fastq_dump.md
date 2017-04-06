# Use index seq to split Illumina fastq dump

### Suppose we have Illumina sequencing fastq results, but the multiplex samples had not been splited. I demultiplex the fastq as follows.

1. prepare i5/i7 index seqs, and corresponding sample names.

2. use R script to convert above info into sample~indexseq format

``` R
# Author: zhao
# Date: 17-1-19
# Purpose: split fastq using index sequences

library(openxlsx)
library(Biostrings)
library(dplyr)

i7_tbl <- read.xlsx("../docs/CTBN701-712_CTBS501-508_20170118.xlsx", sheet = 2,
                    cols = 1:2, rows = 1:13)
i5_tbl <- read.xlsx("../docs/CTBN701-712_CTBS501-508_20170118.xlsx", sheet = 2,
                    cols = 1:2, rows = 15:23)

sample_tbl <- read.xlsx("../docs/CTBN701-712_CTBS501-508_20170118.xlsx", sheet = 1,
                        cols = 1:13, rowNames = T, colNames = T)

# generate index seq <=> sample name table
i7_seq <- DNAStringSet(i7_tbl$`8base`) %>% reverseComplement()
i5_seq <- DNAStringSet(i5_tbl$`8base`)
index_seq <- DNAStringSet()
index_name <- character()
for (i in 1:length(i7_seq)) {
  for (j in 1:length(i5_seq)) {
    index_seq <- c(index_seq, xscat(i7_seq[i], i5_seq[j]))
    index_name <- c(index_name, sample_tbl[i5_tbl$i5.index[j], i7_tbl$i7.Index[i]])
  }
}
names(index_seq) <- index_name

substrRight <- function(x, n){
  substr(x, nchar(x)-n+1, nchar(x))
}

# read fastq index seq
fastq_file <- "../data/110k.fastq"
seq <- readDNAStringSet(fastq_file, format = "fastq")
fastq_index <- names(seq) %>% substrRight(n = 16)

write.table(data.frame(Seq=index_seq, Name =index_name), file = "index_seq.txt", sep = "\t", col.names = T, row.names = F, quote = F)

```

* i7 table

| i7 Index | 8base    |
|----------|----------|
| N701     | TAAGGCGA |
| N702     | CGTACTAG |
| N703     | AGGCAGAA |
| N704     | TCCTGAGC |
| N705     | GGACTCCT |
| N706     | TAGGCATG |
| N707     | CTCTCTAC |
| N708     | CAGAGAGG |
| N709     | GCTACGCT |
| N710     | CGAGGCTG |
| N711     | AAGAGGCA |
| N712     | GTAGAGGA |

* i5 table

| i5 index | 8base    |
|----------|----------|
| S501     | TAGATCGC |
| S502     | CTCTCTAT |
| S503     | TATCCTCT |
| S504     | AGAGTAGA |
| S505     | GTAAGGAG |
| S506     | ACTGCATA |
| S507     | AAGGAGTA |
| S508     | CTAAGCCT |

* sample_tbl 

|      | N701  | N702  | N703  | N704  | N705  | N706  | N707  | N708  | N709  | N710  | N711  | N712  |
|------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| S501 | 9661  | 9696  | 9079  | 11674 | 11760 | 12854 | 446   | 1299  | 2546  | 2996  | 3467  | 3517  |
| S502 | 4265  | 4368  | 4833  | 5939  | 7372  | 7492  | 8016  | 8501  | 9499  | 9930  | 11339 | 11455 |
| S503 | 8689  | 11358 | 11606 | 12245 | 13792 | 14802 | 517   | 3433  | 4311  | 8093  | 8195  | 7984  |
| S504 | 9312  | 9572  | 8900  | 8990  | 9516  | 10143 | 10224 | 10279 | 11385 | 11698 | 11750 | 11837 |
| S505 | 11967 | 13343 | 13379 | 13484 | 8351  | 8428  | 8436  | 8519  | 8521  | 13846 | 13993 | 6827  |
| S506 | 7657  | 7659  | 8150  | 8156  | 8239  | 8241  | 8327  | 8334  | 8342  | 8345  | 12248 | 11945 |
| S507 | 11424 | 3526  | 2961  | 958   | 899   | 11658 | 12389 | 11714 | 11668 | 4170  |       |       |
| S508 | pku1  | pku2  | pku3  | pku4  | pku5  | pku6  | pku7  | pku8  | pku9  | pku10 |       |       |

* index_seq.txt

|Seq             |Name |
|----------------|-----|
|TCGCCTTATAGATCGC|9661 |
|TCGCCTTACTCTCTAT|4265 |
|TCGCCTTATATCCTCT|8689 |
|TCGCCTTAAGAGTAGA|9312 |
|TCGCCTTAGTAAGGAG|11967|
|TCGCCTTAACTGCATA|7657 |
|TCGCCTTAAAGGAGTA|11424|
|TCGCCTTACTAAGCCT|pku1 |
|...             |...  |

3. split fastq files using Perl script

``` Perl
#!/usr/bin/env perl
# Author: Zhao
# Date: 2017 1 19
# Purpose: split fastq file using index seq info

use Bio::SeqIO::fastq;
use 5.014;
use Data::Dump qw/dump/;
use Smart::Comments;
use Data::Table;
use Bio::Seq::Quality;

my $index_seq = Data::Table::fromTSV("index_seq.txt");
my (%i2s, %fastq_out);
foreach my $i (0 .. $index_seq->lastRow) {
  my $name = $index_seq->elm($i, 'Name');
  my $seq = $index_seq->elm($i, 'Seq');
  $i2s{$seq} = $name;
  $fastq_out{$name} = Bio::SeqIO->new(-format    => 'fastq',
                                      -file      => ">r1/$name.fq");
}

  # grabs the FASTQ parser, specifies the Illumina variant
  my $in = Bio::SeqIO->new(-format    => 'fastq',
                           -file      => '../../data/lane1_combined_clean_R1.fastq')                                                                                                                               ;

  # $seq is a Bio::Seq::Quality object
  while (my $seq = $in->next_seq) {
    my $desc = $seq->desc;
    my $index = right($desc, 16);
    my $sample = match_sample($index);
    $fastq_out{$sample}->write_seq($seq);  # convert Illumina 1.3 to Sanger format

  }

sub match_sample {
  my ($s) = @_;
  if (defined $i2s{$s} ) {
    return $i2s{$s};
  } else {
    foreach my $i (0 .. $index_seq->lastRow) {
      if (Ns($s, 'N') == hd($s, $index_seq->elm($i, 'Seq')) ) {
        return $index_seq->elm($i, 'Name');
      }
    }
  }
  return 'NA';
}

sub right {
  my ($s, $n) = @_;
  return substr $s, -$n, $n;
}

sub hd { # hamming distance http://www.perlmonks.org/?node_id=500235
  return ($_[0] ^ $_[1]) =~ tr/\001-\255//;
}

sub Ns {
  my ($x, $n) = @_;
  $x =~ s/[^$n]//g;
  return length($x);
}

```

The splited fastq files
will be save as <sample_name>.fq