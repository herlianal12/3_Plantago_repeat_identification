# *Plantago ovata* repeat content

source: https://www.biostars.org/p/411101/ tutorial for repeat

**Main stages:**
1. Obtaining Dfam library
2. Building database using RepeatModeler
3. Filtering repeats
4. RepeatMasker

**Step 1. Obtaining Dfam library and updating RepeatMasker library**
```
wget https://www.dfam.org/releases/Dfam_3.2/families/Dfam.h5.gz
gunzip Dfam.h5.gz
mv Dfam.h5 share/RepeatMasker/Libraries/
```

**Step 2. Building database using RepeatModeler**
```
BuildDatabase -name Plantago_database -engine ncbi Plantago_ovata_ncbi.fasta
RepeatModeler -database Plantago_database -pa 20 -engine ncbi
```

**Step 3. Filtering repeats**
```
##Mining retrotransposon protein homologies
transposonPSI.pl uniprot_all.pep prot
awk ‘{if($0 ~ /^[^\/\/.*]/) print $5} uniprot_all.pep.TPSI.topHits | sort -u > accessions.list

##Removing TEs from proteome
gaas_fasta_removeSeqFromIDlist.pl -f uniprot_all.pep -l accessions.list -o proteins.filtered.fa

##Creating local database and blast-ing proteome against RepeatModeler library
makeblastdb -in proteins.filtered.fa -dbtype prot

blastx -db proteins.filtered.fa -query consensi.fa.classified -out blastx.out

##Removing hits from RepeatModeler library
ProtExcluder.pl blastx.out consensi.fa.classified
```

**Step 4. RepeatMasker**

```
RepeatMaster -xsmall -pa 20 -dir . -gff -lib consensi.fa.classifiednoProtFinal Plantago_ovata_ncbi.fasta
```

