TASK: log in to high memory flux nodes (flumx) and run mothur following the tutorial written by Michelle Berry, found here: http://deneflab.github.io/flux-tools/lessons/04-running-mothur.html


1. log in to flux: ```ssh -l kbenedek flux-login.arc-ts.umich.edu```  
2. log in to high memory nodes on flux: ```cd /scratch/duhaimem_fluxm/```  
  This is where we do the data processing/running mothur. We will need to move our sequence files here to process them. But we don't want to save our raw data or our results here. The scratch space on the high memory nodes is deleted every 90 days if not being used.  
3. go into personal directory ```cd kbenedek```  
4. go into project directory tara/mothur  
   ```cd tara```  
   ```cd mothur```  
   This is where I will do data processing and run mothur.
5. copy the script to run mothur from the DenefLab repositor on GitHub: ```wget https://github.com/DenefLab/flux-tools/raw/gh-pages/scripts/mothur.batch```  
6. using ```vi``` edit the input, output, and tempdefault locations on the text file mothur.batch  
  ```vi mothur.batch```  
  Tempdefault signifies a location that mothur will search for file that are not in the input directory. This is a good place to leave your databases for alignment/taxonomy.
7. press "escape" and "i" to edit lines  
  ```set.dir(input=/scratch/duhaimem_fluxm/kbenedek/tara/mothur)```  
  ```set.dir(output=/scratch/duhaimem_fluxm/kbenedek/tara/mothur)```  
  ```set.dir(tempdefault=/scratch/duhaime_fluxm/duhaime/Database/ssu_rRNA/Mothur)```   
8. using ```vi``` edit the file argument, processors, reference argument, reference and taxonomy arguments, and groups  

  ```make.contigs(file=Tara.stability.file, processors=30)```  
  
  This file has not yet been created, but will change the file argument to a file that contains all the sample names and links to the forward and reverse reads  
  
  ```align.seqs(fasta=current, reference=silva.seed_v123.pcr.align)```  
  This will change the reference argument to the name of the file you want to align to  
  
  ```classify.seqs(fasta=current, count=current, reference=silva.nr_v119.pcr.v4.align, taxonomy=silva.nr_v119.tax, cutoff=60)```  
  This will change the reference and taxonomy arguments to the files you are using for your analysis. Leave this the same because we need an accurate species list to compare the newly sequenced data too.  
  
9. press "escape" and type ```:wq``` to save changes to text file and exit  

 
