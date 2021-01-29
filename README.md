# Plantago_repeat_content

https://www.biostars.org/p/411101/ tutorial for repeat
1.	Getting Dfam library, gunzip, mv into ?/share/RepeatMasker/Libraries/
Wget -c ?
Gunzip Dfam
mv Dfam? ?/share/RepeatMasker/Libraries/
cd ?/share/RepeatMasker/Libraried/
buildRMLibFromEMBL.pl Dfam > RepeatMasker.lib
makeblastdb -dbtype nucl -in RepeatMasker.lib
2.	De-novo -RepeatModeler
BuildDatabase -name <genome name> -engine ncbi <genome fasta>
RepeatModeler -database <genome name> -engine ncbi
Output= consensi.fa.classified
3.	Filtering repeats
1)	Mine (Retro-) Transposon protein Homologies
transposonPSI.pl <protein fasta> prot
output : <protein.fasta>.TPSI.topHits
collection of accession numbers with similarities to transposons can be generated
awk ‘{if($0 ~ /^[^\/\/.*]/) print $5} <proteins.fa>.TPSI.topHits | sort -u > accessions.list
2)	Remove TEs from proteome
gaas_fasta_removeSeqFromIDlist.pl
Fasta_removeSeqFromIDlist.pl -f <proteins.fasta> -l accessions.list -o proteins.filtered.fa
3)	Blast proteome against RepeatModeler library
Makeblastdb -in proteins.filtered.fa -dbtype prot
Blastx -db proteins.filtered.fa -query consensi.fa.classified -out blastx.out
4)	Remove hits from RepeatModeler library
ProtExcluder.pl blastx.out consensi.fa.classified
Output: consensi.fa.classifiednoProtFinal
