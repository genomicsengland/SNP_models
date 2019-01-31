## Purpose
A system is required to compare the genotypes determined via whole genome sequencing (and potentially other diagnostic tests in the future) with genotypes determined from alternative genotyping strategies performed by the home GLH. The purpose of this process is to confirm that the sample held at the home GLH and the one that was sequenced and for which results have been produced via the National Bioinformatics Service are from the same person. This does not protect for sample swaps upstream of the home GLH's DNA extraction.

## Requirements
The process will:

- Be applied to rare diseases germline samples
- Not be applied to cancer samples as it is thought that comparing germline with tumour is sufficient.
- Be applied to all members of the family
- Stop identification failures from being dispatched to DSS
- Pass identification successes or misses to DSS
- For identification failures, further information about genetic vs reported checks (sex, Mendelian inconsistencies, family relatedness) will be communicated [https://gelreportmodels.genomicsengland.co.uk/html_schemas/org.gel.models.metrics.avro/1.2.0/ReportedVsGenetic.html#/schema/org.gel.models.metrics.avro.ReportedVsGeneticChecks]
- Generate tickets where there are failures 


## Proposed solution

### Prerequisites

- GLHs to provide a list of SNPs that will be genotyped.
- Confirmation that the list of SNPs are informative across a range of ancestries.
- Bioinformatics pipeline will explicitly genotype these SNPs in the variant calling process (i.e. add to the force-gt command).
- GLHs are able to link their local sample ID with the NGIS sample ID and patient ID. There are issues here about how to store these long IDs in LIMS.
- Genotype checking approach 
- We will use bcftools gtcheck [https://samtools.github.io/bcftools/bcftools.html#gtcheck] using the --GTs-only
- It will export CN and ERR as described in the bcftools gtcheck documentation
- We need to define a suitable threshold for the discordance and error rate to fail samples. 


## Phases

We are proposing a two-phased approach:

### Phase 1 - The user uploads a VCF directly into the system and the check is carried out interactively
- Agreed list of SNPs is directly downloadable from interpretation portal and programmatically. 
- GLH creates on VCF per sample according to the spec in [https://github.com/genomicsengland/SNP_models]
- GEL provides a genotype checking service where the GLH uploads the genotypes to the service and compares against an already sequenced genome
- The service can be called programmatically and is available from the interpretation portal for each case
- The service is called by specifying the test request ID, patient ID and the sample ID, and then uploading the VCF. (all three?)
- The service ensures that VCF is properly formed, that test request ID, the patient ID and that sample id match. Service rejects immediately failures here: "please try again"
- Then compares genotypes with genome derived ones.
- Service returns pass or fail and additional information for FAIL
- Service communicates with rest of NGIS to update statuses (TBD)
- Service allows to force the dispatch of a pending status
- The service will produce routine reports indicating which sequenced samples are awaiting SNP checks


### Phase 2 - Users bulk upload VCFs through standard messaging and results are made available in the right documents
- Agreed list of SNPs is directly downloadable from interpretation portal and programmatically. 
- GLH creates a VCF that can be multi-sample
- GLH submits genotypes to NGIS via standard messaging
- SNP Genotypes and WGS derived Genotypes find themselves in the pipeline
- The pipeline calls the service from above
- Concordance data is presented in the interpretation portal and communicated around such that concordance status can be pulled from NGIS
- Service allows to force the dispatch of a pending status
- The service will produce routine reports indicating which sequenced samples are awaiting SNP checks


### Statuses

PASS: Sample ID matches the Participant ID
PENDING: The test has not been performed
FAIL: The test failed

### Processing rule
In both phases, the test request will be processed as follows

FAIL: stop don’t dispatch to DSS
PASS: dispatch to DSS 
PENDING: will need to be explicitly forced through

## Issues
- Only home GLH holds the SNP results but the interpreting GLH may be different. Report will be generated to home GLH with outstanding genotypes
- CIPAPI will need to develop functionality to handle the processing rules
  
