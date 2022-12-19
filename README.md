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

## Getting Started With Blast
### First we make a database using new_species.fasta
`makeblastdb -in new_species.fasta -dbtype nucl -out db/new_species` \
When we run the above command, we see some statistics printed to the terminal.  We also created a directory called db, this is where our database is stored. \
We can compare our query sequences agains the database, lets start by comparing `mismatch.fasta` \
`blastn -db db/new_species -query queries/mismatch.fasta > out` \ 
because we used the `>` operator to redirect our output to the `out` file, we do not see any output after running the above command.  When we examine the contents of `out` and scroll down we see one very long *sequence alignment* that starts like:
```
Query  1    GGCATCATCACGCCGTTATTTCATGTCTCAGGATACGTTCGCGGAGGAGTTAAACCTCTT  60 
            ||| |||| ||||||| ||||||| |||||||||||||||||| ||||||||||||||||    
Sbjct  1    GGCTTCATTACGCCGTGATTTCATCTCTCAGGATACGTTCGCGAAGGAGTTAAACCTCTT  60
```
The `|`s between the letters indicate a match, or *identity* and the letters not connected by a `|` do not match.  Above the sequence alignment we can see some statistics.  For this alignment we get the following:
```
Score = 339 bits (183)
```
This is the bit score, the bit score is a log_2 normalized number that tells us how many nucleotides there would need to be in our database in order for us to expect our query to match up with something in the database by chance.  The larger the bitscore, the better the match.  our bitscore was 339, which means that we would need a database of 2^339 = 1119872371088902105278721140284222139060822748617324767449994550481895935590080472690438746635803557888 base pares in order to expect to find a match as close as the one we have.
```
Expect = 2e-95
```
The Expected value (AKA evalue) is the number of times we would expect to get a match this good by chance.  Lower evalues indicate better matches.  Our evalue for this alignment is 2e-95, which is very close to 0, meaning the match is almost certainly not just a coincidence.
```
Identities = 218/235 (93%)
```
As previously mentoined, identities are matches between the database and the query.  the length of our query match is 235 and 218 of the nucleotides match up with database.
```
Gaps = 2/235 (1%)
```
Gaps in alignments indicate insertions and deletions between the query and the database.
```
Strand=Plus/Plus
```
Due to the double-stranded nature of DNA, blast must also search not only for sequences that are similar, but also for reverse compliments that are similar. the Plus/Plus indicates that our match is not a reverse compliment.
## Tweaking the Parameters
When we run `blastn -h` we get a list of all the parameters that we can use with blast. \

