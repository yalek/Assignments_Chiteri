#UNIX Assignment
##Author: Chiteri, 2/26/2021
##Data Inspection
### Attributes of fang_et_al_genotypes.txt
My code snippet:-
 
	``` 
	wc  -l fang_et_al_genotypes.txt 
	du  -h fang_et_al_genotypes.txt  
	awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
	cut -f 1-7 fang_et_al_genotypes.txt | head -n 4

	
	```  

By inspecting this file I learned that the:  
	1. No of lines are:- 
		2783

	2. Size of the file is:-  
    	6.1M     

	3. No of columns is:-
		986
	4. Columns 1- 7
		Sample_ID       JG_OTU  Group   abph1.20        abph1.22        ae1.3   ae1.4
		SL-15   T-aust-1        TRIPS   ?/?     ?/?     T/T     G/G
		SL-16   T-aust-2        TRIPS   ?/?     ?/?     T/T     ?/?
		SL-11   T-brav-1        TRIPS   ?/?     ?/?     T/T     G/G

### Attributes of snp_position.txt
My code snippet:-
 
	``` 
	wc  -l snp_position.txt  
	du  -h snp_position.txt  
	awk -F "\t" '{print NF; exit}' snp_position.txt
	cut -f 1,3,4 snp_position.txt | head -n 5
	
	```
By inspecting this file I learned that the:  
	1. No of lines are:- 
		984   
 
	2. Size  of the file:-
    	38K     

	3. No of columns are:-
		15
	4. 1st, 3rd, 4th column
		SNP_ID  	Chromosome      Position
		abph1.20        2       27403404
		abph1.22        2       27403892
		ae1.3   		5       167889790
		ae1.4   		5       167889682

##Data Processing

### Preparation to join
#### You need to extract both maize and teosinte before transposing
### I created a file with the headers then used the awk function to extract maize records

##Extracting maize genotypes

```
head -n 1 fang\_et\_al\_genotypes.txt > maize_genotypes.txt
```  

```
awk -F "\t" '$3 ~ /ZMMIL|ZMMLR|ZMMMR/ ' fang\_et\_al\_genotypes.txt >> maize_genotypes.txt
```

###Inspecting the maize genotype file
```
cut -f3 maize_genotypes.txt | uniq
```  
	ZMMLR  
	ZMMMR  
	ZMMIL  

```
wc -l maize_genotypes.txt 
```  
	1574 maize_genotypes.txt

##Extracting teosinte genotypes
```
head -n 1 fang_et_al_genotypes.txt > teosinte_genotypes.txt
```

```
awk '$3 ~ /ZMPBA|ZMPIL|ZMPJA/ ' fang_et_al_genotypes.txt >> teosinte_genotypes.txt
```

###Inspecting teosinge genotypes file
```
cut -f3 teosinte_genotypes.txt | uniq
```  
	ZMPBA  
	ZMPJA  
	ZMPIL
  
``` 
wc -l teosinte_genotypes.txt
```  
976 teosinte_genotypes.txt


##Transposing 
##Maize...
###Before transposing
```
	cut -f1-5 maize_genotypes.txt | head -n 5 | column -t
```  

	Sample_ID 	JG_OTU                  Group  abph1.20  abph1.22  
	ZDP_0752a  Zmm-LR-ACOM-usa-NM-1_s  ZMMLR  C/G       A/A  
	ZDP_0793a  Zmm-LR-ACOM-usa-NM-2    ZMMLR  C/G       A/A  
	ZDP_0612a  Zmm-LR-ALAZ-Per-JEQU    ZMMLR  C/C       A/A  
	ZDP_0602a  Zmm-LR-ALAZ-Per-MOCH    ZMMLR  C/G       A/A  

###Transposing
```	awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.xt
```

###After transposing
```
	cut -f1-5 transposed_maize_genotypes.txt | head -n 5 | column -t
```

	Sample_ID  ZDP_0752a               ZDP_0793a             ZDP_0612a             ZDP_0602a
	JG_OTU     Zmm-LR-ACOM-usa-NM-1_s  Zmm-LR-ACOM-usa-NM-2  Zmm-LR-ALAZ-Per-JEQU  Zmm-LR-ALAZ-Per-MOCH
	Group      ZMMLR                   ZMMLR                 ZMMLR                 ZMMLR
	abph1.20   C/G                     C/G                   C/C                   C/G
	abph1.22   A/A                     A/A                   A/A                   A/A

##Teosinte...
###Before transposing
```
	cut -f1-5 teosinte_genotypes.txt | head -n 5 | column -t
```

	Sample_ID  JG_OTU              Group  abph1.20  abph1.22
	S0881      Zmp-B-ACAPET-GR-01  ZMPBA  C/G       A/A
	S1057      Zmp-B-ACAPET-GR-02  ZMPBA  C/G       A/A
	S1087      Zmp-B-ACAPET-GR-03  ZMPBA  G/G       A/A
	S1689      Zmp-B-ACAPET-GR-04  ZMPBA  G/G       A/A

###transposing
```
	awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genoypes.txt
```

###after transposing
```
	cut -f1-5 transposed_teosinte_genotypes.txt | head -n 5 | column -t
```

	Sample_ID  S0881               S1057               S1087               S1689
	JG_OTU     Zmp-B-ACAPET-GR-01  Zmp-B-ACAPET-GR-02  Zmp-B-ACAPET-GR-03  Zmp-B-ACAPET-GR-04
	Group      ZMPBA               ZMPBA               ZMPBA               ZMPBA
	abph1.20   C/G                 C/G                 G/G                 G/G
	abph1.22   A/A                 A/A                 A/A                 A/A


#Data Processing
## For my data processing, I had to remove the headers to work out.
##I hope to be able to learn how to manipulate the files with headers on.

##Maize

Remove first 3 lines with header and sort with first column of sample id
```
tail -n +4 transposed_maize_genotypes.txt | sort -k1,1 > maize_geno_no_heder.txt
```

```
cut -f1-5 maize_geno_no_header.txt | head -n 10 | column -t  
```  

	abph1.20  C/G  C/G  C/C  C/G  
	abph1.22  A/A  A/A  A/A  A/A  
	ae1.3     T/T  T/T  T/T  G/T  
	ae1.4     G/G  G/G  G/G  A/G  
	ae1.5     C/C  C/T  C/C  C/T  
	an1.4     C/G  C/G  C/C  C/C  
	ba1.6     G/G  A/G  G/G  G/G  
	ba1.9     G/G  G/T  ?/?  G/G  
	bt2.5     C/C  C/C  C/C  C/C  
	bt2.7     ?/?  ?/?  G/G  G/G

```
tail -n +2 snp_position.txt | sort -k1,1 > snp_no_header.txt
```  

	abph1.20  5976  2  27403404  
	abph1.22  5978  2  27403892  
	ae1.3     6605  5  167889790  
	ae1.4     6606  5  167889682  
	ae1.5     6607  5  167889821  
	an1.4     5982  1  240498509  
	ba1.6     3463  3  181362952  
	ba1.9     3466  3  181362666  
	bt2.5     5983  4  66290049  
	bt2.7     5985  4  66290994

```
join -1 1 -2 1 -t $'\t' snp_no_header.txt maize_geno_no_header.txt > maize_snp_geno.txt
```

```
wc -l maize_snp_geno.txt 
```

983

```
awk -F "\t" '{print NF; exit}' maize_snp_geno.txt 
```

1588

```
cut -f1-10  maize_snp_geno.txt | head -n 10 | column -t
```

	abph1.20  5976  2  27403404   abph1  AB042260  abph1  candidate
	abph1.22  5978  2  27403892   abph1  AB042260  abph1  candidate
	ae1.3     6605  5  167889790  ae1    ae1       ae1    candidate
	ae1.4     6606  5  167889682  ae1    ae1       ae1    candidate
	ae1.5     6607  5  167889821  ae1    ae1       ae1    candidate
	an1.4     5982  1  240498509  an1    an1       an1    candidate
	ba1.6     3463  3  181362952  ba1    AY753892  ba1    candidate
	ba1.9     3466  3  181362666  ba1    AY753892  ba1    candidate
	bt2.5     5983  4  66290049   bt2    bt2       bt2    candidate
	bt2.7     5985  4  66290994   bt2    bt2       bt2    candidate

##Teosinte

Remove first line which is the header

```
tail -n +2 transposed_teosinte_genotypes.txt | sort -k1,1 > teosinte_geno_no_header.txt
```

```
cut -f1-5 teosinte_geno__no_header.txt | head -n 10 | column -t
```

	abph1.20  C/G  C/G  G/G  G/G
	abph1.22  A/A  A/A  A/A  A/A
	ae1.3     T/T  G/T  T/T  T/T
	ae1.4     G/G  A/G  G/G  G/G
	ae1.5     T/T  T/T  T/T  T/T
	an1.4     C/C  C/C  C/C  C/C
	ba1.6     A/G  A/A  A/A  A/A
	ba1.9     G/G  G/G  G/G  G/G
	bt2.5     T/T  C/T  C/T  C/C
	bt2.7     A/A  A/A  A/A  A/A


```
join -1 1 -2 1 -t $'\t' snp_no_header.txt teosinte_geno__no_header.txt > teosinte_snp_geno.txt
```

```
wc -l teosinte_snp_geno.txt 
```
983

```
awk -F "\t" '{print NF; exit}' teosinte_snp_geno.txt 
```

990

```
cut -f1-20 teosinte_snp_geno.txt | head -n 10 | column -t

```

	abph1.20  5976  2  27403404   abph1  AB042260  abph1  candidate  8393  10474  1  1  1  C/G  C/G  G/G  G/G  C/G
	abph1.22  5978  2  27403892   abph1  AB042260  abph1  candidate  8394  10475  0  0  0  A/A  A/A  A/A  A/A  A/A
	ae1.3     6605  5  167889790  ae1    ae1       ae1    candidate  8395  10477  1  1  1  T/T  G/T  T/T  T/T  T/T
	ae1.4     6606  5  167889682  ae1    ae1       ae1    candidate  8396  10478  0  0  0  G/G  A/G  G/G  G/G  G/G
	ae1.5     6607  5  167889821  ae1    ae1       ae1    candidate  8397  10479  0  0  0  T/T  T/T  T/T  T/T  T/T
	an1.4     5982  1  240498509  an1    an1       an1    candidate  8398  10481  1  1  1  C/C  C/C  C/C  C/C  C/C
	ba1.6     3463  3  181362952  ba1    AY753892  ba1    candidate  8399  10482  1  0  1  A/G  A/A  A/A  A/A  A/A
	ba1.9     3466  3  181362666  ba1    AY753892  ba1    candidate  8400  10483  0  0  0  G/G  G/G  G/G  G/G  G/G
	bt2.5     5983  4  66290049   bt2    bt2       bt2    candidate  8401  10486  1  1  1  T/T  C/T  C/T  C/C  C/C
	bt2.7     5985  4  66290994   bt2    bt2       bt2    candidate  8402  10487  0  0  0  A/A  A/A  A/A  A/A  A/A

#Processing
##maize

#create folder for maize output
    mkdir maize_output
Q1. 
Sort by position in increasing order ;

```
sort -t $'\t' -k3,3n -k4,4n maize_snp_geno.txt | awk -F '\t' '{print $0 > "./maize_output/maize_q1_"$3".txt"}'
```

Q2. Sort by position in reverse and replace ? with -;
```
sed 's/?/-/g' maize_snp_geno.txt | grep -v -E "(multiple|unknown)" |sort -t $'\t' -k3,3n -k4,4n | awk -F '\t' '{print $0 > "./maize_output/maize_q2_"$3".txt"}'
```

##The next two questions are answered in first Q1. Although I printed them here to show how the files look like

##unknowns

```
awk -F '\t' '$4 == "unknown"' maize_snp_geno.txt > maize_unknown_snp_position.txt
```

27 maize_unknown_snp_posiition.txt

```
cut -f1-10 maize_unknown_snp_posiition.txt | head -n 10 | column -t

```
	PZA00065.2   3537  unknown  unknown  PZA00065  AY106265  AY106265  random
	PZA00086.8   3643  unknown  unknown  PZA00086  AY106134  AY106134  random
	PZA00103.20  3655  unknown  unknown  PZA00103  AY110882  AY110882  random
	PZA00148.2   3691  unknown  unknown  PZA00148  AY105906  AY105906  random
	PZA00153.3   3555  unknown  unknown  PZA00153  AY105888  AY105888  random
	PZA00153.6   3694  unknown  unknown  PZA00153  AY105888  AY105888  random
	PZA00221.7   3747  unknown  unknown  PZA00221  AY109474  AY109474  random
	PZA00560.1   4022  unknown  unknown  PZA00560  AY112303  AY112303  random
	PZA00560.2   4023  unknown  unknown  PZA00560  AY112303  AY112303  random
	PZA02862.3   4246  unknown  unknown  PZA02862  AY104937  AY104937  random


##multiples
```
awk -F '\t' '$4 == "multiple"' maize_snp_geno.txt > maize_multiples_snp_position.txt
```

```
wc -l maize_multiples_snp_position.txt ```

11 maize_multiples_snp_position.txt

```
cut -f1-10 maize_multiples_snp_position.txt | head -n 10 | column -t
```

	PZA00296.6   3810   2  multiple  Chr2(209957515;210039173);  PZA00296  AY108561  AY108561  random
	PZA00486.2   3964   7  multiple  Chr7(108218425;108952795);  PZA00486  AY107392  AY107392  random
	PZA00493.1   11295  4  multiple  Chr4(241144995;241178306);  PZA00493  AY107331  AY107331  random
	PZA00493.2   11296  4  multiple  Chr4(241145101;241178412);  PZA00493  AY107331  AY107331  random
	PZA00493.5   11299  4  multiple  Chr4(241145151;241178462);  PZA00493  AY107331  AY107331  random
	PZA00589.10  7329   9  multiple  Chr9(43692524;43720849);    PZA00589  AY107035  AY107035  random
	PZA00589.8   4051   9  multiple  Chr9(43692603;43720928);    PZA00589  AY107035  AY107035  random
	PZA00589.9   4052   9  multiple  Chr9(43692633;43720958);    PZA00589  AY107035  AY107035  random
	PZA02948.19  4286   6  multiple  Chr6(25035262;25122959);    PZA02948  AY104537  AY104537  random
	PZA02948.21  4287   6  multiple  Chr6(25035303;25123000);    PZA02948  AY104537  AY104537  random

## Teosinte
#create folder for teosinte output
    mkdir teosinte_output
Q1.
Sort by position in increasing order ;
```
sort -t $'\t' -k3,3n -k4,4n teosinte_snp_geno.txt | awk -F '\t' '{print $0 > "./teosinte_output/teosinte_q1_"$3".txt"}'
```

Q2. Sort by position in reverse and replace ? with -;
```
sed 's/?/-/g' teosinte_snp_geno.txt | grep -v -E "(multiple|unknown)" |sort -t $'\t' -k3,3n -k4,4n | awk -F '\t' '{print $0 > "./teosinte_output/teosinte_q2_"$3".txt"}'
```

##The next two questions are answered in first Q1. Although I printed them here to show how the files look like


##unknowns
```
awk -F '\t' '$4 == "unknown"' teosinte_snp_geno.txt > teosinte_unknown_snp_position.txt```

```
wc -l teosinte_unknown_snp_position.txt ```
27 teosinte_unknown_snp_position.txt  

```
cut -f1-10 teosinte_unknown_snp_position.txt | head -n 10 | column -t```

	PZA00065.2   3537  unknown  unknown  PZA00065  AY106265  AY106265  random
	PZA00086.8   3643  unknown  unknown  PZA00086  AY106134  AY106134  random
	PZA00103.20  3655  unknown  unknown  PZA00103  AY110882  AY110882  random
	PZA00148.2   3691  unknown  unknown  PZA00148  AY105906  AY105906  random
	PZA00153.3   3555  unknown  unknown  PZA00153  AY105888  AY105888  random
	PZA00153.6   3694  unknown  unknown  PZA00153  AY105888  AY105888  random
	PZA00221.7   3747  unknown  unknown  PZA00221  AY109474  AY109474  random
	PZA00560.1   4022  unknown  unknown  PZA00560  AY112303  AY112303  random
	PZA00560.2   4023  unknown  unknown  PZA00560  AY112303  AY112303  random
	PZA02862.3   4246  unknown  unknown  PZA02862  AY104937  AY104937  random

##multiples
```
awk -F '\t' '$4 == "multiple"' teosinte_snp_geno.txt > teosinte_multiple_snp_position.txt
```

```
wc -l teosinte_multiple_snp_position.txt 
```
11 teosinte_multiple_snp_position.txt

```
cut -f1-10 teosinte_multiple_snp_position.txt | head -n 10 | column -t
```

	PZA00296.6   3810   2  multiple  Chr2(209957515;210039173);  PZA00296  AY108561  AY108561  random
	PZA00486.2   3964   7  multiple  Chr7(108218425;108952795);  PZA00486  AY107392  AY107392  random
	PZA00493.1   11295  4  multiple  Chr4(241144995;241178306);  PZA00493  AY107331  AY107331  random
	PZA00493.2   11296  4  multiple  Chr4(241145101;241178412);  PZA00493  AY107331  AY107331  random
	PZA00493.5   11299  4  multiple  Chr4(241145151;241178462);  PZA00493  AY107331  AY107331  random
	PZA00589.10  7329   9  multiple  Chr9(43692524;43720849);    PZA00589  AY107035  AY107035  random
	PZA00589.8   4051   9  multiple  Chr9(43692603;43720928);    PZA00589  AY107035  AY107035  random
	PZA00589.9   4052   9  multiple  Chr9(43692633;43720958);    PZA00589  AY107035  AY107035  random
	PZA02948.19  4286   6  multiple  Chr6(25035262;25122959);    PZA02948  AY104537  AY104537  random
	PZA02948.21  4287   6  multiple  Chr6(25035303;25123000);    PZA02948  AY104537  AY104537  random














