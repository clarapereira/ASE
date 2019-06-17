# Files needed for the analysis

1. RNA-seq data for F1-cross, fastq file (single end or paired end)

> For example, `rep_i.fastq` for each of i={1..N} replicates. 

2. Reference genome

> For example, `GRCm38_68.fa`.
```
wget ftp://ftp-mouse.sanger.ac.uk/ref/GRCm38_68.fa
```

3. gtf annotation for the reference genome

> For example, `Mus_musculus.GRCm38.68.gtf.gz` for corresponding reference genome version.
```
wget ftp://ftp.ensembl.org/pub/release-68/gtf/mus_musculus/Mus_musculus.GRCm38.68.gtf.gz
gunzip Mus_musculus.GRCm38.68.gtf.gz
```

4. Two vcf files for maternal and paternal imbred line (should be "compatible" with reference genome)
  
> For example, `129S1_SvImJ.mgp.v5.snps.dbSNP142.vcf.gz` and `CAST_EiJ.mgp.v5.snps.dbSNP142.vcf.gz` for 129S1 and CAST mice lines.
```
wget ftp://ftp-mouse.sanger.ac.uk/current_snps/strain_specific_vcfs/129S1_SvImJ.mgp.v5.snps.dbSNP142.vcf.gz
wget ftp://ftp-mouse.sanger.ac.uk/current_snps/strain_specific_vcfs/CAST_EiJ.mgp.v5.snps.dbSNP142.vcf.gz
```
or

Joint vcf file for multiple species, where two lines are presented (should be "compatible" with reference genome)
  
> For example, `mgp.v5.merged.snps_all.dbSNP142.vcf.gz` for multiple mice lines.
```
wget ftp://ftp-mouse.sanger.ac.uk/current_snps/mgp.v5.merged.snps_all.dbSNP142.vcf.gz
```

> *Note: remember about vcf calling by mutect, varscan, etc*

# Input preprocessing:

1. Reference fasta should be indexed (idx).

2. vcf-files should be bgzip-ed and indexed with tabix.

For alignment:

3. Reference fasta should be indexed with samtools.


# Reference preparation:
> `--PSEUDOREF True`

One script to rule them all:

```
python3 /home/am717/ASE/python/prepare_reference_tmp.py --PSEUDOREF True --HETVCF True \
  --pseudoref_dir /dir/for/pseudo/ref/out/ \
  --vcf_dir /dir/for/vcf/outputs/ \
  --ref GRCm38_68.fa \
  --name_mat 129S1_SvImJ --name_pat CAST_EiJ \
  --vcf_joint mgp.v5.merged.snps_all.dbSNP142.vcf.gz \
  --gtf /n/scratch2/sv111/ASE/Mus_musculus.GRCm38.68.gtf
```

## Pseudoreference fasta creation:
> `--HETVCF True`

* Input:

|  | Inbred lines | Inbred lines | Individual | Individual | 
| --- | --- | --- | --- | --- |
|  | Joint lines vcf | Separate line(s) vcf | Joint individuals vcf | Separate individual vcf |
| FASTA Reference genome | --ref | --ref | --ref | --ref |
| VCF Variant file(s)[1]    | --vcf_joint | --vcf_mat, --vcf_pat | --vcf_joind | --vcf_ind |
| Name(s)                | --name_mat, --name_pat | --name_mat, --name_pat | --name_ind | --name_ind |
| FASTA Output directory | --pseudo_dir | --pseudo_dir | --pseudo_dir | --pseudo_dir |
| VCF Output directory   | --vcf_dir | --vcf_dir | --vcf_dir | --vcf_dir |

[1] If one or the alleles in case of inbred lines is reference, then everything should be provided as mat or pat only, consistently.

* Output:
... * Pseudoreference genome fastas with own directories.
... * Support vcf or bed files (if needed).


## Heterozygous(parental) VCF creation:

> NOTE(!) current version on github have no bed option (soon) 

* Input:

|  | Inbred lines | Inbred lines | Individual | Individual | 
| --- | --- | --- | --- | --- |
|  | Joint lines vcf | Separate line(s) vcf | Joint individuals vcf | Separate individual vcf |
| FASTA Reference genome | --ref | --ref | --ref | --ref |
| GTF|BED Selected regions annotation [2] | --gtf or --bed | --gtf or --bed | --gtf or --bed | --gtf or --bed |
| VCF Variant file(s)[1]    | --vcf_joint | --vcf_mat, --vcf_pat | --vcf_joind | --vcf_ind |
| Name(s)                | --name_mat, --name_pat | --name_mat, --name_pat | --name_ind | --name_ind |
| VCF Output directory   | --vcf_dir | --vcf_dir | --vcf_dir | --vcf_dir |

[1] If one or the alleles in case of inbred lines is reference, then everything should be provided as mat or pat only, consistently.
[2] if gtf provided, automatically considered regions=exons and groups=genes; bed file should have 4 columns and prepared in advance: contig, start position, end position, group ID.

* Output:
... * VCF with heterozygous positions, one allele as refeernce and the second as alternative.
... * Support vcf files (if needed).

> *Note: Any extra file will eat some extra space!*

# RNA-seq preparation:

## Alignment (STAR) on parental genomes:

## Allele distributing (merge):

## Reads sampling for mutual analysis:

## SNP allele coverage counting:

## Creating SNP / Grouped SNPs tables:

## Correction constant, CI(AI) and differential analysis:


