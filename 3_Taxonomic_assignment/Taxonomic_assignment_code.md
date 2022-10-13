
# Assigning taxonmy to Operational Taxonomic Units (OTUs) from 16S rRNA amplicon sequencing data

## Assigning taxonomy:
### Software needed:
qiime feature-classifier plugin in QIMME2 2020.11 platform
https://docs.qiime2.org/2020.2/plugins/available/feature-classifier/extract-reads/



### Step 1: 
Transfer the following files obtained from denoising step in DADA2 to your working directory for taxonomy assignment:
- 3_feature-rep-seqs-dada2.qza: Contains the actual DNA sequences assigned to each feature count (OTU) that appear in the samples (.qza).
- 3_feature-table-dada2.qza: Contains the feature counts (OTUs) per sample statistics, number of feature counts per sample, frequency of features per sample, and the feature detail assigning it a number per feature count and the number of times that feature count appears
metadata_final.txt

### Step 2: 
Transfer the imported SILVA database that contains the reference sequences and the corresponding taxonomic classifications:
- silva_132_99_16S.qza
- ref-SILVA-132_taxonomy.qza
The files above were created as follows:
### Example code:
```
qiime tools import \
  --type 'FeatureData[Sequence]' \
  --input-path silva_132_99_16S.fna \
  --output-path silva_132_99_16S.qza
```

```
qiime tools import \
  --type 'FeatureData[Taxonomy]' \
  --input-format HeaderlessTSVTaxonomyFormat \
  --input-path taxonomy_all_levels_silva_132_99_16S.txt \
  --output-path ref-SILVA-132_taxonomy.qza
```


### Step 3: 
Extract reads - Extract the region of reference reads that correspond to the region that matches the dataset that you are working with according to the primer set and cutting parameters:
https://docs.qiime2.org/2020.2/plugins/available/feature-classifier/extract-reads/ 
### Example code for Morales_2016_V3V4: 
341F (5′- CCTACGGGNGGCWGCAG -3′)
785R (5’- GACTACHVGGGTATCTAATCC -3′)
```
qiime feature-classifier extract-reads \
  --i-sequences silva_132_99_16S.qza \
  --p-f-primer CCTACGGGNGGCWGCAG \
  --p-r-primer GACTACHVGGGTATCTAATCC \
  --p-trunc-len 215 \
  --o-reads forward_ref-seq_silva_132_99_16S.qza \
  --p-n-jobs 4 \
  --verbose
```
* Note: p-trunc-len 215 added because forward reads were cut at that length

### Step 4:
Train the classifier - Now train a Naive Bayes classifier as follows, using the reference reads and taxonomy that we just created.
### Example code:
```
qiime feature-classifier fit-classifier-naive-bayes \
  --i-reference-reads forward_ref-seq_silva_132_99_16S.qza \
  --i-reference-taxonomy ref-SILVA-132_taxonomy.qza \
  --o-classifier forward_trained_classifier-silva_132_99_16S.qza \
  --verbose
```

### Step 5: 
Test the classifier - verify that the SILVA classifier works by classifying the representative forward reads from your dataset by taxon: https://docs.qiime2.org/2020.11/plugins/available/feature-classifier/classify-sklearn/ 
### Example code:
```
qiime feature-classifier classify-sklearn \
  --i-classifier forward_trained_classifier-silva_132_99_16S.qza \
  --i-reads 3_feature-rep-seqs-dada2.qza \
  --o-classification 4_forward_taxonomy-result.qza \
  --verbose
```

### Step 6:
Filter out mitochondria and chloroplast from sequences and feature table (taxonomy file is not needed to be filtered since we will not be using the SILVA taxonomy results).
https://docs.qiime2.org/2020.11/tutorials/filtering/ 
https://docs.qiime2.org/2020.11/plugins/available/taxa/filter-seqs/?highlight=qiime%20taxa%20filter%20seqs 
### Example code- filter sequences:
```
qiime taxa filter-seqs \
  --i-sequences 3_feature-rep-seqs-dada2.qza \
  --i-taxonomy 4_forward_taxonomy-result.qza \
  --p-exclude mitochondria,chloroplast,archaea \
  --o-filtered-sequences 5_forward_rep_seqs_taxonomy_filtered.qza \
  --verbose
```
* Note: --p-exclude mitochondria,chloroplast,archaea excludes all features that contain either mitochondria or chloroplast in their  taxonomic annotation (Upper or lower case does not matter)

### Example code- filter OTU table:
```
qiime taxa filter-table \
  --i-table 3_feature-table-dada2.qza \
  --i-taxonomy 4_forward_taxonomy-result.qza \
  --p-exclude mitochondria,chloroplast,archaea \
  --o-filtered-table 5_forward_feature-table_taxonomy_filtered.qza \
  --verbose
```

### Step 7:
Transfer the imported GTDB database that contains the reference sequences and the corresponding taxonomic classifications:
- bac120_ssu_reps_r95_gtbd.qza
- ref-bac120_taxonomy.qza
The files above were created as follows:
### Example code:
```
qiime tools import \
  --type 'FeatureData[Sequence]' \
  --input-path bac120_ssu_reps_r95.fna \
  --output-path bac120_ssu_reps_r95_gtbd.qza
```

```
qiime tools import \
  --type 'FeatureData[Taxonomy]' \
  --input-format HeaderlessTSVTaxonomyFormat \
  --input-path bac120_taxonomy.tsv \
  --output-path ref-bac120_taxonomy.qza
```

### Step 8:
Extract reads - Extract the region of reference reads that correspond to the region that matches the dataset that you are working with according to the primer set and cutting parameters: (~ 2 min)
https://docs.qiime2.org/2020.2/plugins/available/feature-classifier/extract-reads/ 
### Example code for Morales_2016_V3V4: 
341F (5′- CCTACGGGNGGCWGCAG -3′)
785R (5’- GACTACHVGGGTATCTAATCC -3′)
```
qiime feature-classifier extract-reads \
  --i-sequences bac120_ssu_reps_r95_gtbd.qza \
  --p-f-primer CCTACGGGNGGCWGCAG \
  --p-r-primer GACTACHVGGGTATCTAATCC \
  --p-trunc-len 215 \
  --o-reads forward_ref-seqs_bac120_ssu_reps_r95_gtbd.qza \
  --p-n-jobs 4 \
  --verbose
```
* Note: p-trunc-len 215 added because forward reads were cut at that length

### Step 9:
Train the classifier - Now train a Naive Bayes classifier as follows, using the reference reads and taxonomy that we just created:
### Example code:
```
qiime feature-classifier fit-classifier-naive-bayes \
  --i-reference-reads forward_ref-seqs_bac120_ssu_reps_r95_gtbd.qza \
  --i-reference-taxonomy ref-bac120_taxonomy.qza \
  --o-classifier forward_trained_classifier-bac120_ssu_reps_r95_gtbd.qza \
  --verbose
```

### Step 10:
Test the classifier - verify that the GTDB classifier works by classifying the representative forward reads that were filtered from chloroplast and mitochondria by taxon:
 https://docs.qiime2.org/2020.11/plugins/available/feature-classifier/classify-sklearn/ 
### Example code:
```
qiime feature-classifier classify-sklearn \
  --i-classifier forward_trained_classifier-bac120_ssu_reps_r95_gtbd.qza \
  --i-reads 5_forward_rep_seqs_taxonomy_filtered.qza \
  --o-classification 6_forward_taxonomy-result_GTDB.qza \
  --verbose
```
### Step 11:
Filter files to only include reads that were classified at least to the phylum level from sequences, feature table (that were previously filtered using SILVA), and remake taxonomy file: https://docs.qiime2.org/2020.11/tutorials/filtering/ 
https://docs.qiime2.org/2020.11/plugins/available/taxa/filter-seqs/?highlight=qiime%20taxa%20filter%20seqs 

### Example code- filter sequences:
```
qiime taxa filter-seqs \
  --i-sequences 5_forward_rep_seqs_taxonomy_filtered.qza \
  --i-taxonomy 6_forward_taxonomy-result_GTDB.qza \
  --p-include p__ \
  --o-filtered-sequences 7_forward_rep_seqs_taxonomy_filtered_phylum.qza \
  --verbose
```
* Note:  
--p-include can be used to retain only features that were at least annotated to the phylum level.

### Example code- filter OTU table:
```
qiime taxa filter-table \
  --i-table 5_forward_feature-table_taxonomy_filtered.qza \
  --i-taxonomy 6_forward_taxonomy-result_GTDB.qza \
  --p-include p__ \
  --o-filtered-table 7_forward_feature-table_taxonomy_filtered_phylum.qza \
  --verbose
```
### Example code- filter taxonomy file (by remaking it, basically):
```
qiime feature-classifier classify-sklearn \
  --i-classifier forward_trained_classifier-bac120_ssu_reps_r95_gtbd.qza \
  --i-reads 7_forward_rep_seqs_taxonomy_filtered_phylum.qza \
  --o-classification 7_forward_taxonomy_filtered_phylum.qza \
  --verbose
```

### Step 12:
Extract Final taxonomy classification:

### Code to extract feature table file:
```
qiime tools extract \
--input-path 7_feature-table_taxonomy_filtered_phylum.qza \
--output-path 7_feature-table_taxonomy_filtered_phylum_data
```
### Code to convert extracted feature table file from biom to .tsv file:
```
biom convert -i feature-table.biom -o filtered_phylum_feature-table.tsv --to-tsv
```
### Code to extract taxa file:
```
qiime tools extract \
--input-path 7_forward_taxonomy_filtered_phylum.qza \
--output-path 7_forward_taxonomy_filtered_phylum_data

```
### Code to merge your feature table with taxonomy, then convert to a tsv:
* Note: You need to manually manipulate the file header in the taxonomy file change the taxonomy header to start with #OTUID and delete top row “Created from biom file”
```
biom add-metadata -i feature-table.biom -o table_filtered_with_taxonomy_filtered.biom --observation-metadata-fp taxonomy_GTDB_filtered_modified_header.tsv --sc-separated taxonomy
```

```
biom convert -i table_filtered_with_taxonomy_filtered.biom -o table_filtered_with_taxonomy_filtered.tsv --to-tsv --header-key taxonomy

```

* Note: all the above steps were done for each dataset and for both the forward/single reads and the paired reads when available.
