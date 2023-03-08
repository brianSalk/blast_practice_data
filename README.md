# Table of Contents

- [Content](#content)
  - [new_speices.fasta](https://github.com/brianSalk/blast_practice_data/blob/main/new_species.fasta)
  - [queries/mismatch](https://github.com/brianSalk/blast_practice_data/blob/main/queries/mismatch.fasta)
  - [queries/short.fasta](https://github.com/brianSalk/blast_practice_data/blob/main/queries/short.fasta)
  - [queries/gapped.fasta](https://github.com/brianSalk/blast_practice_data/blob/main/queries/gaps.fasta)
  - [queries/compliment.fasta](https://github.com/brianSalk/blast_practice_data/blob/main/queries/compliment.fasta)
  - [queries/repeate.fasta](https://github.com/brianSalk/blast_practice_data/blob/main/queries/repeat.fasta)
- [BLAST Installation](#blast-installation)
  - [The Easy Way](#the-easy-way)
  - [The Hard Way](#the-hard-way)
- [Clone This Repo](#clone-this-repo-to-your-computer)
- [Getting Started With Blast](#getting-started-with-blast)
  - [Make a Database Using new_species.fasta as a Reference](#make-a-database-using-new_speciesfasta-as-a-reference)
- [BLAST Output](#blast-output)
  - [BLAST Statistics](#blast-statistics)

# blast_practice_data
The data here is made to accompany the following YouTube video:

https://youtu.be/1AzujLnr3RM 

## content
### new_speices.fasta
fasta file created with my [fastcpp tool](https://github.com/brianSalk/fastcpp), contains the entire genome of a hypothetical new speices 
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
## BLAST installation
### The easy way
the easiest way to install blast is to use your package manager:
#### Debian/Ubuntu
```
sudo apt-get install ncbi-blast+
```
#### arch linux
```
sudo pacman -S blast+
```
### The hard way
visit [the ncbi-blast download page](https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/) and download the version for your hardware. \
once the download is complete, open your terminal and navigate to your `Downloads` directory and run 
```
tar -zxf <your_blast_file.tar.gz>
```
move the resulting directory to a perminant location on your computer \
add the following line to your `bashrc`, `zshrc` or other file that is sourced each time you open a terminal 
```
PATH="/path/to/your/blast/directory/bin:${PATH}"
```
## Clone this repo to your computer
on your computer, open a UNIX terminal and past the following command into your terminal:
```
git clone https://github.com/brianSalk/blast_practice_data.git; cd blast_practice_data
```
## Getting Started With Blast
Blast comes with many different commands.  This tutorial will serve as an introduction and as such will only require the use of two BLAST programs: \
`blastn` and `makeblastdb` \
`blastn` is used to align nucleotide queries from [fasta](https://zhanggroup.org/FASTA/) files to a database of nucleotides and `makeblastdb` is used to create our database. \
We need to create a database because BLAST is not designed to compare two fasta files directly.
### make a database using new_species.fasta as a reference
```
makeblastdb -in new_species.fasta -dbtype nucl -out db/new_species
``` 
When we run the above command, we see some statistics printed to the terminal.  We also created a directory called db, this is where our database is stored. 
## BLAST output
We can compare our query sequences agains the database, lets start by comparing `mismatch.fasta` \
`blastn -db db/new_species -query queries/mismatch.fasta > out` \ 
because we used the `>` operator to redirect our output to the `out` file, we do not see any output after running the above command.  When we examine the contents of `out` and scroll down we see one very long **sequence alignment** that begins like:
```
Query  1    GGCATCATCACGCCGTTATTTCATGTCTCAGGATACGTTCGCGGAGGAGTTAAACCTCTT  60 
            ||| |||| ||||||| ||||||| |||||||||||||||||| ||||||||||||||||    
Sbjct  1    GGCTTCATTACGCCGTGATTTCATCTCTCAGGATACGTTCGCGAAGGAGTTAAACCTCTT  60
```
The `|`s between the letters indicate a match, or *identity* and the letters not connected by a `|` do not match.  
### BLAST statistics
Above the sequence alignment we can see some statistics.  For this alignment we get the following:
```
Score = 339 bits (183)
```
This is the bit score, the bit score is a $log_2$ normalized number that tells us how many nucleotides there would need to be in our database in order for us to expect our query to match up with something in the database by chance.  The larger the bitscore, the better the match.  our bitscore was 339, which means that we would need a database of 2^339 = 1119872371088902105278721140284222139060822748617324767449994550481895935590080472690438746635803557888 base pares in order to expect to find a match as close as the one we have.
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
Gaps in alignments indicate insertions and deletions (INDELs) between the query and the database.
```
Strand=Plus/Plus
```
Due to the double-stranded nature of DNA, blast must also search not only for sequences that are similar, but also for reverse compliments that are similar. the Plus/Plus indicates that our match is not a reverse compliment.
## blast parameters
When we run `blastn -h` we get a list of all the parameters that we can use with blast. \
To get a more in depth list, run `blastn -help`
### -penalty
Lets go ahead and change the value to one of these parameters by running the following command:
```
blastn -db db/new_species -query queries/mismatch.fasta > out -penalty -5
```
Now go ahead and examine your `out` file.  Notice anything different?  You will see that most of the file looks similar except your evalue and bitsscores changed a bit (bit score is now 135 and evalue is 5e-75).  Because we specified a more harsh penalty, we have increased our evalue and decreased our bit score.  Notice that the identity percentage and gaps percentage did not change.  By decreasing the the argument to `-penatly` from it's default value of -3 to -5, we made BLAST more punitive.
### -gapopen, -gapextend
Now let's query our `gaps.fasta` file:
```
blastn -db db/new_species -query queries/gaps.fasta >| out
```
quick asside, I used `>|` instead of `>` because you might have your `noclobber` option set and `>` will not overwrite a file when `noclobber` is turned on. \
Anyway, When we examine the `out` file now, we see three relativly large gaps in our alignment.  Let's change the gap penalties and see how that changes the results. \
BLAST has two seperate gap penalties, a penalty to start a gap and a penalty to  continue a gap. \
Why are these parameters usefull?  Perhaps you know that an organism - for whatever biological reason - is unlikely to accrue insertion/deletions in its genome.  In that case you may way to penalize gaps heavier.  The opposite is true when you susspect that INDELs are common in an organism. \
let's set both of these to very high numbers and see what happens:
```
blastn -db db/new_species -query queries/gaps.fasta >| out -gapopen 100 -gapextend 100
```
notice that we use positive integers to increase gap penalties while mismatch penalties were expressed in negative numbers. \
When you examine `out` this time, you will see 4 ungapped alignments instead of one gapped alignment.  With such extreme penalties for gaps, BLAST decided that it was better to count each non-gapped segment as a small aligment. 

### -word_size
Now we will query our `short.fasta` file: 
```
blastn -db db/new_species -query queries/short.fasta >| out
```
Hmmmm... No matches this time.  Is that really correct?  let's use a different search tool - `grep` - to verify our findings. \
`grep` is a great search tool for pattern matching and exact word finding. \
Our queries/short.fasta file contains two sequences, they are **GCCGTGAT** and **GGCTTCATTACG** \
We can verify that they do not exist in our database by searching for them to the raw `new_species.fasta` file we used to create our database using grep.
run the following command to return all instances of **GCCGTGAT** and **GGCTTCATTACG**: 
```
grep -Eo 'GGCTTCATTACG|GCCGTGAT' < new_species.fasta
```
I won't go into detail about grep, but basically since BLAST found not hits, we would not expect `grep` to find any exact matches or the two sequences.  So what happened?  Why did BLAST not find matches but grep did? \
The answer is that BLAST uses a `-word_size` parameter.  Essentially, BLAST searches for exact matches that are of a certain minimum length called a word size.  This is one of several reasons why BLAST is able to perform so well.  By requiring long highly conserved regions, blast can avoid doing very computationally expensive operations on areas of the query that are unlikely to yeild significant results.  \
The longer the word size the less sensitive BLAST is, the shorter the word size, the longer it takes BLAST to complete an alignment.  It is therefor very important to set the word size appropriatly. \
Since our query sequences are in the database but did not show up, we can assume that our default word size is longer that the sequences in our query file. \
Let's see what happens if we set the word_size to 7:
```
blastn -db db/new_species -query queries/short.fasta >| out -word_size 7
```
Now we get serveral matches!  But look at the evalues.  Do you think an evalue of, say, 4.9 is very good?  If we just look at the identity percentage and ignore the bit score and evalue we might naivly think our results are significant.  The evalue and bit score tell us that our findings are likely to be coincidence and that we should be sceptical of any conclusions about the relationship between the query and the database.

### -strand
Run the following command:
```
blastn -db db/new_species -query queries/compliment.fasta >| out
```
When we examine the file, we see that we have a perfect match!  The identity percentage is 100, the bit score is incredible and the evalue is 0.0. \
So we would expect that if we search for any long stretch of the query in our `new_species.fasta` file we should find a match right? \
```
grep -Eo CATACTTCTAAGTACTCAAGACTGATGACTCTGACTACGCGCCAGCGGGCAGGAAATAGA < new_species.fasta
```
What??? Why are we not finding this in our `new_species.fasta`?!!  First BLAST failed to find matches that existed because they were too short and now BLAST finds very high scoring matches that do not exist in our database at all...  \
Remember that DNA is double stranded and that base pairs bind to compliments.  So not only does BLAST need to find close matches, but blast also needs to find close matches to **reverse compliments**.  We can verify that our query is a reverse compliment by looking at the `strand` part of our `out` file. \
```
Strand=Plus/Minus
```
If we do not want BLAST to report such matches (for whatever reason) we can use the `-strand` parameter:
```
blastn -db db/new_species -query queries/compliment.fasta >| out -strand=plus
```
now when we examine `out`, we do not find any hits.
## filtering
By default, BLAST filters out low complexity regions in your query.  This is good for two reasons: \
**1)** We do not typically care about alignments within low complexity sequences, many organisms contain sequences like **AACAACAACAACAAC...** and it is therefore not very interesting to find matches in such regions. \
**2)** repetative regions such as the aformentioned can overlap with large sections of the database, this could cause BLAST to create many alignments and can impact performance greatly. \
For our last part of this tutorial, run: 
```
blastn -db db/new_species -query queries/repeat.fasta >| out
```
This time I will save you the trouble of checking yourself and just tell you that the query sequence is in our database and is much longer than our default word size.  The reason we did not find a match is because our query sequence is a trinucleotide repeat of **AAT**, and is therefor filtered out by BLAST.\
If you would like to display matches such as the one above, you can use the following command:
```
blastn -db db/new_species -query queries/repeat.fasta -dust no -soft_masking false >| out
```
## the -outfmt option
the `-outfmt` option in blast will alter the way the alignment info is displayed after running your blast query.  
the syntax for the `-outfmt` option is is follows:  
```
blastn -db <DB> -query <QUERY> -outfmt N
```
where `N` is a number in the range (0,11)  
play around with this yourself to get a feel for how it works.  Here is a summery of what each output does.  
### 0 pairwise
this is the default output that we have been looking at so far  
### 1 query-anchored showing identities
identities (matching nucleotides/amino-acids) in the reference sequence are represented with `.`s.  Insertions are shown using special notation.
### 2 query-anchored no identities
same as **1**, except identities are not represented with `.`s.
### 3 flat query-anchored, show identities
identities are represented with `.`s. Deletions are shown instead of insertions (which is what makes it flat)
### 4 flat query-anchored, no identities
same as **3**, except identities are not represented with `.`s.
### 5 XML format
All the info is stored in XML format, making it easier for some downstream tools.
### 6 tabular
show summary information in tab seperated format. 
### 7 tabular with comment lines
same as **6**, except contains additional comments prefixed with `#`.
### 8 Text ASN.1
converts all info to ASN.1 format, which is usefull for certain downstream tools.
### 9 Binary ASN.1
Binary version of **8**
### 10 Comma-separated values
Comma seperated summary stats
### BLAST archive format (ASN.1)


#### For 6,7, and 10 the collumns are:
*    Query ID: the identifier of the query sequence.
*    Subject ID: the identifier of the subject sequence, which is the reference sequence in the database that was compared to the query sequence.
*    Percentage of identity: the percentage of identical residues between the aligned regions of the query and subject sequences.
*    Alignment length: the length of the aligned region of the query and subject sequences.
*    Mismatches: the number of mismatched residues between the aligned regions of the query and subject sequences.
*    Gap opens: the number of gap openings in the alignment.
*    Query start: the start position of the alignment in the query sequence.
*    Query end: the end position of the alignment in the query sequence.
*    Subject start: the start position of the alignment in the subject sequence.
*    Subject end: the end position of the alignment in the subject sequence.
*    E-value: the expectation value, which is a measure of the significance of the alignment. Lower E-values indicate more significant alignments.
*    Bit score: the bit score, which is a measure of the similarity of the alignment. Higher bit scores indicate more similar alignments.
