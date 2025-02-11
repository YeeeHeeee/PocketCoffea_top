 # Workflow Commands:
 Build datasets metadata - Run the processor - Plot:
 
### Activate the environment:
```bash
micromamba activate pocket-coffea
```
### Check the certificate:
on lx06:
```bash
source /vols/grid/cms/setup.sh
voms-proxy-init --rfc --voms cms -valid 192:00
```
on lxplus:
```bash
voms-proxy-init --rfc --voms cms -valid 192:00
```
### Find the sample:
```bash
dasgoclient -query="dataset dataset=/SUSYGluGluToHToTauTau*/*102X*/MINIAODSIM"
```
The first section is of the dataset name, is the typical physics process name, the second part is the CMS software used to produce the file and the the final part is the data tier (MiniAOD/NanoAOD). 102X: major version 10, minor version 2 and any revision version.

### Run the codes:
```bash
pocket-coffea run --cfg example_config.py --full -o output --skip-bad-files
```
Submit to condor:
```bash
pocket-coffea run --cfg example_config.py  --executor condor@ic  -o output_condor --scaleout=100 --skip-bad-files
```

### Merge outputs:
For submitting to condor, the data are splitted to be proessed. Then it loses the metadata and so it is needed to add it back when combine. 
```bash
pocket-coffea merge-outputs -o output_condor/output_all.coffea -jc jobs-dir/job/jobs_config.yaml output_condor/output_job_*.coffea
```
To skip the missing files, added ```--ignore-missing```

### Plots 
```bash
cd output_condor
pocket-coffea make-plots -i output_all.coffea --cfg parameters_dump.yaml -o plots -op plotting_style.yaml
```

### Check the condor availability:
```bash
/vols/cms/tr1123/condor_tools/condor_stat.py
```

# Useful Commands:
1. Find a file:
    ```bash
    find . -name "build_datasets.py"
    ```
2. Grant execute permission:
   ```bash
   chmod +x ./pocket_coffea/scripts/dataset/build_datasets.py
   ```
3. Check environment paths:
   ```bash
   micromamba env list
   ```
4. Check the roots file existance
    ```bash
    xrdfs root://cmsdcadisk.fnal.gov
    ls /dcache/uscmsdisk/store/data/Run2018D/SingleMuon/NANOAOD/UL2018_MiniAODv2_NanoAODv9-v1/280000/
    ```
5. Check the path of the library
   ```bash
    python -c "import coffea.processor; print(coffea.processor.__file__)"
    ```
6. Remove jobs before submit to Condor
   ```bash
   rm -r ./jobs-dir/job
   ```
7. Count files:
   ```bash
   ls output_condor/output_job_*.coffea | wc -l
   ```
  
- [Join](https://mattermost.web.cern.ch/cms-exp/channels/pocketcoffea---qa) the Q&A CMS Mattermost channel for technical questions


