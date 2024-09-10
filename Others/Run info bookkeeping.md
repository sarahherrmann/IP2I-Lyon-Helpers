Install Remote Desktop Client
[https://devices.docs.cern.ch/pss/RemoteDesktopClientMac/](https://devices.docs.cern.ch/pss/RemoteDesktopClientMac/)

Click on the pc, connect with cern credentials and :
sherrman@cern.ch
Go to google chrome, then ali-bookkeeping
https://ali-bookkeeping.cern.ch/?page=run-detail&id=27139&panel=logs


![[Capture d’écran 2023-02-09 à 16.34.59.png]]
Here for this run you can click on the log created automatically (the one authored by Anonymous) 

![[Capture d’écran 2023-02-09 à 16.36.38.png]]
If you are looking for the MFT ROF, you can go and look for **MFT_STROBE** , for instance MFT_STROBE=198 (gives you the length in BC)


## Access to production informations in AliMonitor 

Go to https://alimonitor.cern.ch/ then production list $\rightarrow$ MC
Search for LHC21k6 (command+F)
Click on LHC21k6, it redirects you to the production table of this MC production, with all of the runs
![[Capture d’écran 2022-12-23 à 11.58.22.png]]

Then click on the PID of the run you want to know information about, and then click on JDL
![[Capture d’écran 2022-12-23 à 11.58.44.png]]
The command line inside **SplitArguments** is the command line that was used for the production, you can then find the .sh file and look up the parameters

Example of the JDL for the latest LHC21k6 run
![[Capture d’écran 2022-12-23 à 12.03.11.png]]