##Data File QC and Partitioning

Adjusted the phenotype data file so that it could be uploaded into R (removed spaces, "", and added NA for blank cells).  Removed several columns to improve upload.
Within R:
* Created a phentype column as a binary value (0=controls, 1=cases).  For the Case_Control column this included all individuals
* Created ID files with the FID and IID of both Border Collies and Australian Shepards (did not include the Minature Australian Shepherds in the initial analysis).

##Genotype File

Created two sets of plink files using both the Australian and Border Collie data - for each set, prunded for a MAF=0.01 and genotyping rate of 0.1 (i.e. present in 90% of the individuals), and removed chromosome X (chr39).

> plink --dog --bfile BCC --keep AS_ID.txt --maf 0.01 --geno 0.1 --not-chr 39 --make-bed --out BCC_QC_AS

      Results: Kept 151 dogs out of 520, 27,190 SNPs removed for MAF (remaining=477,509 out of 509,789)

> plink --dog --bfile BCC --keep BC_ID.txt --maf 0.01 --geno 0.1 --not-chr 39 --make-bed --out BCC_QC_BC

      Results: Kept 356 dogs out of 520, 61,412 SNPs removed for MAF (remaining=443,287 out of 509,789)
