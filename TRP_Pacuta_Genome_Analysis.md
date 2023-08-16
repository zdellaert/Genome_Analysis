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

gunzip Pocillopora_acuta_HIv2.assembly.fasta.gz #unzip genome file
gunzip Pocillopora_acuta_HIv2.genes.gff3.gz #unzip gff annotation file
```

