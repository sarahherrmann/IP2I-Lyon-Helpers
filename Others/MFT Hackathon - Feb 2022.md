[TOC]

# MFT Hackathon - Feb 2022

Agenda: https://indico.cern.ch/event/1127453/

* Part 1: Quick overview of MFT Software Documentation and how to contribute to Assessment workflows
* Part 2: Hand's on
    * Machine Learning
    * MFT reconstruction on the grid


## MFT Software Documentation

Tiki: https://twiki.cern.ch/twiki/bin/view/ALICE/MftPhysics#Software_Documentation

What is new? [**Readme on GitHub**](https://github.com/AliceO2Group/AliceO2/blob/dev/Detectors/ITSMFT/MFT/README.md) with details of MFT and Matching workflows.

:::info
Everyone is welcome to improve and update the documentation! TWiki, Alice-talk wiki post, READMEs. 
:::

#### How to contribute to assessment workflows

Class `o2::mft::MFTAssessment` defines what is executed by the assessment workflow, as defined in [`MFTAssessment.h`](https://github.com/AliceO2Group/AliceO2/blob/dev/Detectors/ITSMFT/MFT/assessment/include/MFTAssessment/MFTAssessment.h) and [`MFTAssessment.cxx`](https://github.com/AliceO2Group/AliceO2/blob/dev/Detectors/ITSMFT/MFT/assessment/src/MFTAssessment.cxx). 

**`void runASyncQC(o2::framework::ProcessingContext& ctx)`** is the main function called by the assessment workflow which receives and prepare all input data and loops over objects available at typical asynchronous reconstruction of MFT data producing ASynchronous QC task histograms. Other functions specific for processing generated data and MC labels are:
- `void processTrackables()`
- `void processGeneratedTracks()`
- `void processRecoAndTrueTracks()`

##### Adding histograms
For each histogram, in order:
* In `MFTAssessment.h`
    1. Add unique pointers to new histograms as private data members of the class
* In `MFTAssessment.cxx`
    1. Each histogram has to be properly created, loaded, saved and reset. Ensure to add them to the following functions:
        1. `createHistos()` creates the histograms which are filled in data collection step
        2. `loadHistos()` loads the histograms stored in a file
        3. `getHistos()` provides the histograms to an external application
        4. `reset()` clears all histograms. Necessary for QC.
    6. Fill the histogram at one of the data processing functions listed on the previous section. 

:::info
Global Forward Assessment worfklow follow the same logic.
:::


## Muon Matching with machine learning

* Slides by Satoshi: https://indico.cern.ch/event/1130140/contributions/4742978/attachments/2392135/4089591/0216_syano_MFT.pdf
* Machine learning code: https://github.com/MFT-MCHMatching/GlobalMuonTracking/tree/master/ML
* PbPb @5.02 TeV production with prompt and non-prompt J/Psi are done at forward rapidity, with ML training root file: 
`LHC22b2a - run 310005 (non-prompt J/Psi):` https://alimonitor.cern.ch/catalogue/?path=/alice/sim/2022/LHC22b2a/310005
`LHC22b2b - run 310005 (prompt charmonia):` https://alimonitor.cern.ch/catalogue/?path=/alice/sim/2022/LHC22b2b/310005
For technical details, JIRA ticket: 
https://alice.its.cern.ch/jira/browse/O2-2773

### Steps 

to be detailed during the hackathon

![](https://codimd.web.cern.ch/uploads/upload_c8f32b104e17d761be2da594494e8699.png)


## MFT Reconstruction on the Grid

The procedure outlined here uses the following files for running MFT standalone reconstruction from CTF files on the grid:

* `getFileList.py`: Produces XML file with list of input data
* `reco.jdl`: Describes the job: environment, node requirements, inputs, outputs, file dependencies...
* `reco.sh`: Runs the reconstruction
* `reco_validation.sh`: Validates job execution

Reconstruction-specific files are also needed such as dictionaries, geometry files and grp objects. At the moment this is to be provided by the user.

A copy of the files are available on the grid at  `/alice/cern.ch/user/p/pezzi/global-commissioning/MFTHackathon/reco.sh`
Alimonitor link: https://alimonitor.cern.ch/catalogue/?path=/alice/cern.ch/user/p/pezzi/global-commissioning/MFTHackathon

See also: [Tutorial: Grid Certificate](https://alice-doc.github.io/alice-analysis-tutorial/start/cert.html).

### Prepare file list

Adapt `getFileList.py`:

* Ensure `workDir` the line bellow points to your working directory on the grid
    * `workDir = "{}/global-commissioning/".format(getUserHome(user)) + workDir`
    * Working directory example: `/alice/cern.ch/user/p/pezzi/global-commissioning/`` 

* Select the run: year, period, runNumber
* Run `getFileList.py` on your local computer
    * `$ python3 getFileList.py -r 507574 -y 2022 -p JAN -u pezzi`
    * Produce XML file with input file list on the grid:
        * `/alice/cern.ch/user/p/pezzi/global-commissioning/2022/JAN/507574/507574.xml`

Comment: the directory `global-commissioning` will be created automatically if it doesn't exist.
To copy file from local computer to the grid: `alien_cp file:./local_file /path_on_the_grid`

### Reconstruction script

`reco.sh` is used to produce the input file list to be provided to the O2 workflow and execute the reconstruction command.

### Validation script

`reco_validation.sh` ensures all files expected to be produced by the workflow are present. Also parses log files for fatal errors.

### JDL

`reco.jdl` must be adapted to point to your reconstruction working directory on the grid. Also some paths are hard coded such as the year of the run.

The example file needs 3 arguments in this order: runNumber, MONTH, and TTL of the job in seconds.

* `Split = "file";` in the example configures the master job to create one subjob per CTF file. This can be changed using 
* `Split = "se";`
* `SplitMaxInputFileNumber = "1000";`


### Submitting the job
Connect to the grid with alien:
* `$ alien.py`
* Submit the job
    * Syntax: `submit <JDL File> <runNumber> <MONTH> <TTL_in_Seconds>`
    * Example: 
        * `submit /alice/cern.ch/user/p/pezzi/global-commissioning/reco.jdl 507574 JAN 3600` 
* Check job status on alimonitor: https://alimonitor.cern.ch/users/jobs.jsp
