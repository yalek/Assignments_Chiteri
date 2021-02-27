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


#Data processing
###sort snp with header on
	awk -F '\t' 'NR==1; NR>1 {print $0 | "sort -k1,1"}' snp_position.txt > snp_position_sorted.txt

###check no. of line
	wc -l snp_position_sorted.txt
	984 snp_position_sorted.txt

###check no of columns
	awk -F '\t' '{print NF; exit}' snp_position_sorted.txt
	15

###check file
	cut -f1-5 snp_position_sorted.txt | head -n 5 | column -t

	SNP_ID    cdv_marker_id  Chromosome  Position   alt_pos
	abph1.20  5976           2           27403404
	abph1.22  5978           2           27403892
	ae1.3     6605           5           167889790
	ae1.4     6606           5           167889682

###sort maize with header on
	awk -F '\t' 'NR==1; NR>3 {print $0 | "sort -k1,1"}' transposed_maize_genotypes.txt > maize_geno_sorted.txt

###check no. of lines
	wc -l maize_geno_sorted.txt
	984 maize_geno_sorted.txt

###check file
	cut -f1-5 maize_geno_sorted.txt | head -n 5 | column -t

	Sample_ID  ZDP_0752a  ZDP_0793a  ZDP_0612a  ZDP_0602a
	abph1.20   C/G        C/G        C/C        C/G
	abph1.22   A/A        A/A        A/A        A/A
	ae1.3      T/T        T/T        T/T        G/T
	ae1.4      G/G        G/G        G/G        A/G

###join the maize genotypes with the snp position

	join -1 1 -2 1 -t $'\t' --header snp_position_sorted.txt maize_geno_sorted.txt > maize_snp_geno.txt

###No of lines
	wc -l maize_snp_geno.txt
	984 maize_snp_geno.txt

###No of columns
	awk -F '\t' '{print NF; exit}' maize_snp_geno.txt
	1588

###check file
	cut -f1-5 maize_snp_geno.txt | head -n 5 | column -t

	SNP_ID    cdv_marker_id  Chromosome  Position   alt_pos
	abph1.20  5976           2           27403404
	abph1.22  5978           2           27403892
	ae1.3     6605           5           167889790
	ae1.4     6606           5           167889682

##maize questions
###make directory for maize output
	mkdir maize_output

##Q1

###sorting by chromosome number and snp position the spit separate files
	sort -t $'\t' -k3,3n -k4,4n maize_snp_geno.txt | awk -F '\t' '{print $0 > "./maize_output/maize_q1_"$3".txt"}'

###get into the maize output folder
	cd maize_output

###sample file maize_q1_Chromosome.txt file
	cut -f1-5 maize_q1_Chromosome.txt | head -n 5 | column -t

	SNP_ID  cdv_marker_id  Chromosome  Position  alt_pos

###sample maize_q1_1.txt file
	cut -f1-5 maize_q1_1.txt | head -n 5| column -t

	PZB00859.1   5634  1  157104
	PZA02962.13  4299  1  3205252
	PZA00393.1   3880  1  4175293
	PZA00393.4   6041  1  4175573
	PZA02869.8   4253  1  4429897


###Include headers to the above files adding a_prefix

	for i in $(seq 1 10); do cat maize_q1_Chromosome.txt maize_q1_"$i".txt > "./a_maize_q1_"$i".txt"; done

###sample file a_maize_q1_1.txt

	cut -f1-5 a_maize_q1_1.txt | head -n 5 | column -t

	SNP_ID       cdv_marker_id  Chromosome  Position  alt_pos
	PZB00859.1   5634           1           157104
	PZA02962.13  4299           1           3205252
	PZA00393.1   3880           1           4175293
	PZA00393.4   6041           1           4175573

###for multiple & unknown
	cat maize_q1_Chromosome.txt maize_q1_multiple.txt > a_maize_q1_multiple.txt

	cat maize_q1_Chromosome.txt maize_q1_unknown.txt > a_maize_q1_unknown.txt


## Q2. 
###Sort file in reverse order by snp position replacing missing data (?) with (-)

###Go back to main folder
	cd ..

###replace (?) with (-), sort by chromosome and position before giving separate files for each chromosome
	sed 's/?/-/g' maize_snp_geno.txt | grep -v -E "(multiple|unknown)" |sort -t $'\t' -k3,3n -k4,4n | awk -F '\t' '{print $0 > "./maize_output/maize_q2_"$3".txt"}'

###go maize folder
	cd maize_output

###replace headers
	for i in $(seq 1 10); do cat maize_q2_Chromosome.txt maize_q2_"$i".txt > "./b_maize_q2_"$i".txt"; done

### Remove anything with maize End up with 22 files
	rm m*


### q3 and q4 were answered with the code in Q1

#Teosinte

###cd into main folder

###sort teosinte with header on
	awk -F '\t' 'NR==1; NR>3 {print $0 | "sort -k1,1"}' transposed_teosinte_genotypes.txt > teosinte_geno_sorted.txt

###join the teosinte genotypes with the snp position

	join -1 1 -2 1 -t $'\t' --header snp_position_sorted.txt teosinte_geno_sorted.txt > teosinte_snp_geno.txt


###check file
	cut -f1-5 teosinte_snp_geno.txt | head -n 5 | column -t

	SNP_ID    cdv_marker_id  Chromosome  Position   alt_pos
	abph1.20  5976           2           27403404
	abph1.22  5978           2           27403892
	ae1.3     6605           5           167889790
	ae1.4     6606           5           167889682



##teosinte questions
###make directory for teosinte output
	mkdir teosinte_output

###sorting by chromosome number and snp position the spit separate files
	sort -t $'\t' -k3,3n -k4,4n teosinte_snp_geno.txt | awk -F '\t' '{print $0 > "./teosinte_output/teosinte_q1_"$3".txt"}'

###get into the maize output folder
	cd teosinte_output


###Include headers to the above files adding a_prefix

	for i in $(seq 1 10); do cat teosinte_q1_Chromosome.txt teosinte_q1_"$i".txt > "./a_teosinte_q1_"$i".txt"; done

##for multiple & unknown
	cat teosinte_q1_Chromosome.txt teosinte_q1_multiple.txt > a_teosinte_q1_multiple.txt

	cat teosinte_q1_Chromosome.txt teosinte_q1_unknown.txt > a_teosinte_q1_unknown.txt


## Q2. 
###Sort file in reverse order by snp position replacing missing data (?) with (-)

###Go back to main folder
	cd ..

###replace (?) with (-), sort by chromosome and position before giving separate files for each chromosome
	sed 's/?/-/g' teosinte_snp_geno.txt | grep -v -E "(multiple|unknown)" |sort -t $'\t' -k3,3n -k4,4n | awk -F '\t' '{print $0 > "./teosinte_output/teosinte_q2_"$3".txt"}'

###go maize folder
	cd teosinte_output

###replace headers
	for i in $(seq 1 10); do cat teosinte_q2_Chromosome.txt teosinte_q2_"$i".txt > "./b_teosinte_q2_"$i".txt"; done

### Remove anything with maize End up with 22 files
	rm t*










