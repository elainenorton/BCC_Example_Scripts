The main objective of this part of the project was to try to find the individual posterior probabilities of the SNPs associated with the SNPs located in the 3rd and 4th bins (those with moderate to high effect) identified by BayesR.   
* Note: The program is written in the Fortran Programming Language  
* Paper: https://www.biorxiv.org/content/10.1101/2020.10.23.351700v1.full 
* Manual: https://github.com/fmollandin/BayesRCO/blob/main/doc/BayesRCO.pdf  
* GitHub: https://github.com/fmollandin/BayesRCO.git  
* Input Files: Plink bfiles and the categories file (see step 3).  Phenotypes must be present in the fam file and corrected for covariates.  

1. Clone the repository - this is located on MSI in Dog_BCC/Dog_Heritability/  
```https://github.com/fmollandin/BayesRCO.git```

2. Activate the source code for Fotran - these files were located in the src folder  
```gfortran RandomDistributions.f90 baymodsRCO.f90 bayesRCO.f90 -o bayesRCO```

3. Create the categories file.  For BayesR, this needs to be a single column of 1s with the same number of rows as SNPs.  Created this in R.  

4. Ran BayesRCO - created a shell script to send to the queue - note, took approximately 8 hours to run.  Shell script: BayesRCO.sh
```./bayesRCO -bfile ../../BayesR/BC_Age_Residuals_BCC_QC -out BayesRCO_Outfile -catfile BCC_Categories.txt -ncat 1 -cat -beta```  

5. Ran the second part of BayesRCO - created a shell script to send to the queue - BayesRCO_Part2.sh
```./bayesRCO -bfile ../../BayesR/BC_Age_Residuals_BCC_QC -out BayesRCO_Outfile_Model -model BayesRCO_Outfile.model -freq BayesRCO_Outfile.frq -param BayesRCO_Outfile.param -catfile BCC_Categories.txt -cat -beta```


