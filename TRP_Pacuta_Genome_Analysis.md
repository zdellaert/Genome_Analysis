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



Use single cell atlas to track down important marker genes 

https://sebe-lab.shinyapps.io/Stylophora_cell_atlas/

