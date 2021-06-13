## CDS extraction

To perform positive selection analysis tools such as HyPhy , PAML requires a codon alignment file of single copy orthologs. So the below strategy can be used to extract the CDS sequences using orthofinder results and perform further analysis.

The orthgroups directory contains a file called Orthogroups_SingleCopyOrthologues.txt and also Orthogroups.tsv These two files can be used to extract the CDS sequences.

``` 
$ i=$(cat Orthogroups_SingleCopyOrthologues.txt | xargs )
 $ for j in $i; do grep "$j" Orthogroups.tsv | awk '{for (i=2; i<=NF; i++) print $i}'> $j.txt; done;
```
The above command uses the orthogroups names and prints all the protein ids of those orthogroups and saves them into a text file. 
Now the CDS sequences can be downloaded from ncbi and headers of the sequences should be changed to the protein id.
### Example for RefSeq ids and Ensemble ids
```
 sed 's/ /_/g' GCF_014176215.1_mRouAeg1.p_cds_from_genomic.fna  | sed 's/\(>\).*_\[protein_id=/\1/g' | sed 's/\].*$//g' | awk '{ if ($1 ~ /^>/)  print $0 "_Rousettus_aegyptiacus ";  else print $0; }' 
```
```
sed 's/ /_/g' Equus_caballus.EquCab3.0.cds.all.fa | sed '/>/s/\(>\).*[0-9]_gene:/\1/g' | sed 's/\_.*$//g' | awk '{ if ($1 ~ /^>/)  print $0 "_Equus_caballus";  else print $0; }' 
```
### Now merge all the CDS sequences in to a single file
```
cat * > all_cds_seq.faa
```
Seqkit grep can be used to extract the fasta sequences from a fasta file using a text file which contains the fasta headers. Here the text file is the one which contains all the protein ids which was created in the previous step.
```
for txt_file in $(ls *.txt); do seqkit grep -f $txt_file all_cds_seq.fa -o $txt_file.fa; done;
```
The final orthogroup fasta files contain all the CDNA sequences of the proteins.
These cDNA containing orthogroups can be used for further analysis such as alignment using PRANK and filtering using GBlocks and further positive selection analysis using HyPhy or PAML.
 
