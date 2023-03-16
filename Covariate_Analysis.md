## Covariate Evaluation for inclusion within the heritability analysis

#### 1. Evaluation of MDS/PCA plots with the GRM
> ./gcta64 --grm BC_BCC_GRM --autosome-num 38 --autosome --pca 10 --out GCTA_PCA

Note: GCTA and Plink use the same algorithim to create the GRM but plink would not read in the GCTA GRM so instead created the plink GRM to run the MDS plot

> plink --dog  --bfile ./BCC_QC_BC --genome --out BC_BCC_Plink_GRM 
> plink --dog --allow-no-sex --bfile ./BCC_QC_BCC --read-genome BC_BCC_Plink_GRM.genome --cluster --mds-plot 6 --out BC_BCC_Plink_MDS

**Used R to create plots for visualization**
> mds <- read.table(file="BC_BCC_Plink_MDS.mds",header=TRUE)
> mds$FID <- NULL
> mds$SOL <- NULL
> mds2 <- merge(mds,pheno_BC,by="IID")
> attach(mds2)

> mds2$Case_Control2 <- ifelse(mds2$Case_Control=='1', c("Case"), c("Control"))

> mycolors <- c("Red","Blue","Green","darkorange2","Black","Purple","Pink","Brown","Yellow","Grey","aquamarine","coral","darkmagenta","cyan","chartreuse")
c25 <- c(
  "dodgerblue2", "#E31A1C", # red
  "green4",
  "#6A3D9A", # purple
  "#FF7F00", # orange
  "black", "gold1",
  "skyblue2", "#FB9A99", # lt pink
  "palegreen2",
  "#CAB2D6", # lt purple
  "#FDBF6F", # lt orange
  "gray70", "khaki2",
  "maroon", "orchid1", "deeppink1", "blue1", "steelblue4",
  "darkturquoise", "green1", "yellow4", "yellow3",
  "darkorange4", "brown"
)
> n=length(levels(mds2$COUNTRY))

> g <- 
ggplot(data=mds2,aes(x=C1,y=C2,group=COUNTRY,col=COUNTRY))+geom_point()+theme_classic()+theme(axis.line = element_line(colour = 
"black"))+scale_color_manual(values=c25[sample(1:n,n,replace=FALSE)])+theme(legend.position=c(0.9,0.8))+xlab("C1")+ylab("C2")+theme(axis.line.x = 
element_line(color="black"),axis.line.y = element_line(color="black"))

> g <- g+labs(colour="Country")

> pdf("BoarderCollies_BCC_MDS_Country.pdf")
> g
> dev.off()

      Results: Evaluation of the clustering between MDS and PCA plots for Country, Sex, and Array did not reveal any obvious clustering.  Further, <4% of the variation could be explained by PCA1 and <1% by PCA2.
      

#### 2. Evaluation of AIC values using case-control status as the outcome variable
> fit0 <- glm(formula(paste(trait, 1, sep="~")), family=binomial,data=df_name)  
> AIC_fit0 <- summary(fit0)$aic

> fit1 <- glm(formula(paste(trait, 'Female', sep="~")), family=binomial,data=df_name)  
> AIC_fit1 <- summary(fit1)$aic

#Note created a function to write the results to a dataframe with all of the potential covariates.

|Covariates|AIC|
|----------|---|
|None|495.4758|
|Female|494.0786|
|Array|491.2635|
|Female + Array|488.8013|
|Country|476.0961|
|Female + Country|473.0098|
|Array + Country|475.1872|
|Female + Array + Country|470.9557|


#### 3. Evaluation of LogLikelihood values and p-value using chisquare using case-control status as the outcome variable

> fit1 <- glm(Case_Control ~ 1, family=binomial, data=pheno_BC)  
> fit1 <- glm(Case_Control ~ Female, family=binomial, data=pheno_BC)  
> delta_2ll <- -2*(logLik(fit0) - logLik(fit1))  
> degree_freedom <- length(coefficients(fit1)-length(coefficients(fit0)   
> pchisq(delta_2ll,df=degree_freedom,lower.tail=FALSE)  

#Note created a function to write the results to a dataframe with all of the potential covariates.

|Covariates|LogLikelihood|DF|P-Value|
|----------|-------------|--|-------|
|None|-246.7379|1|NA|
|Female|-245.0393|2|0.065|
|Array|-240.6318|5|0.016|
|Female + Array|-237.4007|7|4.8e-03|
|Country|-223.0480|15|1.66e-05|
|Female + Country|-220.5049|16|4.73e-06|
|Array + Country|-218.5936|19|8.02e-06|
|Female + Array + Country|-215.4778|20|1.54e-06|


#### 4. Evaluate for Multicollinearity (Variance Inflation Factor)

> Model1 <- lm(Case_Control ~ Female + Array + COUNTRY, data=pca2)  
> Car::vif(model1)

|Covariates|GVIF|DF|Corrected_GVIF|
|----------|----|--|---------------|
|Female|1.049810|1|1.024602|
|Array|1.349090|4|1.038138|
|Country|1.387077|14|1.011754|


#### 5. Evaluate for Pseudo R2

> library(blorr)  
> blr_rsq_nagelkerke(model)

|Covariates|Nagelkerke_R2|
|----------|-------------|
|None|NA|
|Female|0.013|
|Array|0.045|
|Female + Array|0.063|
|Country|0.166|
|Female + Country|0.183|
|Array + Country|0.195|
|Female + Array + Country|0.215|


  
 

