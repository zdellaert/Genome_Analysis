# TRP Genome Analysis: P acuta

Script Written By: Dellaert
Last Updated: 20230816

## Download Data

Work will be done on URI server Andromeda

### Download [*Pocillopora acuta*](http://cyanophora.rutgers.edu/Pocillopora_acuta/) genome

Rutgers University Stephens et al. 2022 [Publication](https://academic.oup.com/gigascience/article/doi/10.1093/gigascience/giac098/6815755)

Obtain reference genome assembly and gff annotation file.

```
cd /data/putnamlab/zdellaert
mkdir Genome_Analysis
cd Genome_Analysis/
mkdir references/
cd references/ 

wget http://cyanophora.rutgers.edu/Pocillopora_acuta/Pocillopora_acuta_HIv2.assembly.fasta.gz

wget http://cyanophora.rutgers.edu/Pocillopora_acuta/Pocillopora_acuta_HIv2.genes.gff3.gz

wget http://cyanophora.rutgers.edu/Pocillopora_acuta/Pocillopora_acuta_HIv2.genes.cds.fna.gz

wget http://cyanophora.rutgers.edu/Pocillopora_acuta/Pocillopora_acuta_HIv2.genes.pep.faa.gz

gunzip Pocillopora_acuta_HIv2.assembly.fasta.gz #unzip genome file
gunzip Pocillopora_acuta_HIv2.genes.gff3.gz     #unzip gff annotation file
gunzip Pocillopora_acuta_HIv2.genes.cds.fna.gz  #unzip CDS file
gunzip Pocillopora_acuta_HIv2.genes.pep.faa.gz  #unzip Protein file
```

### Generate file containing genes of interest

On personal computer, download annotation file "Pocillopora_acuta_HIv2.genes.Conserved_Domain_Search_results.txt" from [Rutgers P. acuta genome website](http://cyanophora.rutgers.edu/Pocillopora_acuta/) (VERSION 2)

Using the "Short name", "Superfamily", and "Definition" Columns of the annotation file, filter for proteins of interest. I selected for proteins in the TRP channel superfamily by filtering for rows that contained "ELD_TRPML", "LSDAT_euk superfamily", "trp superfamily" and "TRPV superfamily". I narrowed down the list to these superfamilies by searching through the "Definition" column for TRP channels and TRP channel associated domains. This is a list of 72 rows, which once checking for duplicates contains 66 unique genes/queries.

Save the rows of interest in a new file, which I called "TRP_Pocillopora_acuta_HIv2.genes.Conserved_Domain_Search_results.csv"

Upload to andromeda:

```
scp  /Users/zoedellaert/Documents/URI/Genome_Analysis/TRP_Pocillopora_acuta_HIv2.genes.Conserved_Domain_Search_results.csv zdellaert@ssh3.hac.uri.edu:/data/putnamlab/zdellaert/Genome_Analysis
```

Take the first column (minus the header) from this file to have a list of gene names. 

```
cut -d ',' -f 1 TRP_Pocillopora_acuta_HIv2.genes.Conserved_Domain_Search_results.csv | tail -n +2 > Genes.txt
```

Use grep to create a file with the genes and their coding sequence (pep.faa = amino acid sequences, cds.fna = nucleotide sequences) by searching through the genome file

```
grep -A 1 -f Genes.txt references/Pocillopora_acuta_HIv2.genes.pep.faa > Genes.pep.faa
grep -A 1 -f Genes.txt references/Pocillopora_acuta_HIv2.genes.cds.fna > Genes.cds.fna
```



Submitted job to [Clustal Omega](https://www.ebi.ac.uk/Tools/services/web_clustalo/toolform.ebi) (just submitted the Genes.pep.faa file)

Results: [http://www.ebi.ac.uk/Tools/services/web/toolresult.ebi?jobId=clustalo-E20230817-184430-0562-85614196-p1m&analysis=tree]

![Screenshot%202023-08-17%20at%2014.00.08.png](https://github.com/zdellaert/Genome_Analysis/blob/main/Screenshot%202023-08-17%20at%2014.00.08.png?raw=true)

![Screenshot%202023-08-17%20at%2013.59.59.png](https://github.com/zdellaert/Genome_Analysis/blob/main/Screenshot%202023-08-17%20at%2013.59.59.png?raw=true)


Think that we should try to get more specific matches, and to combine the similar sequences into groups. Right now, these matches are all superfamily-level matches. It would be good to have a better idea of what seqeucnes are partial matches for each other, for example.

[CD-HIT](https://sites.google.com/view/cd-hit)

- Installation instructions: https://github.com/weizhongli/cdhit/wiki/2.-Installation
- I will use the version of CD-HIT that is already installed on andromeda, CD-HIT/4.8.1-GCC-11.3.0



```
interactive
module load CD-HIT/4.8.1-GCC-11.3.0

sed '/--/d' Genes.pep.faa > pep.fasta 
cd-hit -i pep.fasta -o db90 #90% identity
cd-hit -i pep.fasta -o db90 -c 0.6 -n 4 #60% identity

```

90% idenitiy (default) leads to 66 clusters (so each protein is "unique" enough to not cluster with any others)

60% idenitiy (default) leads to 60 clusters, so our dataset is pretty non-redundant...

Next steps then:

- Download human TRP channel sequences based on [Bhattacharya et al 2016](https://doi.org/10.7554/eLife.13288) elife-13288-fig2-data1-v1.docx

| Human TRP Channels | Present in Pdam? According to Bhattacharya et al |
|--------------------|--------------------------------------------------|
| TRPA1              | X                                                |
| TRPV1              |                                                  |
| TRPV2              |                                                  |
| TRPV3              |                                                  |
| TRPV4              | X                                                |
| TRPV5              |                                                  |
| TRPV6              |                                                  |
| TRPM1              |                                                  |
| TRPM2              |                                                  |
| TRPM3              | X                                                |
| TRPM4              |                                                  |
| TRPM5              |                                                  |
| TRPM6              |                                                  |
| TRPM7              |                                                  |
| TRPM8              |                                                  |
| TRPC1              |                                                  |
| TRPC2              | p                                                |
| TRPC3              |                                                  |
| TRPC4              | X                                                |
| TRPC5              | X                                                |
| TRPC6              | X                                                |
| TRPC7              |                                                  |


[NCBI Fasta Download](https://github.com/kblin/ncbi-acc-download)

```
pip install ncbi-acc-download
```

#TRP IDs
```
mkdir human_TRP
cd human_TRP

ncbi-acc-download --format fasta NM_007332.3	NM_018727.5	NM_016113.5	NM_001258205.2	NM_001177428.1	NM_019841.7	NM_018646.6	NM_001252020.2	NM_001320350.2	NM_001007470.3	NM_001195227.2	NM_014555.4	NM_001177310.2	NM_001301212.2	NM_001397606.1	NM_001251845.2	NR_002720.2	NM_001130698.2	NM_001135955.3	NM_012471.3	NM_004621.6	NM_001167576.2
```


Notes:

Use [single cell atlas](https://sebe-lab.shinyapps.io/Stylophora_cell_atlas/) to track down important marker genes + cell specificity of TRP channels

Good reference: https://www.nature.com/articles/srep08032
