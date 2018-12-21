# VCF specification to provide SNP types to GEL for positive sample identification
version 0.1

## Genome version

- Variants must be referenced to the GRCh38 genome version.
- Genomics England uses the following reference files: http://ftp.1000genomes.ebi.ac.uk/vol1/ftp/technical/reference/GRCh38_reference_genome/README.20150309.GRCh38_full_analysis_set_plus_decoy_hla
- Chromosome names should be prefixed with"chr". The mitochondrion is called "chrM". There following values are permitted: {chr1, chr2, chr3, chr4, chr5, ..., chr21, chr22, chrX, chrY, chrM}.

## The VCF specification

- Files will be submitted using the VCF 4.2 specification: https://samtools.github.io/hts-specs/VCFv4.2.pdf
- Files can contain 1 or more samples

### Minimum requirements

- The header shall include the following tags:
```
##fileformat=VCFv4.2
##fileDate=<date the file was produced: eg. 20090805>
##source=<data source: e.g. mygenotypingplatformV3.4>
##reference=<name of the fasta file used>
##contig=<as per the specification>
```

- The `source` field must be from an enumeration reflecting the standardised names of the assays used for genotyping.
- The genotype columns must be named by the Sample ID [please insert here unique name for this ID from TOMS].
- Genotypes must contain the `GT` field.
- It is highly desirable to include the Genotype Likelihood `PL` field where possible. This allows a more sophisticated concordance check.
- It is desirable to add a genotype quality `GQ`
- Only biallelic SNVs are permitted. The ALT field must not contain more than one value
- Only `PASS` variants will be used
- Variants should be normalised, i.e. parsimonious and left-aligned
- Variants should be sorted by reference contig name and position.
  
## Dealing with duplicate entries

- If genotypes for the same sample are resubmitted either on purpose or by mistake, the latest genotype according to the ##fileDate field will be used. If there are multiple occurrences of the same sample and same fileDate then one of them will be chosen at random (likely the last one that was inserted in the database)

## Example

```
##fileformat=VCFv4.2
##fileDate=20090805
##source=my_assay
##reference=http://ftp.1000genomes.ebi.ac.uk/vol1/ftp/technical/reference/GRCh38_reference_genome/GRCh38_full_analysis_set_plus_decoy_hla.fa
##contig=<ID=chr20,length=62435964,assembly=GRCH38,md5=f126cdf8a6e0c7f379d618ff66beb2da,species="Homo sapiens",taxonomy=x>
##FILTER=<ID=PASS,Description="All filters passed">
##FILTER=<ID=NOCALL,Description="Genotype not called on array.">
##FORMAT=<ID=GT,Number=1,Type=String,Description="Genotype">
##FORMAT=<ID=GQ,Number=1,Type=Integer,Description="Genotype Quality">
#CHROM  POS     ID      REF     ALT     QUAL    FILTER  INFO    FORMAT  NA00001 NA00002 NA00003
chr20   14370   rs6054257       G       A       29      PASS    .       GT:GQ   0|0:48  1|0:48  1/1:43
chr20   17330   .       T       A       3       PASS    .       GT:GQ   0|0:49  0|1:3   0/0:41
```
