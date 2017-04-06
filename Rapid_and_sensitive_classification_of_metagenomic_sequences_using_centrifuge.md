# Rapid and sensitive classification of metagenomic sequences using centrifuge

### 快速分类宏基因组测序结果。不同于16s基因组测序，可能会包含除细菌外其他类别生物的序列，如真菌、植物动物等。centrifuge可以快速检测测序结果中的物种信息，比其知名的[Kraken](https://ccb.jhu.edu/software/kraken/),它对内存的要求较低，适用范围广。自带下载命令，方便自己构建特定物种的比对库。

1. 这里我们查找特定物种的测序结果，所以先找到对应物种的NCBI分类学编号，如下表：

| Org                                     | Short | Tax id |
|-----------------------------------------|-------|--------|
| 梅毒螺旋体 (Treponema pallidum, Tp)       | Tp    | 243276 |
| 单纯泡疹病毒 (Herpessimplexvirus, HSV1)   | HSV1  | 10298  |
| 单纯泡疹病毒 (Herpessimplexvirus, HSV2)   | HSV2  | 10310  |
| 杜克雷嗜血杆菌 (Haemophilusducreyi, HD)   | HD    | 730    |
| 念珠菌 (Candida Albicans)                | Caalb | 237561 |

2. 构建参考基因组数据库

``` SH
centrifuge-download -o thefour taxonomy
centrifuge-download -o thefour -m -t 243276,10298,10310,730,237561 -d bacteria,viral,fungi refseq > seqid2taxid.map
cat */*.fna > input-sequences.fna
centrifuge-build -p 8 --conversion-table seqid2taxid.map \
                 --taxonomy-tree nodes.dmp --name-table names.dmp \
                 input-sequences.fna thefour

```

3. 运行centrifuge, 得到结果和报告。并将结果转换为Kraken的报告格式，方便导入到Pavian工具中可视化。

``` SH
CENTRIFUGE_INDEXES=/home/zhaorui/ct208/tool/centrifuge/centrifuge/indices/thefour

reads1=(r1/*R1*)
reads2=("${reads1[@]/_R1./_R2.}")
reads2=("${reads2[@]/r1/r2}")
for ((i=0; i<=${#reads1[@]}-1; i++ )); do
    sample="${reads1[$i]%%.*}"
    sample="${sample%_*}"
    sample="${sample#r1/}"
    echo $sample
    centrifuge -x thefour -t -p 8 -1 ${reads1[$i]} -2 ${reads2[$i]} -S cresult/${sample}.cresult --report-file creport/${sample}.creport
    ../../centrifuge-kreport -x thefour --only-unique cresult/${sample}.cresult > kreport/${sample}.kreport
done

```

4. 完成后打开[Pavian](https://ccb.jhu.edu/software/centrifuge/Pavian)的界面，输入得到的kreport文件。即可以将我们的结果可视化显示。

| NAME           | TAX NAME                                        | 10143 | 10224 | 10279 | 11339  | 11358  |
|----------------|-------------------------------------------------|-------|-------|-------|--------|--------|
| 念珠菌         | Candida albicans SC5314                         | 6     | 2183  | 119   | 56     | 4784   |
| 念珠菌         | Candida albicans                                | .     | 5     | .     | .      | 11     |
| 杜克雷嗜血杆菌 | [Haemophilus] ducreyi                           | 4341  | 80034 | 34282 | 134716 | 131988 |
| 单纯泡疹病毒1  | Human alphaherpesvirus 1                        | .     | .     | .     | .      | .      |
| 单纯泡疹病毒2  | Human alphaherpesvirus 2                        | .     | .     | .     | .      | .      |
| 梅毒螺旋体     | Treponema pallidum subsp. pallidum str. Nichols | 7697  | 22020 | 53653 | 5188   | 2010   |

