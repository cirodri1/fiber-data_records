
# Removal of low-quality reads and primers from 16S rRNA amplicon sequencing data

## Removal of low quality reads:
### Software needed:
Fastp program
Version: 0.20.0
https://github.com/OpenGene/fastp 
#### Citation: 
Shifu Chen, Yanqing Zhou, Yaru Chen, Jia Gu; fastp: an ultra-fast all-in-one FASTQ preprocessor, Bioinformatics, Volume 34, Issue 17, 1 September 2018, Pages i884–i890, https://doi.org/10.1093/bioinformatics/bty560 

#### Installation of Fastp:
To install it: conda install -c bioconda fastp
To check installation: fastp -h 

### Code:
#### For paired-end data (PE):
```
fastp -i READ1.fastq -I READ2.fastq -o READ1_clean.fastq -O READ2_clean.fastq --average_qual 30 --length_required 100 --detect_adapter_for_pe --correction
```

#### For single data (SE):
```
fastp -i READ1.fastq -o READ1_clean.fastq --average_qual 30 --length_required 100
```

#### Explanation:
Adapter trimming is enabled by default
-i : forward reads

-I : reverse reads

-o : output name for forward reads

-O : output name for reverse reads

--average_qual 30 : filtering reads by 30 average quality score, if one read’s average quality score is less than 30, then this read/pair is discarded

--length_required 100 : reads shorter than length_required will be discarded
*Note: 100 was chosen because studies usually removed things shorter than 150, 250 or 350, so to be lenient we left a 100 length minimum.

--detect_adapter_for_pe : the adapter sequence detection is enabled for paired data to clean up data from adapter contamination
For SE data, the adapters are evaluated by analyzing the tails of first ~1M reads. This evaluation may be inacurrate, and you can specify the adapter sequence by -a or --adapter_sequence option. If adapter sequence is specified, the auto detection for SE data will be disabled.

--correction : enable base correction in overlapped regions, which try to find an overlap of each pair of reads. If a proper overlap is found, it can correct mismatched base pairs in overlapped regions of paired end reads, if one base is with high quality while the other is with ultra low quality. If a base is corrected, the quality of its paired base will be assigned to it so that they will share the same quality.  


## Removal of primers:
### Software needed:
Cutadapt plugin in QIMME2 2020.11 platform
https://docs.qiime2.org/2021.11/plugins/available/cutadapt/
#### Citation: 
Marcel Martin. Cutadapt removes adapter sequences from high-throughput sequencing reads. EMBnet. journal, 17(1):pp–10, 2011. doi:10.14806/ej.17.1.200.

### Code:
#### Example code:
```
qiime cutadapt trim-paired \
  --i-demultiplexed-sequences 1_imported_paired-end_data.qza \
  --p-adapter-f 'CCTACGGGNGGCWGCAG...GGATTAGATACCCBDGTAGTC' \
  --p-adapter-r 'GACTACHVGGGTATCTAATCC...CTGCWGCCNCCCGTAGG' \
  --p-cores 4 \
  --p-discard-untrimmed \ # only use when primers are present in the majority of the reads
  --o-trimmed-sequences 2_primer-trimmed_imported_paired-end_data.qza \
  --verbose \
  &> 2_primer_trimming.log
```
