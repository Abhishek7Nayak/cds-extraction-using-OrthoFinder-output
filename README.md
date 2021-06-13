## CDS extraction

Tools such as HyPhy, PAML can be used for performing positive selection analysis but they require a codon alignment file of the respective single copy orthologs. Hence, the below strategy can be used to extract the CDS using OrthoFinder results and perform further analysis.

The orthgroups directory contains a file called Orthogroups_SingleCopyOrthologues.txt and also Orthogroups.tsv These two files can be used to extract the CDS.

``` 
$ i=$(cat Orthogroups_SingleCopyOrthologues.txt | xargs )
 $ for j in $i; do grep "$j" Orthogroups.tsv | awk '{for (i=2; i<=NF; i++) print $i}'> $j.txt; done;
```
The above command uses the orthogroups names and prints all the protein IDs of the respective orthogroups and saves them into a text file. These CDS sequences can be now downloaded from NCBI further changing the headers of the sequences to their respective protein IDs.

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
Seqkit grep can be used to extract the FASTA sequences from a FASTA file using a text file (containing protein IDs created in the previous step) which contains the FASTA headers. 

```
for txt_file in $(ls *.txt); do seqkit grep -f $txt_file all_cds_seq.fa -o $txt_file.fa; done;
```
The final orthogroup FASTA files contain all the cDNA sequences of the proteins.
These CDS containing orthogroups can be used for further analysis such as alignment using PRANK, filtering using G-Blocks and further positive selection analysis using HyPhy or PAML.

