## Visualising geometry

For instance launch the following command line in the O2 environment:

o2-sim -n 1 -m ITS --configKeyValues "ITSBase.buildCYSSAssembly=0;ITSBase.buildEndWheels=0"

It then creates the file o2sim_geometry.root

You can open it in a TBrowser in root, and you can try to draw part of the geometry by going in the Master Volume folder (or blank folder here, there is a bug)
You can then right click on part of the folder to see the name, or Draw the volume assembly inside the folders by clicking on Draw
<img width="781" alt="Browser-Volume" src="https://github.com/user-attachments/assets/411107be-87b0-4b9f-82a3-a9e19b1eb9f8">


In the discussion panel, type "ogl"
<img width="288" alt="Capture d’écran 2023-01-09 à 14 58 09" src="https://github.com/user-attachments/assets/3d0a8dd4-de45-4798-8897-dbf34c233af4">


And then OK
You should be able to visualise the geometry part that you have drawn, you can zoom in and out and rotate

You should then have something like this:

<img width="777" alt="Capture d’écran 2023-01-09 à 15 05 37" src="https://github.com/user-attachments/assets/64530d25-eb87-42c3-b823-3071bf3c51fb">

If some parts of the detector are invisible and should be visible, it may be because the visibility level is too low, you can change the visibility level with`gGeoManager->SetVisLevel(5)`
With 5 or more it should resolve the issue


To see the FT0 too:

o2-sim -m PIPE ITS MFT FT0 -e TGeant3 -g boxgen -n 1 --configKeyValues 'BoxGun.pdg=13 ; BoxGun.eta[0]=-3.0 ; BoxGun.eta[1]=-3.0; BoxGun.number=1'
Easier:
`o2-sim -n 1 -m MFT FT0`
