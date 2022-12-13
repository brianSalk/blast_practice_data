# blast_practice_data
The data here is made to accompany the following YouTube video:

https://youtu.be/1AzujLnr3RM 

##content
###new_speices.fasta
fasta file created with my fast++ tool, contains the entire genome of a hypothetical new speices \ 
###queries/mismatch
Used to demonstrate blast output format and statistics
###queries/short.fasta
Used to demonstrate the word size parameter
###queries/gapped.fasta
Used to demonstrate gapped alignments caused by INDELs.  Also demonstrate the `-gapextend` and `-gapopen` parameters
###queries/compliment.fasta
Used to show that BLAST finds reverse compliments
###queries/repeate.fasta
Used to show that blast filters out low complexity regions.  Also introduce `-dust` and `-soft_masking` parameters
