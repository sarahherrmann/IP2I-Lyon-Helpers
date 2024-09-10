MFT Hackathon - Session 1 and 5
===============================

**06 and 08 December 2021**

## Forward Tracks in ALICE

### Types of tracks
![](https://codimd.web.cern.ch/uploads/upload_748d335fa4ac49f6d954c505ae65ae6b.png)

See also [MFT and Global Muon Definitions](https://twiki.cern.ch/twiki/pub/ALICE/MftPhysics/MFT_and_Global_Muon_Definitions_V_1.0_-_April_13th_2021.pdf).

### Coordinate system of forward tracks

Forward tracks near the center of the ALICE detector are represented by helix trajectories.

![](https://codimd.web.cern.ch/uploads/upload_0777c2b099f954f5677996d7ccde3f53.png)

More details on [Transformations between MFT and MCH coordinate systems](https://twiki.cern.ch/twiki/pub/ALICE/MftPhysics/MFT_MCH_Coordinate_system_conversions_V0.1_-_July14th2020.pdf). For updates check the [MFT Software and Physics TWiki page](https://twiki.cern.ch/twiki/bin/view/ALICE/MftPhysics).

## Data Classes

### class MatchInfoFwd: 

Class [`MatchInfoFwd`](https://github.com/AliceO2Group/AliceO2/blob/dev/DataFormats/Reconstruction/include/ReconstructionDataFormats/MatchInfoFwd.h ) stores matching relations of matched forward tracks. 

![](https://codimd.web.cern.ch/uploads/upload_a693e5112b752c2276ed4dc1cf7f0dde.png)

### class TrackParFwd

Class [`TrackParFwd`](https://github.com/AliceO2Group/AliceO2/blob/dev/DataFormats/Reconstruction/include/ReconstructionDataFormats/TrackFwd.h) stores forward track parameters and provides methods usefull for tracking and anaysis. 

![](https://codimd.web.cern.ch/uploads/upload_fa53133ed9cf5e9480dfdace526252f1.png)

:::info
**Task 1.1:** Create an instance for a forward track and propagate it:

1) Enter O2 with `alienv enter O2/latest-dev-o2` and run `root.exe`
2) Run the following code
```
o2::track::TrackParFwd myTrack;
myTrack.setTanl(-10.)
myTrack.setInvQPt(1.)
myTrack.getX()
myTrack.getY()
myTrack.getZ()
myTrack.getPhi()
myTrack.getEta()
myTrack.getPt()
myTrack.propagateParamToZhelix(-50., 5)
myTrack.getX()
myTrack.getY()
myTrack.getZ()
myTrack.getPhi()
myTrack.getEta()
myTrack.getPt()
```
:::


### class TrackParCovFwd : public TrackParFwd

Class [`TrackParCovFwd`](https://github.com/AliceO2Group/AliceO2/blob/dev/DataFormats/Reconstruction/include/ReconstructionDataFormats/TrackFwd.h) adds covariance matrix and reconstruction methods to `TrackParFwd`

![](https://codimd.web.cern.ch/uploads/upload_71e0747bb59b610734b15a7005a0a08a.png)




### class GlobalFwdTrack : public o2::track::TrackParCovFwd, public o2::dataformats::MatchInfoFwd

Class [GlobalFwdTrack](https://github.com/AliceO2Group/AliceO2/blob/dev/DataFormats/Reconstruction/include/ReconstructionDataFormats/GlobalFwdTrack.h) combines `TrackParCovFwd` and `MatchInfoFwd` and can represent any forward matched track in O2.


## MFT Standalone Workflows


```
# 0) Enter O2 environment
alienv enter O2/latest-dev-o2

# 1) Run MFT standalone simulation of 10 pp events
o2-sim -n 10 -g pythia8pp -m MFT

# 2) Run MFT digitizer: produces mftdigits.root
o2-sim-digitizer-workflow -b 

# 3) Run MFT reconstruction workflow from digits
#    produces mftclusters.root and mfttracks.root
o2-mft-reco-workflow  --configKeyValues "MFTTracking.forceZeroField=false;MFTTracking.LTFclsRCut=0.0100;"
```

- More details on MFT simulation and reconstruction in [this alice-talk post](https://alice-talk.web.cern.ch/t/instructions-to-run-mft-simulations-wiki-post/470/21).

- Script [`sim_challenge.sh`](https://github.com/AliceO2Group/AliceO2/blob/dev/prodtests/sim_challenge.sh) runs all the steps from ALICE detector simulation to AOD producer

- DPG script to generate events in pilot beam conditions and injected particles: [run_pp_testbeam_ccbarfilter.sh](https://github.com/AliceO2Group/O2DPG/blob/master/MC/run/PWGHF/run_pp_testbeam_ccbarfilter.sh)

:::info
**Task 1.2:** Run the simulation, digitization and reconstruction steps above.
:::

### Reconstruction from MFT simulation (with MC labels)

[`MFTRecoCheckerMC.C`](https://gist.github.com/rpezzi/f29936fdd6d29bf1c2f4445bf3ceb05c) is a simple macro that loads MFT Tracks, MC labels and the corresponding Kinematics information.


:::info
**Task 1.3:** Understand the structure of `MFTRecoCheckerMC.c` and run it.

```
wget -c https://gist.github.com/rpezzi/f29936fdd6d29bf1c2f4445bf3ceb05c/raw/86c1439606da8480944b8ab86ad4ddf7a13bf191/MFTRecoCheckerMC.C
root.exe -q -b MFTRecoCheckerMC.C
```
:::

### Reconstruction from MFT clusters (real data)

To produce MFT tracks starting from MFT clusters we use the a workflow defined by `MFT cluster reader workflow -> MFT reconstruction workflow`.

The partial data for pilot beam run 505637 is available at https://cernbox.cern.ch/index.php/s/u9cwx0oyIEM08e2.

On a directory containing these files
```
mftclusters.root
MFTdictionary.bin
o2simdigitizerworkflow_configuration.ini
o2sim_geometry.root
o2sim_grp.root
```
Run this workflow

```
o2-mft-cluster-reader-workflow | o2-mft-reco-workflow --clusters-from-upstream --disable-mc --mft-cluster-writer "--outfile /dev/null"
```




Workflows used for reconstruction of pilot beam data from TFs and CTFs are available [here](https://codimd.web.cern.ch/s/SIhszh3Q8#). More tips on how to automatize the process on the [QC MFT Hackathon notes](https://codimd.web.cern.ch/s/TaXZXxgJ_#).

#### Basic assessment of the data

* `MFTCTFRecoChecker_pilotBeam.C`: Basic stats from cluster and tracks files: 
 at https://gist.github.com/rpezzi/3bcfe11a89eacda448ed54ab5f1c6605
  * `root.exe -b -q 'MFTRecoChecker_pilotBeam.C("runNumber")'`

MFT Hackathon - Session 5
=========================

## Forward matching Workflows

Some MC productions are available at  https://cernbox.cern.ch/index.php/s/u9cwx0oyIEM08e2

- Productions on folder `runPromptCharmonia_fwdy_pp.sh` where generated with [this script](https://github.com/AliceO2Group/O2DPG/blob/master/MC/run/PWGDQ/runPromptCharmonia_fwdy_pp.sh).
    - Example For 5000 events: 
```shell=bash
# Build dependencies on local alice folder
aliBuild build EVTGEN --defaults o2
aliBuild build AEGIS --defaults o2
aliBuild build O2DPG --defaults o2

# Enter O2 O2DPG EVTGEN and AEGIS environments
alienv enter O2/latest O2DPG/latest-master-o2 EVTGEN/latest-o2 AEGIS/latest-o2
# Run script runPromptCharmonia_fwdy_pp.sh on a convient folder
NSIGEVENTS=5000 NBKGEVENTS=5000 ${O2DPG_ROOT}/MC/run/PWGDQ/runPromptCharmonia_fwdy_pp.sh
```


### MCH and MID reconstruction workflows

* MCH reconstruction workflow:
    * `o2-mch-reco-workflow`
    * produces `mchtracks.root`
* MID reconstruction workflow:
    * `o2-mid-digits-reader-workflow | o2-mid-reco-workflow`
    * produces `mid-reco.root`

See also [`sim_challenge.sh`](https://github.com/AliceO2Group/AliceO2/blob/dev/prodtests/sim_challenge.sh).

### MCH-MID matching

Starting from mch and mid tracks, produces `muontracks.root` storing matching information only, as defined in [TrackMCHMID.h](https://github.com/AliceO2Group/AliceO2/blob/dev/DataFormats/Reconstruction/include/ReconstructionDataFormats/TrackMCHMID.h).

```
o2-mch-tracks-reader-workflow | o2-mid-tracks-reader-workflow | o2-muon-tracks-matcher-workflow | o2-muon-tracks-writer-workflow
```

### MFT-MCH-MID matching

`o2-globalfwd-matcher-workflow` runs MFT-MCH matching and can also process the MCH-MID matching information stored on `muontracks.root` to identify the track as a GlobalMuonTrack and further constrain the track time.

Global forward matching is configured at two levels:

**1. Workflow**: [globalfwd-matcher-workflow.cxx](https://github.com/AliceO2Group/AliceO2/blob/dev/Detectors/GlobalTrackingWorkflow/src/globalfwd-matcher-workflow.cxx)

![](https://codimd.web.cern.ch/uploads/upload_08b7df55f121b2ff27024ba9886a7698.png)

By default `o2-globalfwd-matcher-workflow` produces `globalfwdtracks.root` which contains all the forward tracks with matching information and track parameters. These are the tracks of interest for track reconstruction and physics. Optionally, file `mftmchmatches.root` storing only the matching information of forward tracks can be generated by using option `--enable-match-output`.


**2. Matcher configKeyValues** [MatchGlobalFwdParam.h](https://github.com/AliceO2Group/AliceO2/blob/dev/Detectors/GlobalTracking/include/GlobalTracking/MatchGlobalFwdParam.h)
![](https://codimd.web.cern.ch/uploads/upload_c08b9870646c2b2dd1949d1cf24e0e03.png)

Forward matching can be configured by `configKeyValues` as
```
o2-globalfwd-matcher-workflow --configKeyValues "FwdMatching.matchFcn=matchALL;FwdMatching.useMIDMatch=true"
```

:::info
**Note:** External cut function support pending [PR#7831](https://github.com/AliceO2Group/AliceO2/pull/7831). 
:::

Configurations of particular interest for matching studies are

- `FwdMatching.matchFcn=matchExternal`: Uses a matching function as defined in parameter `extMatchFuncFile` such as [this example: `FwdMatchFunc.C`](https://gist.github.com/rpezzi/06442d29b0bfc7618b4480d1ce6925d3)
- `FwdMatching.cutFcn=cutExternal`: defines external cut function - same file as above
- `FwdMatching.saveAll=true` to store all tested MFT-MCH pair combinations.
- `FwdMatching.MCMatching=true`: runs MC-based MFT-MCH track matching, producing 100% true tracks.

:::warning
**Note:** Options `--enable-match-output` and `FwdMatching.matchFcn=matchUpstream` are of limited interest with the advent of external cut and matching functions defined in external macros. These macros can be included in CCDB allowing the use of alternative matching methods in centralized reconstruction and MC productions.
:::

Matching functions (cut and matching) defined at the constructor of [`MatchGlobalFwd::MatchGlobalFwd()`](https://github.com/AliceO2Group/AliceO2/blob/dev/Detectors/GlobalTracking/src/MatchGlobalFwd.cxx).

#### Sample workflows

These workflows can be executed on productions on folder tf1 inside productions on `runPromptCharmonia_fwdy_pp.sh` from cernbox.


```
# Enter O2 environment
alienv enter O2/latest 

# Create necessary symlinks
cd tf1
ln -s ../bkg_geometry.root bkg_geometry.root
ln -s ../bkg_grp.root bkg_grp.root  
ln -s ../bkg_Kine.root bkg_Kine.root 
ln -s ../bkg_MCHeader.root bkg_MCHeader.root  
ln -s ../bkg_geometry.root o2sim_geometry.root  
ln -s ../bkg_grp.root o2sim_grp.root 
ln -s ../MFTdictionary.bin

# Run globalfwd matching worflows on tf1 folder
```

These workflows will reproduce/rewrite `globalfwdtracks.root`.

##### Chi²-based MFT-MCH matching (matchAll)
```
o2-globalfwd-matcher-workflow  > defaultmatch.log 
```


##### Chi²-based MFT-MCH-MID matching (matchAll)
```
o2-globalfwd-matcher-workflow  --configKeyValues "FwdMatching.useMIDMatch=true" > MFTMCHMIDMatch.log 
```


##### MC label-based matching
```
o2-globalfwd-matcher-workflow --configKeyValues "FwdMatching.MCMatching=true" > mcmatch.log  ## MC-based matching
```

##### Matching as defined in external functions

```
o2-globalfwd-matcher-workflow --enable-match-output --configKeyValues "FwdMatching.matchFcn=matchExternal;" > matchingExternalMatchingFcn.log
```

#### Basic assessment of GlobalFwdMatching

[GlobalMuonChecks.C](https://gist.github.com/rpezzi/ee5c4effd046eedd7de545ef406b0774) is adapted to load Kinematics from two files for signal and background events. Compatible with files on folder `data/runPromptCharmonia_fwdy_pp.sh` from [CERNBox](https://cernbox.cern.ch/index.php/s/u9cwx0oyIEM08e2)

```
# Run the macro in compiled mode, on the directory containing the globalfwdtracks.root and Kinematic files
root.exe GlobalMuonChecks.C+ -b -q

# Output: GlobalMuonChecks.root
```


:::info
**Challenge 1:** How to use a trained machine learning network on O2-compatible macro like [`FwdMatchFunc.C`](https://gist.github.com/rpezzi/06442d29b0bfc7618b4480d1ce6925d3)?
- Rita
- Ren
- Motomi
:::


:::info
**Challenge 2:** How to interface python ML libraries to profit directly from the root files for training ML networks?
- Andry (newbie on usage of Python ML packages)
- Lucas
:::

(Very rough) summary at this [link](https://codimd.web.cern.ch/s/mjnNm1Yig) for both challenges.

:::info
**Task 5.1:** Gather a list of software implementations of quantities of relevance for performance studies to be included in [GlobalMuonChecks.C](https://gist.github.com/rpezzi/ee5c4effd046eedd7de545ef406b0774).
- Sarah P-H
- Maurice
- Sarah Herrmann
- Robin
- Antonio


https://twiki.cern.ch/twiki/pub/ALICE/MftPhysics/MFT_and_Global_Muon_Definitions_V_1.0_-_April_13th_2021.pdf

MFT Standalone:

- $N^{MFT}_{Trackable}$ → Number of MFT trackables. Tracks with clusters in at least 4 MFT disks; reference is MC.
- $N^{MFT}_{Rec}$ → Number of Reconstructed MFT tracks. (MC and Real data)
    - $N^{MFT}_{Rec} = N^{MFT}_{True} + N^{MFT}_{Fake}$ 
- $N^{MFT}_{True}$ → Number of Reconstructed MFT tracks with correct MC labels (> 80% of clusters from same MC label).
    - $N^{MFT}_{True} = \epsilon^{MFT}_{True} * N^{MFT}_{Trackable}$
- $N^{MFT}_{Fake}$ -> Number of Reconstructed MFT tracks with incorrect MC label (< 80% of clusters from same MC label)
    - $N^{MFT}_{Fake} = \bar\epsilon^{MFT}_{Fake} * N^{MFT}_{Trackable}$
- $N^{MFT}_{Gen}$ → Number of particles generated within the MFT acceptance. (Attention to generation parameters, specific usages).

- $A^{MFT} = N^{MFT}_{Trackable} / N^{MFT}_{Gen}$ → MFT Acceptance
- $\epsilon^{MFT} = N^{MFT}_{Rec} / N^{MFT}_{Trackable}$ → MFT tracking efficiency
    - $\epsilon^{MFT} = \epsilon^{MFT}_{True} + \bar\epsilon^{MFT}_{Fake}$
- $\epsilon_{MFT}*A_{MFT} = N^{MFT}_{Rec} / N^{MFT}_{Gen}$
- Purity of MFT tracks: $P_{MFT} = N^{MFT}_{True} / N^{MFT}_{Rec}$



>For MFT standalone, code made by Sarah Herrmann available [here](https://github.com/sarahherrmann/MFT-analysis/blob/main/StudyMFTTracks.C)

Quality of track fitting
- Resolutions and pulls (vs eta, pt)
  

**Global Muon Tracking**

A $GM$ Track refers to a Global Muon Track


- $N^{GM}_{Trackable}$ → Number of trackable GM Tracks. MC Tracks that are trackable by both MCH and MFT.
- $N^{GM}_{Pairable}$ → Number of pairable global muon tracks: tracks that have been correctly reconstructed by both MFT and MCH and comes from the same generated track (the MC track is part of both $N^{MCH}_{True}$ and the MFT track is part of $N^{MFT}_{True}$, )
    - Preferable quantity to use on performance assessments of the matching algorithm, namely the efficiency (not the purity); filters detector effects: efficiency, acceptance, and standalone tracking. 
- $N^{GM}_{Rec}$ → Number of reconstructed global muon tracks.
    -  $N^{GM}_{Rec} = N^{GM}_{True} + N^{GM}_{Fake}$
- $N^{GM}_{True}$→ Number of GM tracks with correct MCH-MFT pair association. I.E. MCH and MFT have true and identical MC labels. $N^{GM}_{True} \leq N^{GM}_{Pairable}$
    - $N^{GM}_{true} = \epsilon^{GM}_{true} * N^{GM}_{Pairable}$
- $N^{GM}_{Fake}$→ Number of GM tracks with wrong MCH-MFT pair association. I.E. MCH and MFT have true but different MC labels.
    - $N^{GM}_{fake} = \bar\epsilon^{GM}_{fake} * N^{GM}_{Pairable}$
- $N^{GM}_{Close}$→ Number of GM tracks with the true MFT associated track in the search window. Merely indicates if the true MFT associated track was tested, regardless of being selected as the best pair.
- $N^{MCH}_{Dangling}$→Number of MCH tracks not associated to any MFT track; no corresponding GM track



- $\epsilon^{GM}_{pairing} = \frac{N^{GM}_{Rec}} {N^{GM}_{Pairable}}$ → Pairing efficiency:
- $\epsilon^{GM}_{true} = \frac{N^{GM}_{True}} {N^{GM}_{Pairable}}$ → True pairing efficiency. From 0 to 1
- $\bar\epsilon^{GM}_{Fake} = \frac{N^{GM}_{Fake}} {N^{GM}_{Pairable}}$→ Fake pairing efficiency. From 0 to ?
- $\epsilon^{GM}_{pairing} = \epsilon^{GM}_{True} + \bar\epsilon^{GM}_{Fake}$



- Alternative pairing efficiency: no acceptance consideration. Relevant for data size and reconstruction monitoring (QC)
    - $\epsilon^{MCH/MFT}_{pairing} = \frac{N^{GM}_{Rec}} {N^{MCH}_{Rec}}$ 



- $P_{pairing}^{GM} = \frac{N^{GM}_{True}} {N^{GM}_{Rec}}$ → Global pairing purity. From 0 to 1
- $P_{pairing}^{GM} = \frac{\epsilon^{GM}_{true}} {\epsilon^{GM}_{true} + \bar\epsilon^{GM}_{fake}}$


- $\epsilon_{close} = \frac{N^{GM}_{Close}} {N^{GM}_{Pairable}}$
:::

:::danger
**Problem noticed:** more global forward tracks than MCH tracks


```
Loaded 35526 MCH Tracks in 2829 ROF
Writing 36088 GlobalForward Tracks
```

-> MCH track reconstructed twice (boundary problems)
We should take the one with the best matching $\chi^2$

Fixed by https://github.com/AliceO2Group/AliceO2/pull/7875
:::

## Plans for MFT assessment

Create O2 Workflows to produce evaluation objects (QC workflows?).

```
MFT reconstruction workflow -> MFT Reco Assessment Workflow
```

```
Global Fwd matching workflow -> Matching assessment workflow
```

Task 5.1 goal: gather elements to be included in these workflows. Several methods have been implemented by several of us.