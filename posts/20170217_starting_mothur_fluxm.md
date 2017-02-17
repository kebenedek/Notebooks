TASK: log in to high memory flux nodes (flumx)  


1. log in to flux: ```ssh -l kbenedek flux-login.arc-ts.umich.edu```  
2. log in to high memory nodes on flux: ```cd /scratch/duhaime_fluxm/```
  This is where we do the data processing/running mothur. We will need to move our sequence files here to process them. But we don't want to save our raw data or our results here. The scratch space on the high memory nodes is deleted every 90 days if not being used.
3. hui
