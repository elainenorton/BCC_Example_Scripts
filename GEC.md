Calculate The Efective Number of SNPs using GEC
------------------------------------------------

### Step 1: Remove individuals from the plink files by breed

> plink --dog --allow-no-sex --file ~/Heritability/Dog_Heritability/Original_Data_Files/BCC_QC --keep ~/Heritability/Dog_Heritability/BorderCollies_ID_All.txt  --chr 1-22 --recode --make-bed --out BCC_BorderCollies_All_Chr1to22

> plink --dog --allow-no-sex --file ~/Heritability/Dog_Heritability/Original_Data_Files/BCC_QC --keep ~/Heritability/Dog_Heritability/BorderCollies_ID_All.txt  --chr 23-39 --recode --make-bed --out BCC_BorderCollies_All_Chr23to39

> plink --dog --allow-no-sex --file ~/Heritability/Dog_Heritability/Original_Data_Files/BCC_QC --keep ~/Heritability/Dog_Heritability/AustralianShep_ID_All.txt  --chr 1-22 --recode --make-bed --out BCC_AustralianShephards_All_Chr1to22

> plink --dog --allow-no-sex --file ~/Heritability/Dog_Heritability/Original_Data_Files/BCC_QC --keep ~/Heritability/Dog_Heritability/AustralianShep_ID_All.txt  --chr 23-39 --recode --make-bed --out BCC_AustralianShephards_All_Chr23to39


### Step 2: Renumber the files with chromosomes >22 (this code is within the script Plink_File_Conversion.py

> python3
> plink_file=open("BCC_BorderCollies_All_Chr23to39.map",'r')
> New_Plink_File=open("BCC_BorderCollies_All_Chr23to39_Renumbered.map",'w')

> for line in plink_file:  
>	line=line.strip()  
>	line=line.split('\t')  
>	line[0] = int(line[0])  
>	if line[0]>22:  
>	  line[0]=line[0]-22  
>	else:  
>	  print("found a line that does not make sense")  
>	print("{}\t{}\t{}\t{}".format(line[0],line[1],line[2],line[3]),sep="",file=New_Plink_File)  

> plink_file.close()
> New_Plink_File.close()

#### Convert files back to binary files
plink --ped BCC_BorderCollies_All_Chr23to39.ped --map BCC_BorderCollies_All_Chr23to39_Renumbered.map --dog --make-bed --out BCC_BorderCollies_All_Chr23to39

### Step 3: Run GEC
> java -jar ../gec.jar –Xmx1g --effect-number --plink-binary BCC_BorderCollies_All_Chr1to22 --maf 0.01 --genome --out BCC_BorderCollies_All_Chr1to22

> java -jar ../gec.jar –Xmx1g --effect-number --plink-binary BCC_BorderCollies_All_Chr23to39  --maf 0.01 --genome --out BCC_BorderCollies_All_Chr23to39

> java -jar ../gec.jar –Xmx1g --effect-number --plink-binary BCC_AustralianShephards_All_Chr1to22 --maf 0.01 --genome --out BCC_AustralianShephards_All_Chr1to22

### Results
|Breed|Chromosome|Observed_SNPs|Effective_Number|Total|
|-----|----------|-------------|----------------|-----|
|BorderCollie|chr1-22|302255|138889.18|NA|
|BorderCollie|chr23-39|141753|71158.42|210047|
|AustralianSheph|chr1-22|320983|168020.47|NA|
|AustralianSheph|chr23to39|149425|84001.02|252021|

      Results:
      Genomewide p-value Border Collies: 2.3e-07
      Genomewide p-value Australian Shephards: 1.98e-07

