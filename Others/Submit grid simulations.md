
# GRID simulations

## How to submit jobs to the GRID ? 

### 1. Write a JDL.jdl
The JDL file is giving multiple information to the GRID, such as (non exaustive list):
- `User` The user that launches the sim
- `Packages` The O2sim tag used
- `Executable` the bash file that creates the directory of the files, submits the `SplitArguments` command in each directory etc (can be kept as such)
- `InputFile` The input files necessary for the jobs to be executed
- `SplitArguments` The central command line to produce the simulations
- `ValidationCommand` bash script to check if we have the expected outputfiles, if there were errors etc (can be kept as such)
- `OutputDir` where to save the outputs (multiple directories containing AO2D.root files for instance) - be sure to have write access
- `Output` The output files that need to be saved (if not found, gives an error ERROR_S) 
- `TTL` The time limit for each job, the job will be stopped and labeled expired if its runs for longer
- `Price` is an indicator of the importance of a job


Example of JDL used to produce LHC21k6 simulation [here](https://alimonitor.cern.ch/jobs/jdl.jsp?pid=2720861945)

You can click on each input file to access their location, and then see the file. The most important file is the file that launches the O2sim, namely `LF:/alice/cern.ch/user/a/aliprod/LHC21k6/O2DPG_pp_minbias_v4_NoAnalysisQC_v2.sh`

JDL expert: Catalin Ristea

### 2. Submit your job on the grid

Upload your jdl in your user space on the grid (I do it through the online alimonitor browser), and then :

`alienv enter O2/latest`
`alien.py`


then `submit /alice/cern.ch/user/s/sherrman/sim/JDL_dndeta_anchored_LHC22o.jdl`


```shell=
Submitting /alice/cern.ch/user/s/sherrman/sim/JDL_dndeta_anchored_LHC22o.jdl
Your new job ID is 2803257303
```

Keep the job ID in case you want to kill it (if it doesn't work)

`kill 2803257303`

You can look at the progress of your job [here](https://alimonitor.cern.ch/users/jobs.jsp)

### 3. Grid quotas

You can have a look at your grid quotas [here](https://alimonitor.cern.ch/users/quotas.jsp)
You have a maximum number of unfinished jobs, a maximum running time etc

### 4. Troubleshooting

In the Output, you have to write only the things that will exist and be saved, for instance if you write mfttracks.root and this file is not produced by your .sh file, it will complain `ERROR_S` (when you click on the details of the job only)

The version of O2sim used is also critical, some versions simply do not work. It must also be quite recent.

---
## Upload a file to CCDB

Link to do so: https://alimonitor.cern.ch/ccdb/upload.jsp

## Retrieve a list of file names from alien

`alien_find /alice/data/2022/LHC22o/528232/apass2/0000/ AO2D.root`

Or after `alien.py` 
`find /alice/data/2022/LHC22o/528232/apass2/0000/ AO2D.root`

You can then copy paste that output to a .txt file and use it as input for your analysis. Just check that there is no merged AO2D files to avoid double counting


## Code examples

### 1. Retrieve files from alien fast

```bash
#!/bin/bash

alienHomeDir=/alice/cern.ch/user/s/sherrman/JpsiPythia
inputDir=$1
outDir=$2
maxThreads=$3

mkdir -p $outDir/
echo "Downloading files from the folder $alienHomeDir/${inputDir}/"
alien_ls $alienHomeDir/${inputDir}/ > $outDir/chunkList.txt
nchunks=`cat $outDir/chunkList.txt | wc -l`
echo "Copying $nchunks files from grid"
chunkList=`cat $outDir/chunkList.txt`

for chunk in $chunkList; do
  while [ $(ps -ef | grep alien_cp | wc -l) -gt $maxThreads ]; do
    sleep 5s;
  done

  mkdir -p $outDir/$chunk
  echo "Copy chunk $chunk"
  #checks if the file already exists, if not downloads it
  if [ -s $outDir/$chunk/pythiaJpsiHadron_new_oniaAll.root ]
    then
       echo "    -->  Chunk exists already"
    else
       alien_cp alien://$alienHomeDir/${inputDir}/${chunk}/pythiaJpsiHadron_new_oniaAll.root file:$outDir/${chunk}/pythiaJpsi.root  &
  fi
done
```

Usage: 
`bash copySimuFromGrid.sh jpsiOutput_02 jpsiOut_02 100` 
It will fetch the content of `alienHomeDir/inputDir`, more specifically in this case the files called `pythiaJpsiHadron_new_oniaAll.root` and put them it in `./outputDir`

