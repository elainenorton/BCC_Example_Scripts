### Summary

This script uses BayesR to predict the number of SNPs with a low, moderate to high effect using the software program BayesR

#Note, BayesR does not have an option to include covariates into the analysis, so instead, used R to obtain the residuals after correcting for conformation, country and array.

#### Run BayesR using default parameters and residuals for phenotype
#Note: Used default parameters which include 4SNPs/window

> bayesRv2 -bfile BC_Age_Residuals_BCC_QC -out BC_Age_BC_Residuals

##### Results

#Note: total number of SNPs in the plink files is 441,466 SNPs

1. BC_Age_BC_Residuals.model

    Mean	  -0.3287091E-01  #The mean intercept  
    Nsnp	   0.2406743E+04  #The number of SNPs in the model
    Va         0.4543649E+00  
    Ve         0.7469457E+00  
    Nk1        0.4390593E+06  #The number of SNPs in mixture components 1-4 where Nk1=0.0000 
    Nk2        0.2002707E+04  #Nk2=0.0001(VGE)   
    Nk3        0.3494680E+03  #NK3=0.001(VGE)  
    Nk4        0.5456833E+02  #NK4=0.01(VGE)  
    Pk1        0.9945386E+00  #The percent of SNPs in mixture components  
    Pk2        0.4539925E-02  
    Pk3        0.7955343E-03  
    Pk4        0.1258978E-03  
    Vk1        0.0000000E+00  #The sum of squares of SNP effect  
    Vk2        0.1033207E+00  
    Vk3        0.1806524E+00  
    Vk4        0.1692558E+00  
    
 #### Repeat the analysis using GCTA heritability estimate
 > bayesRv2 -bfile BC_Age_Residuals_BCC_QC -vara 0.49 -dfvara -3.0 -out BC_Age_BC_Residuals_Heritability
