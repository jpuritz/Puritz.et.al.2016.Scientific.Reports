```sh
vcftools --vcf TotalRawSNPs.vcf --recode-INFO-all --minDP 5 --out TRSdp5 --recode
```

```sh
File contains 339032 entries and 241 individuals.
Applying Required Filters.
Filtering out Genotypes with Depth less than 5 and greater than 2.14748e+09
After filtering, kept 241 out of 241 Individuals
After filtering, kept 339032 out of a possible 339032 Sites
Outputting VCF file...

```

```sh
vcftools --vcf TRSdp5.recode.vcf --recode-INFO-all --maf 0.01 --geno 0.5 --out TRSdp5g5 --recode
```

```sh
File contains 339032 entries and 241 individuals.
Applying Required Filters.
Filtering sites by allele frequency and call rate
After filtering, kept 241 out of 241 Individuals
After filtering, kept 47226 out of a possible 339032 Sites
Outputting VCF file... Done

```

```sh
bash filter_missing_ind.sh TRSdp5g5.recode.vcf TRSdp5MI
```

```sh
                                       Histogram of % missing data per individual

  14 +-+-------------------+---------------------+----------------------+---------------------+-------------------+-+
     +                     +                     +                      +                     +                     +
     |               ++   ++                                  'totalmissing' using (bin($1,binwidth)):(1.0) +-----+ |
  12 +-+             ||+  ||                                                                                      +-+
     |               |||  ||                                                                                        |
     |               |||  ||                                                                                        |
     |               |||  ||                                                                                        |
  10 +-+           +-+||  ||                                                                                      +-+
     |        ++   | |||+++|   ++                                                                                   |
     |        ||   | |||||||   ||                                                                                   |
   8 +-+      ||   | |||+|||   ||+                                                                                +-+
     |      ++||   | |||||||   ||| ++                                                                               |
     |      ||||   | |||||||   ||| ||                                                                               |
   6 +-+++  ||||+++| |||||||   |||++|                                                                             +-+
     |  ||  |||||||| |||||||   ||||||                                                                               |
     |  ||  |||||||| |||||||   ||||||                          ++                                                   |
   4 +-+||  ||||+||| |||||||++ ||||||+   ++                    ||                                                 +-+
     |  ||  |||||||| ||||||||| |||||||   ||                    ||                                                  ||
     |  || ++|+||||| ||||||||| |||+|||   ||      +-+   ++      ||                            +-+                   ||
     |  || ||||||||| ||||||||| |||||||   ||      | |   ||      ||                            | |                   ||
   2 +-++| ||||||||| ||||||||| ||||||| +-+| ++ ++| | +-+|+-+-+ ||     +-+       +-+   +-+    | |-+               ++-+
     | |||-+|||||||+ |||||||+|-+||||||++ ||++|-+|+ |++ ||+ | |-+|-+-+-+ |+++-+-++ |-+-+ |-+--+ | |-----+------+-++|+|
     + ||| ||||||||| ||||||+|| ||||||||| ||||| ||+ ||| ||| | | || | | | +||| | || | | | | |  |+| |     |      | ||||+
   0 +-+-------------------+---------------------+----------------------+---------------------+-------------------+-+
     0                    0.2                   0.4                    0.6                   0.8                    1
                                                    % of missing data

The 85% cutoff would be 0.606213
```

```sh
yes
0.58
```

```sh
Applying Required Filters.
Excluding individuals in 'exclude' list
After filtering, kept 205 out of 241 Individuals
After filtering, kept 47226 out of a possible 47226 Sites
Outputting VCF file... Done
Run Time = 34.00 seconds
```

```sh
pop_missing_filter.sh TRSdp5MI.recode.vcf popmap 0.25 0 TRSdp5MIp25
```

```sh
Reading Index file.
File contains 47226 entries and 205 individuals.
Applying Required Filters.
Filtering sites by include/exclude positions files
After filtering, kept 205 out of 205 Individuals
After filtering, kept 10337 out of a possible 47226 Sites
Outputting VCF file... Done
```

```sh
vcftools --vcf TRSdp5MIp25.recode.vcf --recode-INFO-all --maf 0.01 --geno 0.9 --out TRSdp5MIp25g9 --recode
```

```sh
File contains 10337 entries and 205 individuals.
Applying Required Filters.
Filtering sites by allele frequency and call rate
After filtering, kept 205 out of 205 Individuals
After filtering, kept 9724 out of a possible 10337 Sites
Outputting VCF file... Done
Run Time = 7.00 seconds
```

```sh
dDocent_filters TRSdp5MIp25g9.recode.vcf TRSdp5MIp25g9
```

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

```sh
vcfallelicprimitives -k -g TRSdp5MIp25g9.FIL.recode.vcf | sed 's:\.|\.:\.\/\.:g' > TRSdp5MIp25g9.prim
vcftools --vcf TRSdp5MIp25g9.prim --recode-INFO-all --recode --out SNP.TRSdp5MIp25g9 --remove-indels
```

```sh
File contains 8536 entries and 205 individuals.
Applying Required Filters.
Filtering sites by allele type
After filtering, kept 205 out of 205 Individuals
After filtering, kept 7902 out of a possible 8536 Sites
Outputting VCF file... Done
```

```sh
filter_hwe_by_pop.pl -v SNP.TRSdp5MIp25g9.recode.vcf -p popmap -c 0.5 -o SNP.TRSdp5MIp25g9HWE
```

```sh
Outputting results of HWE test for filtered loci to 'filtered.hwe'
Kept 7865 of a possible 7902 loci (filtered 37 loci)
```

```sh
vcftools --vcf SNP.TRSdp5MIp25g9HWE.recode.vcf --recode-INFO-all --out SNP.TRSdp5MIp25g9HWE2a --recode --max-alleles 2
```

```sh
File contains 7865 entries and 205 individuals.
Applying Required Filters.
Filtering sites by number of alleles
After filtering, kept 205 out of 205 Individuals
After filtering, kept 7781 out of a possible 7865 Sites
Outputting VCF file... Done

```

```sh
rad_haplotyper -v SNP.TRSdp5MIp25g9HWE2a.recode.vcf -p popmap -r reference.fasta -x 10 -mp 5
```

```sh
cp stats.out stats.out.HF
mawk '/Missi/' stats.out.HF | mawk '$9 > 30' > HF.missing
mawk '/para/' stats.out.HF > HF.para
remove.bad.hap.loci.sh HF.loci.tofilter SNP.TRSdp5MIp25g9HWE.recode.vcf
mawk '$5 > 0.999' MBDP5list | mawk '$4 > 0.1' | mawk '!/Loc/' | cut -f1,2 >BADLOCI
vcftools --vcf SNP.TRSdp5MIp25g9HWE.filtered.vcf --exclude-positions BADLOCI --recode-INFO-all --maf 0.05 --out SNP.TRSdp5MIp25g9HWEHFmaf05 --recode
```

```sh
File contains 7481 entries and 205 individuals.
Applying Required Filters.
Filtering sites by include/exclude positions files
Filtering sites by allele frequency and call rate
After filtering, kept 205 out of 205 Individuals
After filtering, kept 3705 out of a possible 7481 Sites
```

```sh
java -jar /usr/local/bin/PGDSpider2-cli.jar -spid BS.spid -inputfile SNP.TRSdp5MIp25g9HWEHFmaf05.recode.vcf -outputfile SNP1.BS
```

```sh
WARN  12:05:36 - PGDSpider configuration file not found! Loading default configuration.
initialize convert process...
read input file...
read input file done.
write output file...
write output file done.
```

```sh
BayeScan2.1_linux64bits SNP1.BS -thin 100 -nbp 30
```

```sh
VCFtoOutlierOnly.sh SNP.TRSdp5MIp25g9HWEHFmaf05.recode.vcf SNP_fst.txt 0.1 SNPTRSdp5MIp25g9HWEHFmaf05
```

Visually examine outliers

```sh
cat Visuallist BADLOCI > TotalBadSNPLOCI
vcftools --vcf SNP.TRSdp5MIp25g9HWE.filtered.vcf --exclude-positions TotalBadSNPLOCI --recode-INFO-all --recode --out SNP.TRSdp5MIp25g9HWEHFv
```

```sh
File contains 7481 entries and 205 individuals.
Applying Required Filters.
Filtering sites by include/exclude positions files
After filtering, kept 205 out of 205 Individuals
After filtering, kept 7382 out of a possible 7481 Sites
Outputting VCF file... Done
```

```sh
vcftools --vcf SNP.TRSdp5MIp25g9HWEHFv.recode.vcf --recode-INFO-all --max-alleles 2 --out SNP.TRSdp5MIp25g9HWEHFv2a --recode
```

```sh
File contains 7382 entries and 205 individuals.
Applying Required Filters.
Filtering sites by number of alleles
After filtering, kept 205 out of 205 Individuals
After filtering, kept 7303 out of a possible 7382 Sites
Outputting VCF file... Done
```

```sh
rad_haplotyper.pl -v SNP.TRSdp5MIp25g9HWEHFv2a.recode.vcf -p popmap -r reference.fasta -x 15 -mp 5 -g HapDP5HFv2 
```

Filtered 100 loci below missing data cutoff

Filtered 3 possible paralogs

Filtered 0 loci with low coverage or genotyping errors

Filtered 0 loci with an excess of haplotypes

Writing Genepop file: HapDP5HFv2

```sh
cp stats.out stats.out.HFv
```

Now run Bayescan and remove outliers with q value below 0.1







