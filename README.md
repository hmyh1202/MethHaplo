## MethHaplo: Combining Allele-specific DNA Methylation and SNPs for Haplotype Region Identification 

DNA methylation is an important epigenetic modification that plays a critical role in most eukaryotic organisms. Parental alleles in haploid genomes may exhibit different methylation patterns, which can lead to different phenotypes and even different therapeutic and drug responses to diseases. However, to our knowledge, no software is available for the identification of DNA methylation haplotype regions. In this paper, we developed a new method, MethHaplo, that identify DNA methylation haplotype regions with allele-specific DNA methylation and single nucleotide polymorphisms (SNPs) from whole-genome bisulfite sequencing (WGBS) data. Our results showed that methylation haplotype regions were ten times longer than haplotypes with SNPs only. When we integrate WGBS and high-throughput chromosome conformation capture (Hi-C) data, MethHaplo could call even longer haplotypes. By constructing methylation haplotypes for various cell lines, we provide a clearer picture of the effect of DNA methylation on gene expression, histone modification and three-dimensional chromosome structure at the haplotype level. Our method could benefit the study of parental inheritance-related disease and heterosis in agriculture.

## This is a README file for the usage of MethHaplo.
------

## REQUIREMENTS
1. gcc (v4.8) , gsl library
2. SAMtools
3. Python3
4. Perl

## INSTALL
------
a) Download
`git clone https://github.com/ZhouQiangwei/MethHaplo.git`

b) Change directory into the top directory of MethHaplo
`cd MethHaplo`

c) Type
- make
- make install

d) The binary of MethHaplo will be created in current folder

## USAGE of MethHaplo
------
### Example data
You can found the test data in ./test dir.

### Citation:
[Zhou, Q., Wang, Z., Li, J. et al. MethHaplo: combining allele-specific DNA methylation and SNPs for haplotype region identification. BMC Bioinformatics 21, 451 (2020).](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-020-03798-7)

### Usage
#### 1. MethHaplo command
```
        MethHaplo: Combining Allele-specific DNA Methylation and SNPs for Haplotype Region Identification
        Usage: methHaplo -M [mode] -a Y/N -m methfile -s <sam>/-b <bam> -o outputprefix
        Options:
                -M <string> [hap|asm]         methHaplo analysis mode
                                                hap: iterative approach, prefer longer haplotype results;
                                                asm: hypergeometric approach, prefer accurate asm results.(default: hap);
                -m, --methfile <file>         methratio file (requires)
                                                format: chr  pos  strand  context methC  coverage  methlevel
                -o, --out <string>            output file prefix
                -s, --sam <samfile>           sam file from batmeth2-align.  This file should be coordinate sorted, 
                                                using the <samtools sort> command, and must contain methylstatus[MD:Z:].
                -b, --bam <bamfile>           bam file, should be coordinate sorted. (use this option or -s option but not both)
                -a <Y/N>                      If bam/sam file contain MD state by batmeth2 calmeth scripts.
                                                If not, please define genome location by -g paramater.
                -g, --genome <genome>         If bam/sam file isnot contain MD.
                -q <int>                      only process reads with mapping quality >= INT [default >= 20].
                -c, --context                 methylation context process for methHaplo. CG, CHG, CHH, ALL[default].
                -C, --NMETH                   Number of methylated reads cover cytosine site. default: 2 [m>=2]
                -N, --NCOVER                  Number of coverage reads in cytosine site. default: 6 [n >= 6]
                -f, --MFloat                  Cutoff of methratio. default: 0.1 [ f =< meth <= 1-f]
                --minIS <INT>                 Minimum insert size for a paired-end read to be considered as single fragment for phasing, default 0
                --maxIS <INT>                 Maximum insert size for a paired-end read to be considered as a single fragment for phasing, default 1000
                --DBtmpsize <INT>             Maximum size of temp read store, default 12000. (only useful in asm mode)
                --PE                          Paired-end reads.
                -v, --vcffile <file>          snp file (optional)
                -r, --chromosomal-order       Use natural ordering (1,2,10,MT,X) rather then the default (1,10,2,MT,X). 
                                                This requires new version of the unix \sort\ command which supports the --version-sort option.
                -p, --parallel <int>          Change the number of sorts run concurrently to <int>
                -t, --temporary-directory     Use a directory other than /tmp as the temporary directory for sorting.
                -h, -?, --help                This help message.
```

#### 2. Allele-specific DNA methylation region visualization
```bash
python methpoint.py align.md.sort.bam chrom:start-end strand outputprefix visulsort

        [align.md.sort.bam] BS-Seq alignment file for visualization.
        [chrom:start-end] The region in chromosome:start-end for visualization.
        [strand] visualization strand. [+/-/.]
        [outputprefix] output file prefix
        [visulsort] Methylation and Unmethylation position in the figure. [0/1]
```

<p align="center">
        <img src="scripts/asmexample.png" alt="asmexample"  width="550" height="350">
</p>

The figure above represents the distribution of methylation sites in raw reads, orange represents methylation sites, green represents unmethylation sites, and blue represents mutation base information. The bottom figure shows DNA methylation sites and methylation levels.

#### 3. Aellele-specific DNA methylation Sites distribution across TSS/TES etc.

##### 3.1 Caculate coverage across TSS/TES sites.

```bash
ASManno [options] -o <OUT_PREFIX> -G GENOME -gff <GFF file>/-gtf <GTF file>/-b <bed file> -ap <asm plus file> -an <asm neg file>
Usage:
	-o|--out         Output file prefix
	-G|--genome      Genome
	-ap|--asmplus    ASM plus file.
	-an|--asmneg     ASM neg file.
	-p|--pvale       Pvalue cutoff. default: 0.01
	-gtf|-gff        Gtf/gff file
	-b|--BED         Bed file, chrom start end (strand, .bed4 format)
	--ped            loci file, chrom start (strand, .ped3 format)
	-d|--distance    ASM distributions in body and <INT>-bp flanking sequences. The distance of upstream and downstream. default:2000
	-B|--body        For different analysis input format, gene/TEs body methylation level. [Different Methylation Gene(DMG/DMT...)]
	-P|--promoter    For different analysis input format.[Different Methylation Promoter(DMP)]
	-s|--step        Gene body and their flanking sequences using an overlapping sliding window of 5% of the sequence length at a step of 0.8% of the sequence length. So default step: 0.008 (0.8%)
	-h|--help
```

##### 3.2 Visualization

```bash
python methylevel.py Num Input1.Methylevel.1.txt [Input2 ...] lable outprefix
```
<p align="center">
        <img src="scripts/asmsites.png" alt="asmsite"  width="500" height="300">
</p>






