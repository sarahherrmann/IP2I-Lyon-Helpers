
ssh sherrman@lxplus.cern.ch 
Pwd: le même que pour le cern

Enter O2:

source /cvmfs/alice-nightlies.cern.ch/bin/alienv enter VO_ALICE@O2sim::v20221212-1

You can run normally any macro under root (for example the finalizeAnalysis.C macro)

## Copy files from your local computer to lxplus

In my local computer (outside lxplus) do
scp MFTAssessment.root sherrman@lxplus.cern.ch:/afs/cern.ch/user/s/sherrman/.

## Copy files from lxplus to your local computer

scp sherrman@lxplus.cern.ch:/afs/cern.ch/user/s/sherrman/DCAres/MFTAssessmentFinalized.root .


update 06/2024

source /cvmfs/alice.cern.ch/bin/alienv enter VO_ALICE@AliPhysics::slc9-vAN-20240216_O2-1