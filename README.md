# blast_practice_data
The data here is made to accompany the following YouTube video:

https://youtu.be/1AzujLnr3RM 

## content
### new_speices.fasta
fasta file created with my fast++ tool, contains the entire genome of a hypothetical new speices 
### queries/mismatch
Used to demonstrate blast output format and statistics
### queries/short.fasta
Used to demonstrate the word size parameter
### queries/gapped.fasta
Used to demonstrate gapped alignments caused by INDELs.  Also demonstrate the `-gapextend` and `-gapopen` parameters
### queries/compliment.fasta
Used to show that BLAST finds reverse compliments
### queries/repeate.fasta
Used to show that blast filters out low complexity regions.  Also introduce `-dust` and `-soft_masking` parameters

## How To
### First we make a database using new_species.fasta
`makeblastdb -in new_species.fasta -dbtype nucl -out db/new_species` \
When we run the above command, we see some statistics printed to the terminal.  We also created a directory called db, this is where our database is stored. \
We can compare our query sequences agains the database, lets start by comparing `mismatch.fasta` \
`blastn -db db/new_species -query queries/mismatch.fasta > out` \ 
because we used the `>` operator to redirect our output to the `out` file, we do not see any output after running the above command.  When we examine the contents of `out` and scroll down we see one very long alignment that starts like: \
`
Query  1    GGCATCATCACGCCGTTATTTCATGTCTCAGGATACGTTCGCGGAGGAGTTAAACCTCTT  60
            ||| |||| ||||||| ||||||| |||||||||||||||||| ||||||||||||||||    
Sbjct  1    GGCTTCATTACGCCGTGATTTCATCTCTCAGGATACGTTCGCGAAGGAGTTAAACCTCTT  60
` 


