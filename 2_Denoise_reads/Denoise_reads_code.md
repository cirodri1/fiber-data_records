

# Denoising 16S rRNA amplicon sequencing reads to remove chimeras and sequencing errors

## Denoising reads:
### Software needed:
DADA2 plugin in QIMME2 2020.11 platform
Version: 0.20.0
https://docs.qiime2.org/2020.11/plugins/available/dada2/
#### Citation: 
Benjamin J Callahan, Paul J McMurdie, Michael J Rosen, Andrew W Han, Amy Jo A Johnson, and Susan P Holmes. Dada2: high-resolution sample inference from illumina amplicon data. Nature methods, 13(7):581, 2016. doi:10.1038/nmeth.3869.


### Example Code:
#### For paired-end data (PE):
```
qiime dada2 denoise-paired \
  --i-demultiplexed-seqs 2_primer-trimmed_imported_paired-end_data.qza \
  --p-trunc-len-f 270 \ #truncation parameter value is chosen based on quality of reads
  --p-trunc-len-r 188 \ #truncation parameter value is chosen based on quality of reads
  --p-trim-left-f 0 \
  --p-trim-left-r 0 \
  --o-table 3_feature-table-dada2.qza \
  --o-representative-sequences 3_feature-rep-seqs-dada2.qza \
  --o-denoising-stats 3_stats-dada2.qza \
  --p-n-threads 0 \
  --verbose \
  &> 3_denoise.log

```

#### For single data (SE):
```
qiime dada2 denoise-single \
  --i-demultiplexed-seqs 2_primer-trimmed_imported_demux-single-end.qza \
  --p-trunc-len 270 \ #truncation parameter value is chosen based on quality of reads
  --p-trim-left 0 \
  --o-table 3_table-dada2.qza \
  --o-representative-sequences 3_rep-seqs-dada2.qza \
  --o-denoising-stats 3_stats-dada2.qza \
  --p-n-threads 0 \
  --verbose \
 &> 3_denoise.log

```
### Table with parameters and code per study:

| Study Name | Database where raw reads are located | Accession Number | Number of samples | Illumina platform used | Single or paired end data | Primers used | Denoise DADA2 paremeters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Baxter_2019_V4 | NCBI Sequence Read Archive | SRP128128 | 1205 | Illumina MiSeq | paired | GTGCCAGCMGCCGCGGTAA...ATTAGAWACCCBDGTAGTCC | `qiime dada2 denoise-paired --i-demultiplexed-seqs 1_imported_paired-end_data.qza --p-trunc-len-f 220 --p-trunc-len-r 200 --p-trim-left-f 0 --p-trim-left-r 0` |
Dahl_2016_V1V2 | NCBI Sequence Read Archive | SRP403421 | 212 | Illumina MiSeq | paired | AGMGTTYGATYMTGGCTCAG...ACTCCTACGGGAGGCAGC | `qiime dada2 denoise-paired  --i-demultiplexed-seqs 1_imported_paired-end_data.qza  --p-trunc-len-f 210 --p-trunc-len-r 205  --p-trim-left-f 0  --p-trim-left-r 0` |
Deehan_2020_V5V6 | NCBI Sequence Read Archive | SRP219296 | 200 | Illumina MiSeq | paired | RGGATTAGATACCC...AGGTGNTGCATGGYYGTCG | `qiime dada2 denoise-paired  --i-demultiplexed-seqs 2_primer-trimmed_imported_paired-end_data.qza  --p-trunc-len-f 212 --p-trunc-len-r 185  --p-trim-left-f 0 --p-trim-left-r 0` |
Healey_2018_V3V4 | NCBI Sequence Read Archive | SRP120250 | 134 | Illumina MiSeq | paired | CGGGAGGCAGCAG...ATTAGAWACCCBDGTA | `qiime dada2 denoise-paired --i-demultiplexed-seqs 1_imported_paired-end_data.qza --p-trunc-len-f 250 --p-trunc-len-r 230 --p-trim-left-f 0 --p-trim-left-r 0` |
Hooda_2012_V4V6 | NCBI Sequence Read Archive | SRP403421 | 28 | 454 titanium technology | single | GTGCCAGCMGCNGCGG...GGGTTNCGNTCGTTG | `qiime dada2 denoise-pyro --i-demultiplexed-seqs 3_imported_single-end_data.qza --p-trunc-len 303 --p-trim-left 0 `|
Kovatcheva_2015_V1V2 | NCBI Sequence Read Archive | SRP062889 | 60 | 454/Roche pyrosequencing | single | TGCTGCCTCCCGTAGGAGT...CTGAGCCAKGATCAAACTCT | `qiime dada2 denoise-single --i-demultiplexed-seqs 2_primer-trimmed_imported_single-end_data.qza --p-trunc-len 191 --p-trim-left 0` |
Liu_2017_V4 | European Nucleotide Archive | PRJEB15149 | 132 | Ion Torrent | single | GTGTGCCAGCMGCCGCGGTAA...ATTAGAWACCCBDGTAGTCCGG | `qiime dada2 denoise-pyro --i-demultiplexed-seqs 2_primer-trimmed_imported_single-end_data.qza --p-trunc-len 232 --p-trim-left 0` |
Morales_2016_V3V4 | NCBI Sequence Read Archive | SRP403421 | 82 | Illumina MiSeq | paired | CCTACGGGNGGCWGCAG...GGATTAGATACCCBDGTAGTC | `qiime dada2 denoise-paired --i-demultiplexed-seqs 2_primer-trimmed_imported_paired-end_data.qza --p-trunc-len-f 270 --p-trunc-len-r 188 --p-trim-left-f 0 --p-trim-left-r 0` |
Rasmussen_2017_V1V3 | NCBI Sequence Read Archive | SRP106361 | 82 | Roche 454 GS FLX pyrosequencing | single | GAGTTTGATCNTGGCTCAG...CAGCMGCCGCNGTAANAC | `qiime dada2 denoise-pyro --i-demultiplexed-seqs 2_primer-trimmed_imported_single-end_data.qza --p-trunc-len 175 --p-trim-left 0` |
Tap_2015_V3V4 | European Nucleotide Archive | ERP000220 | 76 | 454/Roche pyrosequencing | single | TACGGRAGGCAGCAG...ATTAGATACCCTGGTAGTCC | `qiime dada2 denoise-pyro --i-demultiplexed-seqs 2_primer-trimmed_imported_single-end_data.qza --p-trunc-len 224 --p-trim-left 0` |
Vandeputte_2017_V4 | NCBI Sequence Read Archive | SRP067761 | 157 | Illumina MiSeq | paired | GTGCCAGCMGCCGCGGTAA...ATTAGAWACCCBDGTAGTCC | `qiime dada2 denoise-paired --i-demultiplexed-seqs 1_imported_paired-end_data.qza --p-trunc-len-f 211 --p-trunc-len-r 108 --p-trim-left-f 0 --p-trim-left-r 0` |


