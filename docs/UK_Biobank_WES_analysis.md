---
layout: default
title: Analyze UK Biobank WES data
nav_order: 8
description: "Pipeline to analyze the UK Biobank WES data"
has_children: false
has_toc: false
---


# Pipeline for analyzing UK Biobank WES data using SAIGE-GENE+ on DNAnexus

This documentation describes how to perform the exome-wide association tests with the UK Biobank WES data (450k) on DNAnexus.

## Prerequisites
- [dx command-line client](https://documentation.dnanexus.com/getting-started/cli-quickstart#:~:text=The%20dx%20command%2Dline%20client,along%20step%2Dby%2Dstep.)
- Access to UK Biobank WES data on DNAnexus
- Access to UK Biobank hard-called genotypes in PLINK format(`.bed`/`.bim`/`.fam`) for constructing the genetic relationship matrix (GRM)
- Determination of ancestry groups for participants. The analyses need to be performed separately by ancestry groups.

## Save SAIGE image to DNAnexus

```
docker pull wzhou88/saige:1.0.9
docker save -o saige_1.0.9.tar.gz wzhou88/saige:1.0.9

# make it readable for other users in your project
chmod a+r saige_1.0.9.tar.gz

# store it in your image folder on DNAnexus
dx mkdir docker_images
dx cd docker_images/
dx upload saige_1.0.9.tar.gz
```


## Step 0: constructing a sparse GRM using the LD pruned hard-called genotypes.

- This step can be done locally instead of on DNAnexus because it only needs the hard called genotypes.

- Once the sparse GRM is constructed, it can be used for all phenotypes

- You need to first conduct LD pruning for the hard-called genotypes in the plink file (`.bed`/`.bim`/`.fam`).

### Preparing input files

 - Perform LD pruning using PLINK2 with the parameters *--indep-pairwise 50 5 0.05* using the PLINK file for GRM construction using the hard called genotypes


### Run the script
```
Rscript createSparseGRM.R       \
    --plinkFile=${LD pruned PLINK file} \
    --nThreads=72  \
    --outputPrefix=${OUTNAME}       \
    --numRandomMarkerforSparseKin=5000      \
    --relatednessCutoff=0.05
```

- Benchmark:  with 241,660 markers and 459,797 European samples in the LD pruned PLINK file, the script below takes 7hr:40min with 72 CPUs and 35 Gb of memory to finish.  


### Output
  1. file containing the sparse GRM. It can be read using the *readMM* function in the *Matrix* package in R

      `${OUTNAME}_relatednessCutoff_0.05_5000_randomMarkersUsed.sparseGRM.mtx`

  2. file containing sample IDs in the sparse GRM.
      `${OUTNAME}_relatednessCutoff_0.05_5000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt`


## Step 1: fitting the null linear/logistic mixed model using the sparse GRM and estimating variance ratios


### Create the workflow using the wdl file

* Download
    1. `dxCompiler-2.10.1.jar` from [here](https://github.com/dnanexus/dxCompiler/releases)
    2. `saige_null_sGRM_vr_withinfo.wdl` from [here](https://github.com/saigegit/UKBB-WES/blob/main/saige_step1_spGRMforNULLModel.wdl) and replace the path to the docker image in the file

* Check the `project id` of your current project on DNAnexus

```
## create a folder to store the workflows
dx mkdir workflow

## create the workflow. It will give you a workflow id
java -jar dxCompiler-2.10.1.jar compile saige_null_sGRM_vr_withinfo.wdl -project ${project id}  -folder /workflows/ -f
```

### Prepare input files

- sparse GRM files from Step 0  (`sparseGRM_file` and `sparseGRM_sample_file`)
- a file containing the phenotype and covariates values (`pheno_file`)

- creating a new PLINK file containing 2,000 randomly selected markers `PLINK_for_vr` using the following scripts from the large PLINK file provided by UKBB with hard called genotypes `PLINK_file_hard_called`
 - 1,000 markers with 10 <= MAC < 20
 - 1,000 markers with MAC >= 20


```
#1. calculate allele counts for each marker in the large plink file with hard called genotypes
plink2 --bfile ${PLINK_file_hard_called} --freq count --out ${OUTPUT_prefix_counts}

#2 randomly extract IDs for markers falling in the two MAC categories
cat <(tail -n +2 ${OUTPUT_prefix_counts}.acount | awk '((2*$6-$5) < 20 && (2*$6-$5) >= 10) || ($5 < 20 && $5 >= 10) {print $2}' | shuf -n 1000) \
<(tail -n +2 ${OUTPUT_prefix_counts}.acount | awk ' $5 >= 20 && (2*$6-$5)>= 20 {print $2}' | shuf -n 1000) > ${OUTPUT_prefix_counts}.markerid.list


#3. extract markers from the large plink file
plink2 --bfile ${PLINK_file_hard_called} --extract ${OUTPUT_prefix_counts}.markerid.list --make-bed --out ${PLINK_for_vr}

```

### Run the Step 1 job for one phenotype

- replace *saige* with your project name
- replace the file paths, e.g change */SAIGE_GENE/phenotype/* to the folder there the `pheno_file` is stored
- replace the variable values with the correct ones

```
instance_type="mem1_ssd1_v2_x4"
traitType=binary
invNormalize=FALSE
phenoCol=value
covariatesList=PC1,PC2,PC3,PC4,PC5,PC6,PC7,PC8,PC9,PC10,PC11,PC12,PC13,PC14,PC15,PC16,PC17,PC18,PC19,PC20,age,age_sex,age2,age2_sex,sex
qCovarColList=sex
sampleIDCol=userId
pheno_file=phecode-250.2-both_sexes_wowithdrawl_with450kWES.tsv
PLINK_for_vr=ukb.EUR.for_grm.pruned.plink.forvr
sparseGRMfile=ukb.EUR_relatednessCutoff_0.05_5000_randomMarkersUsed.sparseGRM.mtx
sparseGRM_sample_file=ukb.EUR_relatednessCutoff_0.05_5000_randomMarkersUsed.sparseGRM.mtx.sampleIDs.txt
jobname=phecode-250.2_step1

workflow_id=workflow-GB2gx6jJ6y3j4P2f2K60Pfxz


dx run ${workflow_id} \
      -istage-common.phenofile=saige:/SAIGE_GENE/phenotype/${pheno_file} \
      -istage-common.bedfile=saige:/SAIGE_GENE/genotype/${PLINK_for_vr}.bed \
      -istage-common.bimfile=saige:/SAIGE_GENE/genotype/${PLINK_for_vr}.bim \
      -istage-common.famfile=saige:/SAIGE_GENE/genotype/${PLINK_for_vr}.fam \
      -istage-common.spGRMfile=saige:/SAIGE_GENE/spGRM/${sparseGRMfile} \
      -istage-common.spGRMSamplefile=saige:/SAIGE_GENE/spGRM/${sparseGRM_sample_file} \
      -istage-common.output_prefix=${outputPrefix} \
      -istage-common.phenoCol=${phenoCol} \
      -istage-common.traitType=${traitType} \
      -istage-common.covariatesList=${covariatesList} \
      -istage-common.qCovarColList=${qCovarColList} \
      -istage-common.sampleIDCol=${sampleIDCol} \
      -istage-common.invNormalize=${invNormalize} \
      --folder saige:/SAIGE_GENE/step1_output/ \
      --yes \
      --name=${jobname} \
      --instance-type=${instance_type}
```

### Output files
- Model file: `${outputPrefix}.rda`
- Variance ratio file: `${outputPrefix}.varianceRatio.txt`
- Job summary file (showing time and memory usage for the job): `${outputPrefix}.runinfo.txt`


### Run the Step 1 job for multiple phenotype

Step 1 can be run for multiple phenotypes, each has a different job name. We can use a file `pheno_list.txt` to store the input information for each phenotype

- Each row in `pheno_list.txt` is for one phenotype with pheno name, trait type (binary or quantitative), invNormalize (FALSE or TRUE), covariates list (e.g. PC1,PC2,PC3,PC4,PC5,PC6,PC7,PC8,PC9,PC10,PC11,PC12,PC13,PC14,PC15,PC16,PC17,PC18,PC19,PC20,age,age_sex,age2,age2_sex,sex ), and categorical covariants list (e.g. sex)

```
while read -r pheno traitType invNormalize covariatesList qCovarColList
do
  instance_type="mem1_ssd1_v2_x4"
  traitType=${traitType}
  invNormalize=${invNormalize}
  phenoCol=${pheno}
  covariatesList=${covariatesList}
  qCovarColList=${qCovarColList}
  sampleIDCol=IID

  ##differenet phenotype files for each phenotype
  pheno_file=${pheno}.txt
  jobname=${pheno}_Step1

  dx run ${workflow_id} \
      -istage-common.phenofile=saige:/SAIGE_GENE/phenotype/${pheno_file} \
      -istage-common.bedfile=saige:/SAIGE_GENE/genotype/${PLINK_for_vr}.bed \
      -istage-common.bimfile=saige:/SAIGE_GENE/genotype/${PLINK_for_vr}.bim \
      -istage-common.famfile=saige:/SAIGE_GENE/genotype/${PLINK_for_vr}.fam \
      -istage-common.spGRMfile=saige:/SAIGE_GENE/spGRM/${sparseGRMfile} \
      -istage-common.spGRMSamplefile=saige:/SAIGE_GENE/spGRM/${sparseGRM_sample_file} \
      -istage-common.output_prefix=${outputPrefix} \
      -istage-common.phenoCol=${phenoCol} \
      -istage-common.traitType=${traitType} \
      -istage-common.covariatesList=${covariatesList} \
      -istage-common.qCovarColList=${qCovarColList} \
      -istage-common.sampleIDCol=${sampleIDCol} \
      -istage-common.invNormalize=${invNormalize} \
      --folder saige:/SAIGE_GENE/step1_output/ \
      --yes \
      --name=${jobname} \
      --instance-type=${instance_type}

done < pheno_list.txt
```


## Step 2: Performing exome-wide gene-based tests

### Create the workflow using the wdl file

* Download
    1. (skip if already done for Step 1) `dxCompiler-2.10.1.jar` from [here](https://github.com/dnanexus/dxCompiler/releases)
    2. `saigegene_step2.wdl` from [here](https://github.com/saigegit/UKBB-WES/blob/main/saigegene_step2.wdl) and replace the path to the docker image in the file
* Check the `project id` of your current project on DNAnexus

```
## create the workflow. It will give you a workflow id
java -jar dxCompiler-2.10.1.jar compile saigegene_step2.wdl -project ${project id}  -folder /workflows/ -f
```


### Prepare input files

* sparse GRM files from Step 0 and used in Step 1  (`sparseGRM_file` and `sparseGRM_sample_file`)
* model file output by Step 1: `${outputPrefix}.rda`
* variance ratio file output by Step 1: `${outputPrefix}.varianceRatio.txt`
* Genotype files containing the WES data on DNAnexus in the plink format (`.bed`/`.bim`/`.fam`)
* Group file containing marker lists and annotations for genes. See [here]() for the format
    * You may download pre-prepared group files by chromosome from [here](https://drive.google.com/file/d/1WvmVXV1NWhS3AbiwEX38yf_9MY9L047h/view?usp=sharing). This file contains loss of function (LoF), missense, and synonymous markers.
    * You may prepare the group files with more other annotations from the [ANNOVAR](https://annovar.openbioinformatics.org/en/latest/) annotated files from [here](https://drive.google.com/file/d/19RSoVc72k_baDzfZF2Db8LOLiZ3mNiYn/view?usp=sharing) by modifying [this script](https://github.com/saigegit/UKBB-WES/blob/main/make-group-file_use_annovar_output.R)


### Run the Step 2 jobs for a phenotype

We use 3 annotation masks: LoF, LoF+Missense, and LoF+Missense+Synonymous and 3 max MAF cutoffs: 0.0001, 0.001, 0.01


```
##For these large chromosomes, we need to use a instance with larger storage
array=("1" "2" "3" "6" "7" "11" "12" "16" "17" "19")

for chr in {1..22};
do
    if [[ " ${array[*]} " =~ " ${chr} " ]]; then
      echo "${chr}"
      echo "chr is large"
      instance_type=mem1_ssd2_v2_x4
    fi

    if [[ ! " ${array[*]} " =~ " ${chr} " ]]; then
      echo "${chr}"
      echo "chr is small"
      instance_type=mem2_ssd2_v2_x2
    fi


    dx run ${workflow} \
        -istage-common.GMMATmodelFile=saige:/SAIGE_GENE/step1_output/${outputPrefix}.rda \
        -istage-common.varianceRatioFile=saige:/SAIGE_GENE/step1_output/${outputPrefix}.varianceRatio.txt \
        -istage-common.spGRMfile=saige:/SAIGE_GENE/spGRM/${sparseGRM_file} \
        -istage-common.spGRMSamplefile=saige:/SAIGE_GENE/spGRM/${sparseGRM_sample_file} \
        -istage-common.bed=saige:/Bulk/Exome\ sequences/ukb_c${chr}_b0_v1.bed \
        -istage-common.bim=saige:/Bulk/Exome\ sequences/ukb_c${chr}_b0_v1.bim \
        -istage-common.fam=saige:/Bulk/Exome\ sequences/ukb_c${chr}_b0_v1.fam \
        -istage-common.grpfile=saige:/SAIGE_GENE/group_files/${group}_chr${chr}.txt \
        -istage-common.chrom=${chr} \
        -istage-common.annotation_in_groupTest=lof,missense:lof,missense:lof:synonymous \
        -istage-common.maxMAF_in_groupTest=0.01,0.001,0.0001 \
        -istage-common.output_prefix=${outputPrefix}_${pheno}_${chr}.txt \
        -istage-common.isFast=TRUE \
        --folder=saige:/SAIGE_GENE/step2_output/ \
        --name=Step2_${pheno}_chr${chr} \
        --yes \
        --instance-type=${instance_type}

done

```
### Output files
- Set-based association output: `${outputPrefix}_${pheno}_${chr}.txt`
- Single-variant association output: `${outputPrefix}_${pheno}_${chr}.singleAssoc.txt`
- Index file: `${outputPrefix}_${pheno}_${chr}.txt.index`
- Job summary file (showing time and memory usage for the job): `${outputPrefix}_${pheno}_${chr}.txt.runinfo.txt`

## Benchmark on the cost for jobs on DNAnexus

- 2 quantitative traits: LDL, bone marrow density
- 2 binary traits: T2D and Glaucoma(for Glaucoma, sample relatedness cutoff 0.125 was used)

* Step 1: Fit the model with a sparse GRM with sample relatedness cutoff 0.05
    - instance used: mem1_ssd1_v2_x4
    - Jobs were all finished within 10 mins and costed < £0.003

* Step 2:
    - LDL, N = 375670, cost £3.03
    - bone marrow density, N = 125298, cost £1.27
    - T2D, 21279 cases and 371629 controls, cost  £3.18
    - Glaucoma: 6244 cases and 407632 controls, cost  £3.01
