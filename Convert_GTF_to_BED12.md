Convert GTF file into BED12 format
==================================

Most popular tool gtf2bed from [BEDOPS ](https://github.com/bedops/bedops)only
support 6-column BED format, RSeQC tool ask for BED12 format.

After several googling,

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
git clone https://github.com/dasmoth/gtf2bed.git
cd gtf2bed
wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
chmod u+x lein
./lein deps
./lein uberjar

# convert
java -jar target/gtf2bed-0.0.1-SNAPSHOT-standalone.jar gencode.gtf > gencode.bed
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
