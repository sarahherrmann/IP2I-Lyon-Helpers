# How to access the information of runs
## In data
When at CERN simply have a look at the website ali-bookkeeping.cern.ch.

The website ali-bookkeeping.cern.ch is not accessible when not on the CERN site, so to access it one needs to log in a CERN desktop. 
Install Remote Desktop Client
[https://devices.docs.cern.ch/pss/RemoteDesktopClientMac/](https://devices.docs.cern.ch/pss/RemoteDesktopClientMac/)

Click on the pc, connect with cern credentials and :
sherrman@cern.ch
Go to google chrome, then
[ali-bookkeeping](https://ali-bookkeeping.cern.ch/?page=run-detail&id=27139&panel=logs)

<img width="1278" alt="Capture d’écran 2023-02-09 à 16 34 59" src="https://github.com/user-attachments/assets/913cfce3-0e26-41db-80c7-d2b597e0dde8">

You can access many run informations, the fill number, the duration, the date the run was taken etc...
Other pieces of information like the MFT ROF length are also accessible, here is how:
Here for this run you can click on the log created automatically (the one authored by Anonymous) 

<img width="1278" alt="Capture d’écran 2023-02-09 à 16 36 38" src="https://github.com/user-attachments/assets/9b1980d0-f166-4e24-9e8f-1cc3c8a35874">
If you are looking for the MFT ROF, you can go and look for **MFT_STROBE** , for instance MFT_STROBE=198 (gives you the length in BC)


## In MC : Access to MC production information in AliMonitor 
How to obtain information on a specific MC production ? 
Here we use the example of the MC production LHC21k6. 

Go to https://alimonitor.cern.ch/ then production list $\rightarrow$ MC

Search for LHC21k6 (command+F)

Click on LHC21k6, it redirects you to the production table of this MC production, with all of the runs
<img width="962" alt="Capture d’écran 2022-12-23 à 11 58 22" src="https://github.com/user-attachments/assets/edbabc6b-d5cc-4906-a5dd-3b5cc32653f0">


Then click on the PID of the run you want to know information about, and then click on JDL
<img width="962" alt="Capture d’écran 2022-12-23 à 11 58 44" src="https://github.com/user-attachments/assets/185c5357-790a-4bae-a04f-a68e273c48fc">



The command line inside **SplitArguments** is the command line that was used for the production, you can then find the .sh file and look up the parameters

Example of the JDL for the latest LHC21k6 run
<img width="962" alt="Capture d’écran 2022-12-23 à 12 03 11" src="https://github.com/user-attachments/assets/9576e7cd-3e74-43f5-a05e-943a73eb4545">
