

# Denoising 16S rRNA amplicon sequencing reads to remove chimeras and sequencing errors

## Denoising reads:
### Software needed:
DADA2 plugin in QIMME2 2020.11 platform
Version: 0.20.0
https://docs.qiime2.org/2020.11/plugins/available/dada2/
#### Citation: 
Benjamin J Callahan, Paul J McMurdie, Michael J Rosen, Andrew W Han, Amy Jo A Johnson, and Susan P Holmes. Dada2: high-resolution sample inference from illumina amplicon data. Nature methods, 13(7):581, 2016. doi:10.1038/nmeth.3869.


### Code:
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

