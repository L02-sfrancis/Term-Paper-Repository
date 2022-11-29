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

## Step 2: Conducting a Global Multiple Sequence Alignment in Muscle
A global multiple sequence alignment in muscle was performed for gene alignment and to ascertain homologous positions amongst the histone acetyltransferases. The following is using the directories and folders utilized during lab so change input and output files accordingly.
```
seqkit grep --pattern-file ~/labs/lab3-$MYGIT/mygene/mygene.blastp.detail.filtered.out ~/labs/lab3-$MYGIT/allprotein.fas > ~/labs/lab4-$MYGIT/mygene/mygene.homologs.fas

muscle -in ~/labs/lab4-$MYGIT/mygene/mygene.homologs.fas -out ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.fas

alv -kli  ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.fas | less -RS
 
alv -kli --majority ~/labs/lab4-$MYGIT/gqr/gqr.homologs.al.fas | less -RS
 
alv -ki -w 100 ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.fas | aha > ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.html
 
a2ps -r --columns=1 ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.html -o ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.ps

ps2pdf ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.ps ~/labs/lab4-$MYGIT/mygene/mygene.homologs.al.pdf
```

## Step 3: Conducting a Global Multiple Sequence Alignment in Muscle
This sequence alignment was then used to create a phylogenetic tree for the homologs, using IQ-Tree and VT + F + R6 as the three components to the model. The following is using the directories and folders utilized during lab so change input and output files accordingly.

java -jar ~/tools/Notung-3.0-beta/Notung-3.0-beta.jar -s ~/labs/lab5-$MYGIT/species.tre -g ~/labs/lab6-$MYGIT/mygene/mygene.homologs.al.mid.treefile --reconcile --speciestag prefix --savepng --events --outputdir ~/labs/lab6-$MYGIT/mygene/

nw_display ~/labs/lab5-$MYGIT/species.tre

grep NOTUNG-SPECIES-TREE ~/labs/lab6-$MYGIT/mygene/mygene.homologs.al.mid.treefile.reconciled | sed -e "s/^\[&&NOTUNG-SPECIES-TREE//" -e "s/\]/;/" | nw_display -

python2.7 ~/tools/recPhyloXML/python/NOTUNGtoRecPhyloXML.py -g ~/labs/lab6-$MYGIT/mygene/mygene.homologs.al.mid.treefile.reconciled --include.species

thirdkind -Iie -D 40 -f ~/labs/lab6-$MYGIT/gqr/gqr.homologs.al.mid.treefile.reconciled.xml -o  ~/labs/lab6-$MYGIT/gqr/gqr.homologs.al.mid.treefile.reconciled.svg
