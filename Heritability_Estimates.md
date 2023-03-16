Heritability BCC in Boarder Collies
-----------------------------------
### Create GRM with GCTA  
1. Created GRM using the entire cohort (n=356)  
> ../gcta64 --bfile BCC_QC_BC --autosome-num 38 --autosome --make-grm --out BC_BCC_GRM
2. Create a modified GRM pruned based on relatedness of cohort (0,25 cutoff) 
>../gcta64 --grm BCC_GRM_BC --autosome-num 31 --autosome --grm-cutoff 0.25 --make-grm --out BC_BCC_mGRM

      Results: Removed 62 dogs leaving 294 individuals

3. Create a modified GRM pruned based on relatedness of cohort (0.50 cutoff) 
>../gcta64 --grm BC_BCC_GRM --autosome-num 38 --autosome --grm-cutoff 0.5 --make-grm --out BC_BCC_mGRM2

      Results: Removed 19 dogs leaving 337 individuals

### Create GRM with LDAK
1. Cut Weights
>../ldak5.linux.fast --bfile BCC_QC_BC --cut-weights BCC_BC_Cutweights

      Results: This cut the SNPs into 392 sections

2. Calculate Weights - created a shell script (LDAK_OC_CalcWeights) and ran in parallel - submitted to the queue
Example:
> ~/Heritability/ldak5.linux.fast --bfile BCC_QC_BC --calc-weights BCC_BC_Cutweights --section 1 --decay YES --half-life 1000

3. Join weights  
> ../ldak5.linux.fast --bfile BCC_QC_BC --join-weights BCC_BC_Cutweights

      Results: This merged weights and saved in BCC_BC_Cutweights/weights.all with a condensed version in OC_Cutweights2/weights.short

4. Calculate Kinship:  
>../../ldak5.linux.fast --bfile BCC_QC_BC --weights BCC_BC_Cutweights/weights.all --calc-kins-direct BC_BCC_LDAK_GRM --power -0.25

5. Create a modified GRM pruned based on relatedness of cohort (0.25 cutoff) 
>../gcta64 --grm BC_BCC_LDAK_GRM --autosome-num 31 --autosome --grm-cutoff 0.25 --make-grm --out BC_BCC_LDAK_mGRM

      Results: Removed 60 dogs leaving 296 individuals

3. Create a modified GRM pruned based on relatedness of cohort (0.50 cutoff) 
>../gcta64 --grm BCC_GRM_BC --autosome-num 31 --autosome --grm-cutoff 0.5 --make-grm --out BC_BCC_mGRM2

      Results: Removed 9 dogs leaving 347 individuals


### Run REML Analysis  
1. Analysis without covariates and GCTA GRM
>../gcta --reml --grm BC_BCC_GRM --pheno BC_pheno.pheno --prevalence 0.05 --out BC_BCC_GRM_0.05_Estimate  
>../gcta --reml --grm BC_BCC_GRM --pheno BC_pheno.pheno --prevalence 0.08 --out BC_BCC_GRM_0.08_Estimate  
>../gcta --reml --grm BC_BCC_GRM --pheno BC_pheno.pheno --prevalence 0.10 --out BC_BCC_GRM_0.1_Estimate  
>../gcta --reml --grm BC_BCC_mGRM --pheno BC_pheno.pheno --prevalence 0.05 --out BC_BCC_mGRM_0.05_Estimate  
>../gcta --reml --grm BC_BCC_mGRM --pheno BC_pheno.pheno --prevalence 0.08 --out BC_BCC_mGRM_0.08_Estimate  
>../gcta --reml --grm BC_BCC_mGRM --pheno BC_pheno.pheno --prevalence 0.10 --out BC_BCC_mGRM_0.1_Estimate  

2. Analysis without covariates and LDAK GRM  
>../../gcta64 --grm BC_BCC_LDAK_GRM --reml --pheno BC_pheno.pheno --prevalence 0.05 --out BC_BCC_LDAK_GRM_0.05_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_GRM --reml --pheno BC_pheno.pheno --prevalence 0.08 --out BC_BCC_LDAK_GRM_0.08_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_GRM --reml --pheno BC_pheno.pheno --prevalence 0.10 --out BC_BCC_LDAK_GRM_0.10_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_mGRM --reml --pheno BC_pheno.pheno --prevalence 0.05 --out BC_BCC_LDAK_mGRM_0.05_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_mGRM --reml --pheno BC_pheno.pheno --prevalence 0.08 --out BC_BCC_LDAK_mGRM_0.08_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_mGRM --reml --pheno BC_pheno.pheno --prevalence 0.10 --out BC_BCC_LDAK_mGRM_0.10_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_mGRM2 --reml --pheno BC_pheno.pheno --prevalence 0.05 --out BC_BCC_LDAK_mGRM2_0.05_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_mGRM2 --reml --pheno BC_pheno.pheno --prevalence 0.08 --out BC_BCC_LDAK_mGRM2_0.08_Estimate  
>../../gcta64 --grm BC_BCC_LDAK_mGRM2 --reml --pheno BC_pheno.pheno --prevalence 0.10 --out BC_BCC_LDAK_mGRM2_0.10_Estimate

3. Analysis with covariate  
>../../gcta64 --grm BC_BCC_GRM --reml --pheno BC_pheno.pheno --prevalence 0.05 --covar GCTA_Covar_Female_Array_Country.txt --out BC_BCC_Sex_Array_Country_GRM_0.05_Estimate  
>../../gcta64 --grm BC_BCC_GRM --reml --pheno BC_pheno.pheno --prevalence 0.08 --covar GCTA_Covar_Female_Array_Country.txt --out BC_BCC_Sex_Array_Country_GRM_0.08_Estimate  
etc

4. Loops to run GCTA
> for j in {0.05,0.08,0.10};do 
  for i in {$GRM,$mGRM,$mGRM2};do ~/Heritability/gcta64 --reml --grm $i --pheno $Pheno_File --prevalence $j --out ""$Results_Directory""$i"_"$j"_Estimate";done;done
  
> for j in {0.05,0.08,0.10};do
  for i in {$GRM,$mGRM,$mGRM2};do ~/Heritability/gcta64 --reml --grm $i --pheno $Pheno_File --covar $Covar_File --prevalence $j --out ""$Results_Directory""$i"_COUNTRY+Array_"$j"_Estimate";done;done

> for j in {0.05,0.08,0.10};do 
  for i in {$GRM,$mGRM,$mGRM2};do ~/Heritability/gcta64 --reml --grm $i --pheno $Pheno_File --covar $Covar_File2 --prevalence $j --out ""$Results_Directory""$i"_Female+COUNTRY+Array_"$j"_Estimate";done;done
  
> for j in {0.05,0.08,0.10};do 
  for i in {$GRM,$mGRM,$mGRM2};do ~/Heritability/gcta64 --reml --grm $i --pheno $Pheno_File --covar $Covar_File3 --prevalence $j --out ""$Results_Directory""$i"_Conformation+COUNTRY+Array_"$j"_Estimate";done;done

### Table Summary of Results for Boarder Collies
GRM=All Individuals(n=356) mGRM=pruned at 0.25 (n=294) mGRM2=pruned at 0.5 (n=337)

Covariates=F=Female, A=Array, C=Country

|GRM|Covariates|Prevalence|V(G)/Vp_L|SE|P-Value|LDAK_GRM|V(G)/Vp_L|SE|P-Value|
|---|----------|----------|---------|--|-------|--------|---------|--|-------|
|GRM|None|0.05|0.45|0.13|3.8e-10|GRM|0.47|0.13|3.5e-10|
|GRM|None|0.08|0.52|0.15|3.8e-10|GRM|0.55|0.15|3.5e-10|
|GRM|None|0.10|0.56|0.16|3.8e-10|GRM|0.59|0.16|3.5e-10|
|mGRM|None|0.05|0.59|0.17|5.7e-07|mGRM|0.57|0.17|1.9e-06|
|mGRM|None|0.08|0.69|0.19|5.7e-07|mGRM|0.65|0.19|1.9e-06|
|mGRM|None|0.10|0.74|0.21|5.7e-07|mGRM|0.70|0.21|1.9e-06|
|mGRM2|None|0.05|0.61|0.14|1.1e-09|mGRM2|0.54|0.13|3.4e-10|
|mGRM2|None|0.08|0.70|0.16|1.1e-09|mGRM2|0.63|0.15|3.4e-10|
|mGRM2|None|0.10|0.75|0.17|1.1e-09|mGRM2|0.68|0.17|3.4e-10|
|GRM|F,A,C|0.05|0.46|0.14|1.2e-07|GRM|0.48|0.14|1.7e-07|
|GRM|F,A,C|0.08|0.54|0.16|1.1e-07|GRM|0.56|0.16|1.0e-07|
|GRM|F,A,C|0.10|0.57|0.17|1.1e-07|GRM|0.60|0.17|1.0e-07|
|mGRM|F,A,C|0.05|0.72|0.17|3.4e-06|mGRM|0.66|0.18|1.7e-05|
|mGRM|F,A,C|0.08|0.84|0.20|3.4e-06|mGRM|0.76|0.21|1.7e-05|
|mGRM|F,A,C|0.10|0.89|0.22|3.4e-06|mGRM|0.81|0.22|1.7e-05|
|mGRM2|F,A,C|0.05|0.65|0.15|1.8e-08|mGRM|0.59|0.14|1.4e-08|
|mGRM2|F,A,C|0.08|0.75|0.17|1.8e-08|mGRM|0.68|0.16|1.4e-08|
|mGRM2|F,A,C|0.10|0.80|0.18|1.8e-08|mGRM|0.73|0.18|1.4e-08|


Heritability analysis after controlling for age and using strict phenotype
--------------------------------------------------------------------------

Step 1. All cases were removed if their age of onset was >5 years (removed 12 Border Collies) and controls that were <5 years of age (removed 2 individuals).

covariates: A=Array,F=Female,C=Country,CONF=Conformation

|GRM|Covariates|Prevalence|V(G)/Vp_L|SE|P-Value|LDAK_GRM|V(G)/Vp_L|SE|P-Value|
|---|----------|----------|---------|--|-------|--------|---------|--|-------|
|GRM|None|0.05|0.48|0.13|3.02e-10|LDAK_GRM|0.49|0.13|3.47e-10|
|GRM|None|0.08|0.55|0.15|3.02e-10|LDAK_GRM|0.57|0.15|3.47e-10|
|GRM|None|0.10|0.59|0.16|3.02e-10|LDAK_GRM|0.61|0.16|3.47e-10|
|GRM|CONF,C,A|0.05|0.49|0.15|2.97e-05|LDAK_GRM|0.49|0.15|3.19e-05|
|GRM|CONF,C,A|0.08|0.56|0.17|2.97e-05|LDAK_GRM|0.57|0.17|3.19e-05|
|GRM|CONF,C,A|0.10|0.60|0.18|2.97e-05|LDAK_GRM|0.61|0.18|3.19e-05|
|GRM|C,A|0.05|0.50|0.14|7.17e-08|LDAK_GRM|0.51|0.14|7.48e-08|
|GRM|C,A|0.08|0.58|0.16|7.17e-08|LDAK_GRM|0.59|0.16|7.48e-08|
|GRM|C,A|0.10|0.62|0.18|7.17e-08|LDAK_GRM|0.64|0.18|7.48e-08|
|GRM|F,C,A|0.05|0.50|0.14|6.64e-08|LDAK_GRM|0.51|0.14|6.89e-08|
|GRM|F,C,A|0.08|0.57|0.16|6.64e-08|LDAK_GRM|0.59|0.16|6.89e-08|
|GRM|F,C,A|0.10|0.61|0.18|6.64e-08|LDAK_GRM|0.63|0.17|6.89e-08|
|mGRM2|None|0.05|0.70|0.14|3.61e-10|LDAK_mGRM2|0.59|0.14|2.24e-10|
|mGRM2|None|0.08|0.81|0.17|3.61e-10|LDAK_mGRM2|0.69|0.16|2.24e-10|
|mGRM2|None|0.10|0.87|0.18|3.61e-10|LDAK_mGRM2|0.74|0.17|2.24e-10|
|mGRM2|CONF,C,A|0.05|0.74|0.15|7.08e-07|LDAK_mGRM2|0.62|0.15|2.58e-06|
|mGRM2|CONF,C,A|0.08|0.86|0.18|7.08e-07|LDAK_mGRM2|0.72|0.17|2.58e-06|
|mGRM2|CONF,C,A|0.10|0.92|0.19|7.08e-07|LDAK_mGRM2|0.77|0.19|2.58e-06|
|mGRM2|C,A|0.05|0.76|0.15|3.68e-09|LDAK_mGRM2|0.63|0.15|8.4e-09|
|mGRM2|C,A|0.08|0.88|0.17|3.68e-09|LDAK_mGRM2|0.73|0.17|8.4e-09|
|mGRM2|C,A|0.10|0.94|0.19|3.68e-09|LDAK_mGRM2|0.79|0.18|8.4e-09|
|mGRM2|F,C,A|0.05|0.75|0.15|3.20e-09|LDAK_mGRM2|0.64|0.15|5.66e-09|
|mGRM2|F,C,A|0.08|0.87|0.17|3.20e-09|LDAK_mGRM2|0.74|0.17|5.66e-09|
|mGRM2|F,C,A|0.10|0.93|0.18|3.20e-09|LDAK_mGRM2|0.79|0.18|5.66e-09|
|mGRM|None|0.05|0.65|0.18|7.57e-07|LDAK_mGRM|0.61|0.17|2.02e-06|
|mGRM|None|0.08|0.75|0.20|7.57e-07|LDAK_mGRM|0.7|0.2|2.02e-06|
|mGRM|None|0.10|0.80|0.22|7.57e-07|LDAK_mGRM|0.75|0.21|2.02e-06|
|mGRM|CONF,C,A|0.05|0.75|0.19|1.73e-05|LDAK_mGRM|0.65|0.19|1.18e-04|
|mGRM|CONF,C,A|0.08|0.86|0.22|1.73e-05|LDAK_mGRM|0.75|0.22|1.18e-04|
|mGRM|CONF,C,A|0.10|0.93|0.23|1.73e-05|LDAK_mGRM|0.81|0.23|1.18e-04|
|mGRM|C,A|0.05|0.76|0.18|7.58-06|LDAK_mGRM|0.68|0.19|3e-05|
|mGRM|C,A|0.08|0.87|0.21|7.58e-06|LDAK_mGRM|0.78|0.21|3e-05|
|mGRM|C,A|0.10|0.94|0.23|7.58e-06|LDAK_mGRM|0.84|0.23|3e-05|
|mGRM|F,C,A|0.05|0.77|0.18|5.72e-06|LDAK_mGRM|0.69|0.19|2.35e-05|
|mGRM|F,C,A|0.08|0.89|0.21|5.72e-06|LDAK_mGRM|0.8|0.21|2.35e-05|
|mGRM|F,C,A|0.10|0.96|0.23|5.72e-06|LDAK_mGRM|0.86|0.23|2.35e-05|


Step 2: Continued with removing the 14 indivudals based on age but also include a strict phenotyping were only cases listed as A-1 (highly confident of case status) and control which were considered to have normal heat tolerance or were highly heat tolerant (n=) were included.

covariates: A=Array,F=Female,C=Country,CONF=Conformation

|GRM|Covariates|Prevalence|V(G)/Vp_L|SE|P-Value|LDAK_GRM|V(G)/Vp_L|SE|P-Value|
|---|----------|----------|---------|--|-------|--------|---------|--|-------|
|GRM|None|0.05|0.48|0.15|3.25e-08|LDAK_GRM|0.51|0.16|3.67e-08|
|GRM|None|0.08|0.55|0.18|3.25e-08|LDAK_GRM|0.59|0.18|3.67e-08|
|GRM|None|0.10|0.597|0.19|3.25e-08|LDAK_GRM|0.63|0.19|3.67e-08|
|GRM|CONF,C,A|0.05|0.58|0.18|1.04e-04|LDAK_GRM|0.57|0.18|2.02e-04|
|GRM|CONF,C,A|0.08|0.67|0.20|1.04e-04|LDAK_GRM|0.66|0.21|2.02e-04|
|GRM|CONF,C,A|0.10|0.72|0.22|1.04e-04|LDAK_GRM|0.71|0.22|2.02e-04|
|GRM|C,A|0.05|0.59|0.17|1.88e-06|LDAK_GRM|0.6|0.17|3.46e-06|
|GRM|C,A|0.08|0.69|0.20|1.88e-06|LDAK_GRM|0.69|0.2|3.46e-06|
|GRM|C,A|0.10|0.74|0.21|1.88e-06|LDAK_GRM|0.74|0.21|3.46e-06|
|GRM|F,C,A|0.05|0.58|0.17|1.623e-06|LDAK_GRM|0.59|0.17|2.92e-06|
|GRM|F,C,A|0.08|0.67|0.20|1.63e-06|LDAK_GRM|0.68|0.2|2.92e-06|
|GRM|F,C,A|0.10|0.72|0.21|1.63e-06|LDAK_GRM|0.73|0.21|2.92e-06|
|mGRM2|None|0.05|0.83|0.17|3.95e-08|LDAK_mGRM2|0.67|0.17|9.23e-09|
|mGRM2|None|0.08|0.96|0.20|3.95e-08|LDAK_mGRM2|0.78|0.19|9.23e-09|
|mGRM2|None|0.10|1.03|0.22|3.95e-08|LDAK_mGRM2|0.83|0.21|9.23e-09|
|mGRM2|CONF,C,A|0.05|0.88|0.19|1.22e-05|LDAK_mGRM2|0.76|0.18|2.53e-05|
|mGRM2|CONF,C,A|0.08|1.02|0.22|1.22e-05|LDAK_mGRM2|0.88|0.21|2.53e-05|
|mGRM2|CONF,C,A|0.10|1.09|0.23|1.22e-05|LDAK_mGRM2|0.94|0.23|2.53e-05|
|mGRM2|C,A|0.05|0.88|0.19|4.55e-07|LDAK_mGRM2|0.78|0.18|4.58e-07|
|mGRM2|C,A|0.08|1.02|0.22|4.55e-07|LDAK_mGRM2|0.9|0.21|4.58e-07|
|mGRM2|C,A|0.10|1.09|0.23|4.55e-07|LDAK_mGRM2|0.96|0.22|4.58e-07|
|mGRM2|F,C,A|0.05|0.88|0.18|3.91e-07|LDAK_mGRM2|0.78|0.18|3.05e-07|
|mGRM2|F,C,A|0.08|1.02|0.21|3.91e-07|LDAK_mGRM2|0.9|0.2|3.05e-07|
|mGRM2|F,C,A|0.10|1.09|0.23|3.91e-07|LDAK_mGRM2|0.97|0.22|3.05e-07|
|mGRM|None|0.05|0.75|0.22|1.08e-05|LDAK_mGRM|0.73|0.22|1.53e-05|
|mGRM|None|0.08|0.86|0.25|1.08e-05|LDAK_mGRM|0.85|0.25|1.53e-05|
|mGRM|None|0.10|0.92|0.27|1.08e-05|LDAK_mGRM|0.91|0.27|1.53e-05|
|mGRM|CONF,C,A|0.05|0.81|0.23|3.11e-04|LDAK_mGRM|0.71|0.25|1.67e-03|
|mGRM|CONF,C,A|0.08|0.94|0.27|3.11e-04|LDAK_mGRM|0.83|0.28|1.67e-03|
|mGRM|CONF,C,A|0.10|1.01|0.29|3.11e-04|LDAK_mGRM|0.89|0.3|1.67e-03|
|mGRM|C,A|0.05|0.80|0.23|3.49e-04|LDAK_mGRM|0.72|0.24|9.98e-04|
|mGRM|C,A|0.08|0.92|0.27|3.49e-04|LDAK_mGRM|0.84|0.28|9.98e-04|
|mGRM|C,A|0.10|0.99|0.29|3.49e-04|LDAK_mGRM|0.9|0.3|9.98e-04|
|mGRM|F,C,A|0.05|0.80|0.23|3.06e-04|LDAK_mGRM|0.73|0.24|7.89e-04|
|mGRM|F,C,A|0.08|0.92|0.27|3.06e-04|LDAK_mGRM|0.85|0.28|7.89e-04|
|mGRM|F,C,A|0.10|0.99|0.29|3.06e-04|LDAK_mGRM|0.91|0.3|7.89e-04|


