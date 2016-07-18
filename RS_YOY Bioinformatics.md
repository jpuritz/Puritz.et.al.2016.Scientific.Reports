#Filter initial Variant Calls
* Start with raw vcf file and change all genotypes with less than 5 reads to missing data     
   This inital calls can be found [here](https://dx.doi.org/10.6084/m9.figshare.3490226)  
      The file needs to be unzipped before starting this workflow.
```sh
vcftools --vcf TotalRawSNPs.vcf --recode-INFO-all --minDP 5 --out TRSdp5 --recode
```   

* Now filter out all variants that are present below a minor allele frequency of 1% and are not called in at least 50% of samples

```sh
vcftools --vcf TRSdp5.recode.vcf --recode-INFO-all --maf 0.01 --geno 0.5 --out TRSdp5g5 --recode
```
   This should change the number of variant calls from ~339,000 to ~47,000

* Now use a custom script called [filter_missing_ind.sh](https://github.com/jpuritz/dDocent/blob/master/scripts/filter_missing_ind.sh) to filter out bad individuals (there were a lot in this data set).
```sh
bash filter_missing_ind.sh TRSdp5g5.recode.vcf TRSdp5MI
```

   At the prompt, enter `yes` and a custom cutoff of `0.85`
```sh
yes
0.58
```
   The newly filtered VCF file should have 205 individuals, down from 241.

* Now use a second custom script [pop_missing_filter.sh](https://github.com/jpuritz/dDocent/blob/master/scripts/pop_missing_filter.sh) to filter loci that have high missing data values in a single population.  You will need a file that maps individuals to populations [popmap](./popmap)
```sh
pop_missing_filter.sh TRSdp5MI.recode.vcf popmap 0.25 0 TRSdp5MIp25
```
   After this step, the new vcf file should have ~10,337 sites left

* Next, filter sites again my MAF, and filter out any sites with less than 90% overall call rate
```sh
vcftools --vcf TRSdp5MIp25.recode.vcf --recode-INFO-all --maf 0.01 --geno 0.9 --out TRSdp5MIp25g9 --recode
```
   This should leave about 9,700 variants
* Next, use a third custom filter script [dDocent_filters](https://github.com/jpuritz/dDocent/blob/master/scripts/dDocent_filters)

```sh
dDocent_filters TRSdp5MIp25g9.recode.vcf TRSdp5MIp25g9
```
   Below is the included output:
```sh
This script will automatically filter a FreeBayes generated VCF file using criteria related to site depth,
quality versus depth, strand representation, allelic balance at heterzygous individuals, and paired read representation.
The script assumes that loci and individuals with low call rates (or depth) have already been removed. 

Contact Jon Puritz (jpuritz@gmail.com) for questions and see script comments for more details on particular filters 

Number of sites filtered based on allele balance at heterozygous loci, locus quality, and mapping quality / Depth
 1392 of 9724 

Number of additional sites filtered based on overlapping forward and reverse reads
 98 of 8332 

Is this from a mixture of SE and PE libraries? Enter yes or no.
`no`
Number of additional sites filtered based on properly paired status
 78 of 8234 

Number of sites filtered based on high depth and lower than 2*DEPTH quality score
 140 of 8156 



                                             Histogram of mean depth per site

  200 +-+-+---+---+---+---+---+---+--+---+---+---+---+---+---+---+---+---+---+---+---+---+--+---+---+---+---+---+-+-+
      +   +   +   +   +   +   +   +  +   +   +   +   +   +   +++ +   +   +   +   +   +   +  +   +   +   +   +   +   +
  180 +-+                                              +++'meandepthpersite' using (bin($1,binwidth)):(1.0) +-----+-+
      |                                                ||| + ||| +++                                                |
      |                                        ++    ++||| |+||| |||                                                |
  160 +-+                                      ||    ||+|| |||||++||                                              +-+
      |                                        ||   +|||||++|+||||||  ++                                            |
  140 +-+                                    ++||  ++|||||||||||||||+++|                                          +-+
      |                                      ||||  |||||||||||||+||||+||+                                           |
  120 +-+                                   ++|+| ++|+|||||||||||||||||||++                                       +-+
      |                                     ||||| |||||||||||||||||||||||||                                         |
  100 +-+                                   |||||++||||||||||||||||||||||+|  ++                                   +-+
      |                                ++   |||||||||||||||||||||||||||||||  || ++                                  |
      |                                ||   |||||||||||||||||||||||||||||||  || ||                                  |
   80 +-+                              ||  ++||||||||||||||||||||||||||||||++|| ||                                +-+
      |                               ++|+ ||||||||||||||||||||||||||||||||||+| ||+                                 |
   60 +-+                             ||||++||||||||||||||||||||||||||||||||||| |||                               +-+
      |                             ++||||||||||||||||||||||||||||||||||||||||| |||+ +                              |
   40 +-+                         ++|||||||||||||||||||||||||||||||||||||||||||++|||+|++                          +-+
      |                           +|+|+||||||||||||||||||||||||||||||||||||||||||||||+||+                           |
      |                         ++||||||||||||||||||||||||||||||||||||||||||||||||||||+||+  +++                     |
   20 +-+                   ++++||+|||||||||||||||||||||||||||||||||||||||||||||||||||||||+++||++ +++             +-+
      +   +   +   +   + +++++|+|+|+||+|||+|||+|||+|||+|||+|||+|||+|||+|||+|||+|||+|||+|||+|++||++++|++-+++++++  + +++
    0 +-+-+---+---+---+---+---+---+--+---+---+---+---+---+---+---+---+---+---+---+---+---+--+---+---+---+---+---+-+-+
      10  15  20  25  30  35  40  45 50  55  60  65  70  75  80  85  90  95 100 105 110 115120 125 130 135 140 145 150
                                                        Mean Depth

If distrubtion looks normal, a 1.645 sigma cutoff (~90% of the data) would be 289.45825122
The 95% cutoff would be 124
Would you like to use a different maximum mean depth cutoff than 124, yes or no
```

```sh
no
```

```sh
Number of sites filtered based on maximum mean depth
 416 of 8156 

Total number of sites filtered
 1984 of 9724 

Remaining sites
 7740 

Filtered VCF file is called Output_prefix.FIL.recode.vcf

Filter stats stored in TRSdp5MIp25g9.filterstats
```
* Now, we need to break complex mutational events (combinations of SNPs and INDELs) into sepearte SNP and INDEL calls, and then remove INDELs.
```sh
vcfallelicprimitives -k -g TRSdp5MIp25g9.FIL.recode.vcf | sed 's:\.|\.:\.\/\.:g' > TRSdp5MIp25g9.prim
vcftools --vcf TRSdp5MIp25g9.prim --recode-INFO-all --recode --out SNP.TRSdp5MIp25g9 --remove-indels
```
   ~7900 SNPs should remain after this step.
* Next, filter out loci that are out of HWE in more than half the populations, using [filter_hwe_by_pop.pl](https://github.com/jpuritz/dDocent/blob/master/scripts/filter_hwe_by_pop.pl) written by [Chris Hollenbeck](https://github.com/chollenbeck)
```sh
filter_hwe_by_pop.pl -v SNP.TRSdp5MIp25g9.recode.vcf -p popmap -c 0.5 -o SNP.TRSdp5MIp25g9HWE
```
   This steps should remove ~37 SNPs, leaving approximately 7,865 SNPs.

* Restrict SNPs to loci only with 2 alleles.
```sh
vcftools --vcf SNP.TRSdp5MIp25g9HWE.recode.vcf --recode-INFO-all --out SNP.TRSdp5MIp25g9HWE2a --recode --max-alleles 2
```
* Use [rad_haplotyper](https://github.com/jpuritz/WinterSchool.2016/blob/master/Exercises/Day%201/rad_haplotyper.pl) written by [Chris Hollenbeck](https://github.com/chollenbeck)
```sh
rad_haplotyper -v SNP.TRSdp5MIp25g9HWE2a.recode.vcf -p popmap -r reference.fasta -x 10 -mp 5
```
   This script uses called genotypes and aligned reads to make haplotype calls across RAD loci using both F and R reads.

* Move output and create a list of files that had high levels of missing data and potential paralogs
```sh
cp stats.out stats.out.HF
mawk '/Missi/' stats.out.HF | mawk '$9 > 30' > HF.missing
mawk '/para/' stats.out.HF > HF.para
cat HF.para HF.missing > HF.loci.tofilter
```
*  Remove these loci entirely from the data set
```sh
remove.bad.hap.loci.sh HF.loci.tofilter SNP.TRSdp5MIp25g9HWE.recode.vcf
```
*  Remove loci that show potential bias by library (see this [poster](https://figshare.com/articles/Puritz_PAGXIII_Poster_1025/1287474/3) for more details.
   In short, one locality was randomly broken up across libraries.  To test for potentially biased loci, Lositan was run on these locality subets.  Outlier loci are treated as potentially biased.  The code below creates a list of loci with an FST > 0.1 and a 99.9% chance of being an outlier directly from Lositan output, called [MDP5list](./MBDP5list), and them removes them from the vcf file and filters all loci by a MAF of 5%.
```bash
mawk '$5 > 0.999' MBDP5list | mawk '$4 > 0.1' | mawk '!/Loc/' | cut -f1,2 >BADLOCI
vcftools --vcf SNP.TRSdp5MIp25g9HWE.filtered.vcf --exclude-positions BADLOCI --recode-INFO-all --maf 0.05 --out SNP.TRSdp5MIp25g9HWEHFmaf05 --recode
```

   ~3,700 loci should remain.
* Use PGDSpider to convert SNPs to BayeScan input file.

```sh
java -jar /usr/local/bin/PGDSpider2-cli.jar -spid BS.spid -inputfile SNP.TRSdp5MIp25g9HWEHFmaf05.recode.vcf -outputfile SNP1.BS
```
* Run BayeScan (This is to search for more potentially biased loci).
```sh
BayeScan2.1_linux64bits SNP1.BS -thin 100 -nbp 30
```
* Create a VCF file of SNPs of outliers with a false discovery rate of 10% using [VCFtoOutlierOnly.sh](https://github.com/jpuritz/dDocent/blob/master/scripts/untested/VCFtoOutlierOnly.sh)
```sh
VCFtoOutlierOnly.sh SNP.TRSdp5MIp25g9HWEHFmaf05.recode.vcf SNP_fst.txt 0.1 SNPTRSdp5MIp25g9HWEHFmaf05
```
* Visually examine outliers and create a list of outliers that are likely due to artifacts such as:
   *Bad alignments
   *Bad quality scores
   *paralogs
   Our list is here: [Visuallist](./Visuallist)
      Note: This list was compiled across mutlipe iterations of this data set
```sh
cat Visuallist BADLOCI > TotalBadSNPLOCI
```
* Remove loci from data set
```sh
vcftools --vcf SNP.TRSdp5MIp25g9HWE.filtered.vcf --exclude-positions TotalBadSNPLOCI --recode-INFO-all --recode --out SNP.TRSdp5MIp25g9HWEHFv
```
   This should leave about 7,382 SNPs.
```cp SNP.TRSdp5MIp25g9HWEHFv.recode.vcf Final_Filtered_SNPs.vcf```
   Find SNP.TRSdp5MIp25g9HWEHFv.recode.vcf [here](https://dx.doi.org/10.6084/m9.figshare.3490232)
* Again, remove SNPs with more than 2 alleles for halpotyping, and run rad_haplotyper again.
```sh
vcftools --vcf SNP.TRSdp5MIp25g9HWEHFv.recode.vcf --recode-INFO-all --max-alleles 2 --out SNP.TRSdp5MIp25g9HWEHFv2a --recode
rad_haplotyper.pl -v SNP.TRSdp5MIp25g9HWEHFv2a.recode.vcf -p popmap -r reference.fasta -x 15 -mp 5 -g Final_Haps.gen 
```
Find Final_Haps.gen [here](./https://dx.doi.org/10.6084/m9.figshare.34902290 or here (./Final_Haps.gen).









