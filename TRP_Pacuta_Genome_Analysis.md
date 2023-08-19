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

### Download human TRP channel sequences based on [Bhattacharya et al 2016](https://doi.org/10.7554/eLife.13288) elife-13288-fig2-data1-v1.docx

| Human TRP Channels | Present in Pdam? According to Bhattacharya et al | Refseq mRNA    | Refseq Prot    |
|--------------------|--------------------------------------------------|----------------|----------------|
| TRPA1              | X                                                | NM_007332.3    | NP_015628.2    |
| TRPV1              |                                                  | NM_018727.5    | NP_061197.4    |
| TRPV2              |                                                  | NM_016113.5    | NP_057197.2    |
| TRPV3              |                                                  | NM_001258205.2 | NP_001245134.1 |
| TRPV4              | X                                                | NM_001177428.1 | NP_001170899.1 |
| TRPV5              |                                                  | NM_019841.7    | NP_062815.3    |
| TRPV6              |                                                  | NM_018646.6    | NP_061116.5    |
| TRPM1              |                                                  | NM_001252020.2 | NP_001238949.1 |
| TRPM2              |                                                  | NM_001320350.2 | NP_001307279.2 |
| TRPM3              | X                                                | NM_001007470.3 | NP_001007471.1 |
| TRPM4              |                                                  | NM_001195227.2 | NP_001182156.1 |
| TRPM5              |                                                  | NM_014555.4    | NP_055370.1    |
| TRPM6              |                                                  | NM_001177310.2 | NP_001170781.1 |
| TRPM7              |                                                  | NM_001301212.2 | NP_001288141.1 |
| TRPM8              |                                                  | NM_001397606.1 | NP_001384535.1 |
| TRPC1              |                                                  | NM_001251845.2 | NP_001238774.1 |
| TRPC2              | p                                                | NR_002720.2    |                |
| TRPC3              |                                                  | NM_001130698.2 | NP_001124170.1 |
| TRPC4              | X                                                | NM_001135955.3 | NP_001129427.1 |
| TRPC5              | X                                                | NM_012471.3    | NP_036603.1    |
| TRPC6              | X                                                | NM_004621.6    | NP_004612.2    |
| TRPC7              |                                                  | NM_001167576.2 | NP_001161048.1 |


[NCBI Fasta Download](https://github.com/kblin/ncbi-acc-download)

```
pip install ncbi-acc-download
```

#TRP IDs

```
mkdir human_TRP
cd human_TRP

ncbi-acc-download --molecule protein --format fasta NP_015628.2 NP_061197.4 NP_057197.2 NP_001245134.1 NP_001170899.1 NP_062815.3 NP_061116.5 NP_001238949.1 NP_001307279.2 NP_001007471.1	NP_001182156.1 NP_055370.1 NP_001170781.1 NP_001288141.1 NP_001384535.1 NP_001238774.1 NP_001124170.1 NP_001129427.1 NP_036603.1 NP_004612.2 NP_001161048.1

cat * > human_TRP.fa
```

On personal computer:
```
scp  /Users/zoedellaert/Documents/URI/Genome_Analysis/human_TRP/human_TRP.fa zdellaert@ssh3.hac.uri.edu:/data/putnamlab/zdellaert/Genome_Analysis
```

On andromeda:

```
cd /data/putnamlab/zdellaert/Genome_Analysis
nano blast_TRP.sh
```

```
#!/bin/bash
#SBATCH --job-name="Pacuta_TRP_blast"
#SBATCH -t 240:00:00
#SBATCH --export=NONE
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=zdellaert@uri.edu #your email to send notifications
#SBATCH --mem=100GB
#SBATCH --error="blast_out_error"
#SBATCH --output="blast_out"
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/zdellaert/Genome_Analysis/
#SBATCH --nodes=1 --ntasks-per-node=20

module load BLAST+/2.9.0-iimpi-2019b

makeblastdb -in references/Pocillopora_acuta_HIv2.genes.pep.faa -out Pacuta_prot -dbtype prot

blastp -query human_TRP.fa -db Pacuta_prot -out TRP_results.txt -outfmt 6
```

```
sbatch blast_TRP.sh
```

On personal computer:

```
scp  zdellaert@ssh3.hac.uri.edu:/data/putnamlab/zdellaert/Genome_Analysis/TRP_results.txt /Users/zoedellaert/Documents/URI/Genome_Analysis/
```

#### Results



### Alt pipeline, Nucleotide version (not protein)

```
mkdir human_TRP_nt
cd human_TRP_nt

ncbi-acc-download --format fasta NM_007332.3	NM_018727.5	NM_016113.5	NM_001258205.2	NM_001177428.1	NM_019841.7	NM_018646.6	NM_001252020.2	NM_001320350.2	NM_001007470.3	NM_001195227.2	NM_014555.4	NM_001177310.2	NM_001301212.2	NM_001397606.1	NM_001251845.2	NR_002720.2	NM_001130698.2	NM_001135955.3	NM_012471.3	NM_004621.6	NM_001167576.2

cat * > human_TRP_nt.fa
```

> In geneious, performing a translation alignment of the 22 human sequences with the 66 Pacuta sequences.


Notes:

Use [single cell atlas](https://sebe-lab.shinyapps.io/Stylophora_cell_atlas/) to track down important marker genes + cell specificity of TRP channels

Good reference: https://www.nature.com/articles/srep08032
