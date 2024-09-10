# How to find the MFT ROF length for a run ?

## 1. Go to https://ali-bookkeeping.cern.ch/?page=home

If you are at CERN, you should be able to access it right away.
If you are not, you should use **Microsoft Remote Desktop** to connect to a CERN computer, and then you can access the ali-bookkeeping website

## 2. Go to the `Runs` section 

![](https://codimd.web.cern.ch/uploads/upload_fdc705565bf1b72c9b79ca27e703d22b.png)
## 3. Using the `Filters` button, you can select one LHC period

For example here I selected the period LH23zzf

![](https://codimd.web.cern.ch/uploads/upload_2c2e89dc6f8e602c9a077cc38e22b178.png)

There are several runs corresponding to this period, and one of them (Run 544013) is labeled "PHYSICS", and has a "good" quality. We will focus on this run and we will try to get the MFT ROF length used in it.

## 4. Click on the run number and scroll down to see the automatic log entry

![](https://codimd.web.cern.ch/uploads/upload_7810a7f00e1f06d5c4c39622e0de1032.png)
![](https://codimd.web.cern.ch/uploads/upload_498b63c8bbfbc0bc86bb53e58ddc8ba1.png)

## 5. Open the automatic log entry

The automatic log entry is `Log for run 546969 and environment 2kmZNHjPCaG`. If I click `More` I am able to see the configuration used during the run, and after scrolling down a little I can see "MFT_STROBE=594"




![](https://codimd.web.cern.ch/uploads/upload_61a22ecd19f7980bd998f7d90de8f1f9.png)

It means that for this run, the MFT ROF length (also called MFT strobe) is 594 BC.



## Knowing the IR of a run
Go to the MFT operation CodiMD logbook page
https://alice-mft-operation.docs.cern.ch/MFT%20Operation/codim/

Knowing the date of the run (05/10/2023) one can know which week it corresponds to. Here it is week 40 so https://codimd.web.cern.ch/i8nrFlHYQm67tWGR4x-aHw#PHYSICS and then searching for the run number 544013 we can find the IR, the MFT shifter always writes the IR for each Physics run. 
We find that for run 544013 the IR is ~7kHz.


## Conclusion

With a MFT ROF length of 594 BC = 14.8 $\mu$s and an IR of 7kHz, it means one collision each 142 $\mu$s on average. One MFT track is then time-compatible to 14.8/142 = 0.1 collisions on average.

The number of ambiguous MFT tracks should then be really low.
