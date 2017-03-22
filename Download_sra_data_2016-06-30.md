# Download public RNA-Seq data from NCBI SRA database

从NCBI获取公开的RNA-Seq数据流程：

1. 从[SRA][sra]数据库找到需要的数据。
首先了解下SRA获取号的含义，获取号DRP000704，第一位S/E/D分别表示数据来自NCBI、EMBL-EBI、DDBJ。后两位表示类型，详见下表：

    | Accession Prefix | Accession Name           |
    |------------------|--------------------------|
    | ERA              | ERA submission accession |
    | ERP              | ERA study accession      |
    | ERX              | ERA experiment accession |
    | ERR              | ERA run accession        |
    | ERS              | ERA sample accession     |
    | ERZ              | ERA analysis accession   |

2. 找到对应的RUN accession。
3. 安装[sratoolkit]，和[aspera-connect]。不安装aspera也能用，但是连接速度很慢。
    ``` sh
    wget http://download.asperasoft.com/download/sw/connect/3.6.1/aspera-connect-3.6.1.110647-linux-64.tar.gz
    tar zxf asper-commect-3.6.1.110647-linux.tar.gz
    ```
    在.bashrc添加
    ``` sh
    export ascp_home=/home/zhaorui/.aspera/connect
    export PATH=$ascp_home/bin:$PATH
    ```
4. 用prefetch下载数据：
    ``` sh
    prefetch ERR537920 -v
    ```
5. 用fastq-dump将sra数据导出为fastq文件。--split-3参数针对paired测序。
    ``` sh
    fastq-dump ERR537920 --gzip --split-3 -v
    ```
6. 得到了ERR537920.fastq.gz

[//]: #

   [sra]: <http://www.ncbi.nlm.nih.gov/sra>
   [sratoolkit]: <http://www.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=software>
   [aspera-connect]: <http://downloads.asperasoft.com/connect2/>

