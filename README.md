# EC Repository - Sheryl Francis

## Step 1: Conducting the BLAST Search
To find potential homologs of the gene XP_021351142.1, a BLAST search was conducted. A maximum e-value of 1e-35 was set to recover homologs with high sequence identities and alignments scores. Afterwards the top hit  was used to decipher the gene family of focus.
```
makeblastdb -in allprotein.fas -dbtype prot

ncbi-acc-download -F fasta -m protein XP_021351142.1

less XP_021351142.1.fa

blastp -db ../allprotein.fas -query XP_021351142.1.fa -outfmt 0 -max_hsps 1 -out mygene.blastp.typical.out

less mygene.blastp.typical.out

blastp -db ../allprotein.fas -query XP_021351142.1.fa -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle"  -max_hsps 1 -out mygene.blastp.detail.out

awk '{if ($6< 1e-35 )print $1 }' mygene.blastp.detail.out > mygene.blastp.detail.filtered.out
 
grep -o -E "^[A-Z][a-z]+\." mygene.blastp.detail.filtered.out  | sort | uniq -c
```
