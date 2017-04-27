Task: access copy of data and view PBS script to run mothur  

1. log in to flux:  ```ssh -l kbenedek flux-login.arc-ts.umich.edu```  

2. log in to Duhaime lab's high memory nodes on flux: ```cd /scratch/duhaimem_fluxm/```  

3.  to access a zipped copy of the data: ```/scratch/duhaimem_fluxm/kbenedek/tara/mothur/Run_186_Duhaime_data```  
```
cd kbenedek
cd tara
cd mothur
cd Run_186_Duhaime_data
```  

4. to access and edit ```mothur.batch```  file:
      in ```/scratch/duhaimem_fluxm/kbenedek/tara/mothur```  type ```nano mothur.batch```  


# Mothur batch file

1. Tell mothur where to look for your input files (fastq and stability)
2. Tell mothur where to store the output files
3. Give mothur an alternative directory called tempdefault where it can look for files, that are not in your input directory. These files are typically the silva database files which are stored on Vincent's scratch flux location listed below. If you are running this script locally, you will need to change the path.

```set.dir(input=/scratch/duhaimem_fluxm/kbenedek/tara/mothur/Run_186_Duhaime_data)
set.dir(output=/scratch/duhaimem_fluxm/kbenedek/tara/mothur/output)
set.dir(tempdefault=/nfs/vdenef-lab/Shared/Ruben/databases_taxass)
set.dir(tempdefault=/scratch/duhaimem_fluxm/duhaimem/Database/ssu_rRNA/Mothur)
```   

#  Set processors based on the number of samples you have and whether you're using flux or fluxm
```make.contigs(file=stability.file, processors=30, summary.seqs(fasta=current)
```

#  Remove sequences with ambiguous bases and sequences that are not between 240-275 bp
```screen.seqs(fasta=current, group=current, summary=current, maxambig=0, maxlength=275, minlength=240, maxhomop=8)
summary.seqs(fasta=current)
```   

# It would take forever to align every sequence, so we find just the unique ones
```unique.seqs(fasta=current)
count.seqs(name=current, group=current)
```   

# Align sequences to the v4 region of the silva database
```align.seqs(fasta=current, reference=silva.seed_v123.pcr.align)
screen.seqs(fasta=current, count=current, start=1968, end=11550)
summary.seqs(count=current)
```  

# Filter sequences to remove overhangs at both ends and gaps
# After this we rerun unique.seqs because we might have created some new redundancies from the filtering
```filter.seqs(fasta=current, vertical=T, trump=.)
unique.seqs(fasta=current, count=current)
```   

# Precluster sequences allowing for up to 2 differences between sequences.
# The goal of this step is to eliminate sequences with errors due to sequencing technology by merging them with very similar sequences  
```pre.cluster(fasta=current, count=current, diffs=2)
```   

# Search for chimeras and remove them
```chimera.uchime(fasta=current, count=current, dereplicate=t)
remove.seqs(fasta=current, accnos=current)
summary.seqs(count=current)
```  

# Classify sequences with a bayesian classifier using the silva database
```classify.seqs(fasta=current, count=current, reference=silva.nr_v123.align, taxonomy=silv$
```  

# Remove things which are classified as Chloroplast, Mitochondria, unknown, or Eukaryota
```remove.lineage(fasta=current, count=current, taxonomy=current, taxon=Chloroplast-Mitochondria-unknown-Eukaryota)
summary.seqs(count=current)
```  

# Remove groups such as mock communities that you won't use for further analysis
```remove.groups(count=current, fasta=current, taxonomy=current, groups=Mock1-Mock2)
summary.seqs(count=current)
```  

# Cluster sequences into OTUs. To save memory/time we first split sequences by taxlevel 4 (order) then cluster from there. Read mothur wiki for more info.
```cluster.split(fasta=current, count=current, taxonomy=current, splitmethod=classify, taxlevel=4, cutoff=0.15)
```  

# Determine how many sequences are in each OTU, using a .03 similarity cutoff
```make.shared(list=current, count=current, label=0.03)
```  

# Generate a consensus taxonomy for each OTU
```summary.seqs(count=current)
```  

# Classify sequences with a bayesian classifier using the silva database
```classify.seqs(fasta=current, count=current, reference=silva.nr_v123.align, taxonomy=silva.nr_v123.tax, cutoff=60)
```  

# Remove things which are classified as Chloroplast, Mitochondria, unknown, or Eukaryota
```remove.lineage(fasta=current, count=current, taxonomy=current, taxon=Chloroplast-Mitochondria-unknown-Eukaryota)
summary.seqs(count=current)
```  

# Remove groups such as mock communities that you won't use for further analysis
```remove.groups(count=current, fasta=current, taxonomy=current, groups=Mock1-Mock2)
summary.seqs(count=current)
```  

# Cluster sequences into OTUs. To save memory/time we first split sequences by taxlevel 4 (order) and then cluster from there. Read mothur wiki for more info
```cluster.split(fasta=current, count=current, taxonomy=current, splitmethod=classify, taxlevel=4, cutoff=0.15)
```  

# Determine how many sequences are in each OTU, using a .03 similarity cutoff
```make.shared(list=current, count=current, label=0.03)
```  

# Generate a consensus taxonomy for each OTU
```classify.otu(list=current, count=current, taxonomy=current, label=0.03)
```  


5. to access and edit PBS script ```mothur.pbs```  
      in ```/scratch/duhaimem_fluxm/kbenedek/tara/mothur``` type ```nano mothur.pbs```  

Contents of ```mothur.pbs``` script:
```  
####  PBS preamble
#PBS -N Mothur.batch
#PBS -M kbenedek@umich.edu
#PBS -m abe

#PBS -l nodes=1:ppn=30,mem=750gb,walltime=72:00:00
#PBS -V

#PBS -A duhaimem_fluxm
#PBS -l qos=flux
#PBS -q fluxm
####  End PBS preamble

#  Show list of CPUs you ran on, if you're running under PBS
if [ -n "$PBS_NODEFILE" ]; then cat $PBS_NODEFILE; fi  

#  Change to the directory you submitted from
if [ -n "$PBS_O_WORKDIR" ]; then cd $PBS_O_WORKDIR; fi
pwd 

# Run Mothur
mothur mothur.batch
```  


      
